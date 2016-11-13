---
title: "[OpenStack][Extensions] Write a new feature as extension"
date: 2016-08-22 20:13
comments: true
categories: 
  - Tech
  - Blog
tags: 
  - VietStack
  - OpenStack
  - API
  - Python
---
The first question is why we need extensions?

OpenStack as other architectures that support user interaction via API needs to have standard, stable API and another feature that supports innovation, capability for adding new specification based on use cases – it is called extensibility. This extensibility in API allows developing new API based on the needs of different, niche use cases but does not affect to the base, standard API. In a nut shell, a developer wants to add a new feature that returns some information of instance running on cloud, he/she can put it into extension of API then user can query and this modification does not make any problems of standard API that is defined before. If this modification is not needed, it can be removed easily. It looks like pluggability. Otherwise, extensions in API are important for testing new features before these feature become standard.

However, in the next releases of OpenStack, extensions will be removed and replaced by API microversion. Since some new features are developed before with API extensions due to the need of use case, developers still need to maintain and uplift them if needed.

In OpenStack, the folder includes extensions is …/contrib. For example, in novaclient, we can easily find the directory of novaclient/v2/contrib/. In client.py of novaclient (novaclient/client.py), we should see the discovery of extension as below. One of the extension provider is through contrib path:

<img class="alignnone size-full wp-image-880" src="https://vietstack.files.wordpress.com/2016/08/discovery.png" alt="discovery" width="1300" height="469" />

&nbsp;

Let's have a quick test with it. I am going to create a new CLI that is called “vietstack” as below:

<img class="alignnone size-full wp-image-868" src="https://vietstack.files.wordpress.com/2016/08/vietstack.png" alt="vietstack" width="1855" height="1056" />

What we are going to do is understanding a flow of creating a new feature and put it into API extension. Here I chose Nova for presenting.

The below addition is necessary for novaclient as we type “nova vietstack”.

&nbsp;

&nbsp;

<img class="alignnone size-full wp-image-877" src="https://vietstack.files.wordpress.com/2016/08/contrib.png" alt="contrib" width="1300" height="801" />

Since we have the additional feature in novaclient, each time we trigger “vietstack” command as “nova vietstack”, this command will talk to the <strong>nova-api</strong> and call the appropriate feature that we are going to define for extension. We have to put a new feature called “vietstack” in <strong>/nova/api/openstack/compute/contrib/vietstack.py</strong>. What you want to develop vietstack.py that will execute some actions when you call "nova vietstack".

You can re-write live-migration or offline-migration and rename it “vietstack” - we will charge you if you use it for copyright :D – or you can develop the new feature. That is actually up to you. Take the below instruction for reference:

http://docs.openstack.org/developer/nova/api_plugins.html

Have fun :D

&nbsp;

22/08/2016

VietStack team

<h6></h6>
