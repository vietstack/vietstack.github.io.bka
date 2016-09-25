---
title: "[OpenStack][Monitoring][Zabbix][Ceilometer] Some notes when designing monitoring solution for OpenStack cloud"
comments: true
categories: 
  - Tech
tags: 
  - Chia sẻ
  - Kinh nghiệm
  - VietStack
  - Zabbix
  - Monitoring
  - Ceilometer
---
When i did my research for monitoring solution of physical nodes of our OpenStack cloud enviroment, Zabbix is one of my favorite. Besides, some one told me about using Ceilometer for monitoring purpose but definitely, it is a wrong idea. I noted some notes below for further understanding about designing both of them:

<h1>Zabbix</h1>

<h2>I. Zabbix features in cluster:</h2>

<ul>
    <li class="western">Robustness</li>
</ul>

Zabbix works well and if Zabbix gets wrong, it is mostly due to other dependencis such as Mysql. If there are some errors that make Mysql not working properly, Zabbix will fail. One of the current problem of Mysql is about replication of database. The current Galera cluster with multiple Active Mysql servers running has a problem race condition when writing to databases.

<ul>
    <li>Graduality of getting metrics</li>
</ul>

The most popular graduality value is 60 seconds. However, it can decrease down to 1 second to fit requirements of detecting state of hosts.

<ul>
    <li>Fail-over of Zabbix cluster</li>
</ul>

Zabbix cluster works in active/passive. It solves the problem of fail over quite well. When each Zabbix server is down, another one is popped up. Although Zabbix still depends on the fail-over capability of Galera cluster of Mysql but the fail-over capability of Zabbix cluster itself is good

<ul>
    <li>Density of Zabbix Agent – Scalability of Agents that a Zabbix server can serve</li>
</ul>

<span lang="en-US">Density is not a challenge of Zabbix Agents. A Zabbix server can serve multiple agents running on nodes. Zabbix agent uses Push mechanism to send data to Zabbix server and the communication mechanism between server and agent of Zabbix ensures flow of pushing.</span>

<ul>
    <li><span lang="en-US">Reboot detection of node?</span></li>
</ul>

<span lang="en-US">We can check the uptime of a server for reboot detection. It could be fulfilled by using Zabbix Agent.</span>

<ul>
    <li><span lang="en-US">How does Zabbix deal with state transition?</span></li>
</ul>

When the compute node is up, we can set Zabbix Agent to have checking of state of compute host where it is running on.

When both of the compute node is down by some reasons or network problem that ruins connection between Zabbix server and agent, we should have to find another way since there is no connection to Zabbix Agent. Otherwise, we can query Nova database (if Nova has no impact from these situations) to get the state of node. Besides, w<span lang="en-US">e can create a new solution by running the external_scripts in Zabbix Server to checking state of compute host (e.g. triggering ssh checking): </span><span lang="zxx"><a href="https://www.zabbix.com/documentation/2.0/manual/config/items/itemtypes/external"><span lang="en-US">https://www.zabbix.com/documentation/2.0/manual/config/items/itemtypes/external</span></a></span>

<h2>II. Solution for monitoring solution collaborating with other services:</h2>

<ol>
<li>It should have some thresholds that Zabbix server relies on and triggers appropriate actions if the return values from Zabbix Agent go beyond the thresholds.</p></li>
<li><p>It should set Zabbix server some templates that force Zabbix server to get metrics from system based on these templates.<span lang="en-US"> Templates will define the structure of metrics that Zabbix server should get and it stores those metrics to database.</span></p></li>
<li><p>When Zabbix server realizes that some thresholds are caught, it will get information from system based on the template that is defined for the threshold and call ZabbixEndpoint service – actually it is a webserver – through REST API. Later on, the ZabbixEndpoint service will take the information get from Zabbix server and trigger the consequence actions (e.g. sending alarms).</p></li>
</ol>

<p><span lang="en-US">4. ZabbixEndpoint service can run in both Active/Active or Active/Passive on three CiCs, under the control of pace_maker/corosync.</span>

&nbsp;

<h1>Ceilometer</h1>

<ul>
    <li>Robustness:</li>
</ul>

Since it strictly depends on Mongodb, Rabbitmq but both of them seem are not reliable. Since Mongodb is NoSql, document-database solution and actually a cache without backing store (for example: Mysql, Sqlalchemy) behind it, then it becomes flat-out inconsistent. One of the most severe scenario is cache invalidation. Otherwise, it is easy to let Mongodb fill up the disk when creating/storing new item in replication. Besides, the inconsistency of Rabbitmq is reported in CEE when it is used in ACTIVE/PASSIVE.

<ul>
    <li>Failover ability<span lang="en-US">:</span></li>
</ul>

The ceilometer-agent is running ACTIVE/PASSIVE under the management of pacemaker. It deals with failover problem quite well.

<ul>
    <li><span lang="en-US">Graduality:</span></li>
</ul>

<span lang="en-US">The graduality of ceilometer (time for periodic metrics achievement) is not fine enough due to purpose of fitting detection requirements when it should be as fast as possible to detect the availability of a node.</span>

<ul>
    <li><span lang="en-US">Reboot detection of nodes:</span></li>
</ul>

<span lang="en-US">No</span>

<p class="western"><span style="color:#ff0000;"><u>NOTE</u></span><span style="color:#ff0000;">:</span></p>

<p class="western">- Ceilometer was initially created for billing, not for monitoring then when we use ceilometer, we can get the metrics about virtual machines, not about compute hosts. Therefore ceilometer does not seem to be a prospective and potential candidate.</p>

<p class="western">13/09/2016</p>

<p class="western">VietStack team</p>
