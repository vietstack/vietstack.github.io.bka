---
title: [OpenStack-Tacker] Some information to use it for MANO
comments: true
categories: 
  - Tech
tags: 
  - Chia sẻ
  - Kinh nghiệm
  - VietStack
  - Tacker
  - MANO
---
Telco cloud is one of the most considering use case that is implemented, leveraged by collaboration of telco cooperation such as Huawei, Nokia, Ericsson, etc. The abstraction of MANO (Management and Network Orchestration) then is created by ETSI ISG NFV that is a combination of entities that manages and orchestrates cloud infrastructure for NFV. For more information, you can have it in the official website of ETSI:

<a href="http://www.etsi.org/technologies-clusters/technologies/nfv">http://www.etsi.org/technologies-clusters/technologies/nfv</a>

As of today, there are actually a race/competition between telco cloud vendors about MANO implementation. There are some commercial products as below:

<ul>
<li>CloudBand Management System of Alcatel-Lucent</p></li>
<li>Nokia Cloud Application Manager</p></li>
<li><p>Ericsson Cloud Manager</p></li>
<li><p>HP NFV Director</p></li>
</ul>

<p>Besides, there are some open source MANO projects that are orginally initiated by telco cloud providers. They are OpenMANO of Telefonica – Spain, OpenO under the driving of China Mobile. In addition, there are also some products/projects that are seemed as a part and supports MANO such as Cloudify, Ubuntu Juju, etc. One of them is OpenStack Tacker.

In order to test Tacker, we can try it with Devstack by adding the below line in local.conf:

<strong>enable_plugin tacker https://git.openstack.org/openstack/tacker master</strong> (I tried to test with master branch of Tacker on top of Mitaka)

About the description of OpenStack Tacker, you can easily check out on the Internet. Since OpenStack Tacker utilizes TOSCA template and the most important part to use Tacker is how to write the TOSCA template. More information is below:

<a href="http://docs.openstack.org/developer/tacker/devref/vnfd_template_description.html">http://docs.openstack.org/developer/tacker/devref/vnfd_template_description.html</a>

Have fun,

VietStack team
