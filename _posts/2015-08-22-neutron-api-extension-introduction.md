---
title: Neutron API Extension Introduction
comments: true
categories: 
  - Tech
  - Blog
tags: 
  - Neutron
  - OpenStack
  - API
  - Python
---
<strong>Overview:</strong>

All of the requests coming from neutron-client of dashboard will be tranfered to neutron-server using REST API. Those requests are passed to a URL defined in neutron-server. Neutron-server will read that URL, revoke the correspoding methods in plugin then map the corresponding actions like (show, list, delete, etc.) based on methods (POST, GET, DELTE, etc.) defined in URL.

If you have your own plugin and you want neutron-server read URL (e.g. /myextension) with your own plugin, all that you have to do is writing the new plugin inside the plugins folder of neutron. Neutron allows the third party vendors to customize their own methods specific to their plugins with the help of API extensions.

This example will introduce to you the basic steps about writing an API extension.

<ul>
    <li> In the folder plugins, i create a "myplugin" tree as below:</li>
</ul>

<a href="https://vietstack.files.wordpress.com/2015/08/screenshot-from-2015-08-22-101943.png"><img class=" wp-image-567 alignleft" src="https://vietstack.files.wordpress.com/2015/08/screenshot-from-2015-08-22-101943.png" alt="Screenshot from 2015-08-22 10:19:43" width="510" height="252" /></a>

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

<ul>
    <li>In myextension.py, we need to define two things: a RESOURCE_ATTRIBUTE_MAP dictionary and a class called Myextension called. RESOURCE_ATTRIBUTE_MAP what we put inside this new extended attributes, myextension.py seems like below:</li>
</ul>

<blockquote><a href="https://vietstack.files.wordpress.com/2015/08/screenshot-from-2015-08-22-103515.png"><img class="aligncenter wp-image-569" src="https://vietstack.files.wordpress.com/2015/08/screenshot-from-2015-08-22-103515.png" alt="Screenshot from 2015-08-22 10:35:15" width="1000" height="1084" /></a></blockquote>

&nbsp;

<ul>
    <li>After create the myextension file, we need to tell Neutron the path of extension file. In /etc/neutron/neutron.conf, inside the section DEFAULT:</li>
</ul>

<span style="color:#ff0000;">api_extensions_path = /usr/lib/python2.7/dist-packages/neutron/plugins/myplugin/extensions</span>

&nbsp;

&nbsp;

<ul>
    <li>Create database for extensions</li>
</ul>

<em>          models.py: Defines how those properties in database</em>

&nbsp;

<blockquote><span style="color:#ff0000;"><span style="font-size:medium;">import sqlalchemy as sa</span></span>

<span style="color:#ff0000;"><span style="font-size:medium;">from neutron.db import model_base</span></span>

<span style="color:#ff0000;"><span style="font-size:medium;">class MyExtension(model_base.BASEV2):</span></span>

<span style="color:#ff0000;"> <span style="font-size:medium;">'''</span></span>

<span style="color:#ff0000;"> <span style="font-size:medium;">A simple table to store the myextension properties </span></span>

<span style="color:#ff0000;"> <span style="font-size:medium;">'''</span></span>

<span style="color:#ff0000;"><span style="font-size:medium;">     __tablename__ = 'myextension'</span></span>

<span style="color:#ff0000;"><span style="font-size:medium;">    name = sa.Column(sa.String(255),</span></span>

<span style="color:#ff0000;"><span style="font-size:medium;">    primary_key=True, nullable=False)</span></span>

<span style="color:#ff0000;"><span style="font-size:medium;">    id = sa.Column(sa.Integer)</span></span>

&nbsp;</blockquote>

<em>            db.py: Provides a number of methods to read, write properties of database such as READ, WRITE, UPDATE, GET, etc. Each method will be executed through the database session. The database session will be provided by neutron api request context.</em>

&nbsp;

<blockquote>&nbsp;

<u>For example:</u>

<span style="color:#ff0000;">def add_something(db_session, arg1*, arg2*):</span>

<span style="color:#ff0000;">            with db_session.begin (subtransactions=True):</span>

<span style="color:#ff0000;">                      do_something</span>

<span style="color:#ff0000;">                         ...</span>

<u>A simple sample of plugin.py can be written like below:</u>

<span style="color:#ff0000;">from neutron.db import db_base_plugin_v2</span>

<span style="color:#ff0000;">class MyPlugin(db_base_plugin_v2.NeutronDbPluginV2):</span>

<span style="color:#ff0000;"> # We need to define a data member to tell Neutron Server about our plugin.</span>

<span style="color:#ff0000;"> # Name of alias should be the same with get_alias() in myextension.py</span>

<span style="color:#ff0000;">      _supported_extension_aliases = ['Test-Ex']</span>

<span style="color:#ff0000;">      def __init__(self):</span>

<span style="color:#ff0000;">          pass</span>

<span style="color:#ff0000;">     def create_myextension(self, context, myextension):</span>

<span style="color:#ff0000;">          return myextension</span>

<span style="color:#ff0000;">     def update_myextension(self, context, id, myextension):</span>

<span style="color:#ff0000;">          return myextension</span>

<span style="color:#ff0000;">     def get_myextension(self, context, id):</span>

<span style="color:#ff0000;">          myextension = {}</span>

<span style="color:#ff0000;">          return myextension</span>

<span style="color:#ff0000;">     def delete_myextension(self, context, id):</span>

<span style="color:#ff0000;">          return id</span>

Restart neutron server by running “service neutron-server restart”, check “/var/log/neutron/server.log” we will see the “Test-Ex” loaded “Loaded extension: Test-Ex”.

&nbsp;</blockquote>

Reference: http://control-that-vm.blogspot.hu/2014/05/writing-api-extensions-in-neutron.html

&nbsp;

22/08/2015

VietStack Team
