---
title: "Testing Python WSGI"
comments: true
categories: 
  - Tech
  - Blog
tags: 
  - WSGI
  - OpenStack
  - Python
---
<span style="font-family:Arial, sans-serif;"><span style="font-size:medium;">For a time when I have worked with OpenStack, I stumbled upon for the idea of how the api of most of OpenStack services (Nova, Neutron, Cinder, etc.) implemented? When you type some CLI e.g. nova list, neutron net-list, etc. how do they execute the CLI to talk with API servers? Then I realized that they are implemented with Python wsgi application, webob and Paste. For example, if you check Nova project, you can see the file wsgi.py that defines all of abstractions of wsgi elements such as application, middleware, server, etc. Next, nova-api server will abstract them inside the nova/api/openstack/wsgi.py as an application. The wsgi server of nova is initiated in nova/service.py.</span></span>

<span style="font-family:Arial, sans-serif;"><span style="font-size:medium;">In this article, we will not go further with Paste (actually, it is not difficult to understand, you can easily search on Internet). I just try to create a complete example to show how the wsgi app, middleware, server work. The below result is only for testing, for fun, do not take it in further purposes.</span></span>

<span style="font-family:Arial, sans-serif;"><span style="font-size:medium;"><u>From the wiki:</u></span></span>

<span style="color:#252525;"><span style="font-family:Arial, sans-serif;"><span style="font-size:medium;">A </span></span></span><span style="color:#252525;"><span style="font-family:Arial, sans-serif;"><span style="font-size:medium;"><b>WSGI application </b></span></span></span><span style="color:#252525;"><span style="font-family:Arial, sans-serif;"><span style="font-size:medium;">is passed a Python representation of an HTTP request by an application, and returns content which will normally eventually be rendered by a web browser. A common use for this is when </span></span></span><span style="color:#252525;"><span style="font-family:Arial, sans-serif;"><span style="font-size:medium;">a web server </span></span></span><span style="color:#252525;"><span style="font-family:Arial, sans-serif;"><span style="font-size:medium;">serves content created by Python code.</span></span></span>

<span style="color:#252525;"><span style="font-family:Arial, sans-serif;"><span style="font-size:medium;">There are, however, other uses: <b>WSGI middleware </b>is Python code that receives a WSGI request and then performs logic based upon this request, before passing the request on to a WSGI application or more WSGI middleware. WSGI middleware appears to an application as a server, and to the server as an application</span></span></span>

&nbsp;

<span style="font-family:Arial, sans-serif;"><span style="font-size:medium;">Here is the source code:</span></span>

<span style="font-family:Arial, sans-serif;"><span style="font-size:medium;">https://github.com/vietstacker/-OpenStack-Testing-Python-WSGI/blob/master/test_wsgi.py</span></span>

&nbsp;

<h3>When you start the server, go to terminal and type:</h3>

<h3><span style="color:#ff0000;">curl http://localhost:8080</span></h3>

&nbsp;

<span style="font-family:Arial, sans-serif;"><span style="font-size:medium;"><u>The result will be like this:</u></span></span>

<span style="color:#ff0000;"><span style="font-family:Arial, sans-serif;"><span style="font-size:medium;">etualuo@elxgsgld12-ol:~$ curl http://localhost:8080</span></span></span>

<span style="color:#ff0000;"> <span style="font-family:Arial, sans-serif;"><span style="font-size:medium;">From VietStack: Wellcome the 8th Meetup of VietOpenStack</span></span></span>

<h3>Have FUN and ENJOY the 8<sup>th</sup> meetup of VietOpenStack</h3>

26/2/2016

VietStack Team

&nbsp;
