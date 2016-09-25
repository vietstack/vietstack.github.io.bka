---
layout: post
title: Introduction of Metadata service in Openstack
date: 2014-09-27 12:09
author: vnstack
comments: true
categories: [Bài dịch - Tài liệu, Cloud Computing]
---
<strong>Problem Abstract:</strong>

Suppose that if any client in an Openstack instance wants to know the own information like:

- What is my public IP/hostname?

- What is the ssh public key that i got?

- Give me a random seed

- Give me the metadata that tenant provides at the boot time: nova boot …. –user-data —-

Those are reasons for the existing of metadata service. This service will handle such above requests of client.

<strong>Overview of metadata service:</strong>

<a href="https://tuantuluong.files.wordpress.com/2014/09/neutron-metadata-dhcp-agent.png"><img class="alignnone size-medium wp-image-108" src="http://tuantuluong.files.wordpress.com/2014/09/neutron-metadata-dhcp-agent.png?w=204&amp;h=300" alt="Neutron-metadata-dhcp-agent" width="204" height="300" /></a>

Pic 1. Metadata service operation diagram with neutron-dhcp-agent (Source: myfriend)

<a href="https://tuantuluong.files.wordpress.com/2014/09/neutron-metadata-l3-agent.png"><img class="alignnone size-medium wp-image-109" src="http://tuantuluong.files.wordpress.com/2014/09/neutron-metadata-l3-agent.png?w=184&amp;h=300" alt="Neutron-metadata-l3-agent" width="184" height="300" /></a>

Pic 2.  Metadata service operation diagram with neutron-L3-agent (Source: myfriend)

Theoretically, Openstack provides metadata service through nova-api on controller node (up-to-now), by default listening at: nova-api-IP:8775. This IP is specified to metadata service differently to other nova-api-IPs. Neutron proxies the metadata requests of client in Openstack instance to nova metadata service. Inside an Openstack instance, the  HTTP client sends the requests to a link-local address (169.254.169.254:80) which is allocated for metadata service without any authenticating or identifying. From now on, it is the work of neutron.

Inside neutron node, because the requests sent to it without neither authenticating nor identifying, neutron has to do something to identify the source of those requests. There are two elements responsible for that. The first one is Neutron-metadata-proxy laying in Neutron dhcp namespace, another one is neutron-metadata-agent. They are running on the same host and connected by unix socket.

The question is came out that why do we need 2 metadata components for that? Because neutron-metadata-proxy laying in neutron dhcp (Pic.1) or router (Pic.2) namespace can see the IP of the source that sends the requests so that it can handle the overlapping tenant subnets in multiple tenant network by knowing the tenant network where the requests come from. For example, you have 2 tenant network. What if there are two instances which have the same IP address i.e 10.0.0.1 (because they are isolated network then their IPs are independent, that is reason they can be the same) send request? The namespaces in Neutron will handle this. Each tenant network has the own namespace in neutron so that neutron-metadata-proxy knows exactly what the source of request is. And then it adds HTTP headers, Neutron-Network-ID… of the source and passes to neutron-metadata-agent. Neutron-metadata-agent queries public Openstack APIs for the source instances UUID and it adds UUID as a HTTP header.

Metadata service can operate in separately independent methods: via DHCP-agent (Pic.1) or L3-agent (Pic.2). Whatever method it works, the mechanism is the same, only the difference in configuration.

<strong>Config </strong>

These following configs are the same whether using dhcp or l3-agent:

<strong><em>/etc/nova/nova.conf:</em></strong>

enabled_apis = osapi_compute,metadata

service_neutron_metadata_proxy = True

neutron_metadata_proxy_shared_secret = SECRET_KEY

<em><strong>/neutron/metadata_agent.ini:</strong></em>

[DEFAULT]
auth_url = <a href="http://controller:5000/v2.0" rel="nofollow">http://controller:5000/v2.0</a>
auth_region = RegionOne
admin_tenant_name = service
admin_user = neutron
admin_password = admin_pass
nova_metadata_ip = controller
metadata_proxy_shared_secret = SECRETE_KEY

<strong><em>1. <strong>For usi</strong>ng qdhcp namespace (dhcp agent):</em></strong>

<em>/neutron/dhcp_agent.in:</em>

enable_metadata_network = True

enable_isolated_metadata = True

<em>/neutron/l3_agent.ini:</em>

enable_metadata_proxy = False

<em><strong>2. For using qrouter namespace (l3-agent):</strong></em>

<em>/neutron/dhcp_agent.ini:</em>

enable_metadata_network = True

enable_isolated_metadata = False

<em>/neutron/l3_agent.ini:</em>

enable_metadata_proxy = True

metadata_port = 9697

metadata_proxy_socket = /var/lib/neutron/metadata_proxy

&nbsp;

<em><strong>Contact: vietstack@gmail.com</strong></em>

<em><strong>Facebook Groups: https://www.facebook.com/groups/vietstack/</strong></em>

<em><strong>TW: @vietstack</strong></em>
