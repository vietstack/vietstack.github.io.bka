---
layout: post
title: [How-To] Remote debugging OpenStack Neutron
date: 2016-04-12 01:17
author: vnstack
comments: true
categories: [Devstack, devstack, github, OpenStack, Python, Uncategorized, vietstack]
---
Following is the contributed tutorial from @namnh, a member of our Vietnam OpenStack Community. Please ping us if you have any questions.

This tutorial was created based on the last video tutorial "How to setup remote debugging with OpenStack Nova". Please see this video for more details:

https://www.youtube.com/watch?v=Z0PmKyeZjjA

And here is the tutorial for neutron:

<blockquote>In this archive, I will guide configuration to debug Neutron project by Pycharm-Pro5. And configuration with other projects are similar to Neutron project.

<strong>Step 1: Setup environment</strong>

We need two machines and topology as below:

<img class="alignnone  wp-image-735" src="https://vietstack.files.wordpress.com/2016/04/untitled.png" alt="Untitled" width="469" height="273" />

- Machine 1 (called "Pycharm-Pro5"): Installing Pycharm-Pro5.

- Machine 2 (called "VM"): Installing Openstack-AIO by Devstack.

<em><strong>Note:</strong></em>

- We will use Devstack to install Openstack-AIO. This is [local.conf](https://github.com/NguyenHoaiNam/Debuging_Openstack_with_Pycharm-Pro5/blob/master/local.conf) file.

<strong>Step 2: Configuration Pycharm on Pycharm-Pro5</strong>

<strong>Step 2.1: Setup Deverlopment by clicking Tools --&gt; Deployment --&gt; Configuration then click "+" to create a "Devstack".</strong>

<img class="alignnone  wp-image-736" src="https://vietstack.files.wordpress.com/2016/04/untitled1.png?w=300" alt="Untitled" width="604" height="516" />

(1): Type of connect to VM. We will choose SFTP.

(2): The address of VM.

(3): The account of VM.

(4): The password of VM.

<em><strong>Note</strong></em>: In this step, we should check to connect to VM by choice "Test SFTP connection..."

Then we jump "Mappings" tab. To configure mapping between Pycharm-Pro5 and VM is like image:

<img class="alignnone  wp-image-737" src="https://vietstack.files.wordpress.com/2016/04/untitled2.png" alt="Untitled" width="586" height="500" />

(5): This is local path on Pycharm-Pro5.

(6): This is path on VM.

Click OK.

<strong>Step 2.2: Setup project by clicking File --&gt; Settings then choose "Project: neutron" (in this case, I am setting debug with neutron project, with other projects are similar.)</strong>

Choice "Project Interpreter" to add an interpreter remote by choice "Add remote":

<img class="alignnone  wp-image-747" src="https://vietstack.files.wordpress.com/2016/04/687474703a2f2f692e696d6775722e636f6d2f6a7864374e54382e706e67.png" alt="687474703a2f2f692e696d6775722e636f6d2f6a7864374e54382e706e67" width="585" height="372" />

After choice "Add remote", we have image:

<img class="alignnone  wp-image-738" src="https://vietstack.files.wordpress.com/2016/04/untitled3.png" alt="Untitled" width="519" height="319" />

Change to "Deployment configuration" then click "ssh://stack@10.10.10.30:22" to connect to VM. In this time, we will receive message "Successfully ...."

<img class="alignnone  wp-image-739" src="https://vietstack.files.wordpress.com/2016/04/untitled4.png" alt="Untitled" width="522" height="321" />

We need to wait for a few time so that Pycharm download packet from VM.

<strong>Step 2.3: Configuration Python Debugger. In "Settings", we choose "Build, Execution, Deployment" --&gt; "Python Debugger" then select "Gevent compatible"</strong>

<img class="alignnone  wp-image-740" src="https://vietstack.files.wordpress.com/2016/04/untitled5.png" alt="Untitled" width="544" height="394" />

After done, we click OK.

<strong>Step 3: Configuration debug with Neutron prject</strong>

In this step, I will configure debug with neutron-server. With other component or other project, they are similar.

<strong>Step 3.1: Create tab debug by clicking Run --&gt; Edit Configurations then create a neutron-server debug like that:</strong>

<img class="alignnone  wp-image-741" src="https://vietstack.files.wordpress.com/2016/04/untitled6.png" alt="Untitled" width="565" height="368" />

<strong>Step 3.2: Configuration API-worker on VM</strong>

Because Pycharm-Pro5 debug only one process so we have to configure on VM so that neutron-server run only one process.

Edit configure file /etc/neutron/neutron.conf. At first line we change api_workers = -1 (note that in nova api configuration, the config for only one process api worker is osapi_worker = 1. The nova API daemon does not accept negative value).

<strong>Step 3.3: After changing configuration file, turn off neutron-server process on VM. (Hint: you can use rejoin-stack to do that).</strong>

<strong>Step 4: Start debugging</strong>

After finishing all steps. We can start debug by click "debug" button. Have fun !!!

Optional: If you want to locally debugging your openstack neutron, please apply monkey patch for eventlet for collecting debug information from openstack process. In neutron, edit file __init__.py in neutron/common/eventlet_utils.py and modify line 32 from:
<pre>eventlet.monkey_patch()</pre>
to:
<pre>eventlet.monkey_patch(os=False, thread=False)</pre>
&nbsp;

Happy debugging !</blockquote>

<a href="https://github.com/vietstacker/Debuging_Openstack_with_Pycharm-Pro5" target="_blank">Click here to view this tutorial source on GitHub</a>

04/2016

VietStack team.
