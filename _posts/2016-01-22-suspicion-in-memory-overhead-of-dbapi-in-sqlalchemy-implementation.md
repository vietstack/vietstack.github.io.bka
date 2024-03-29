---
title: "Suspicion in memory overhead of DBAPI in sqlalchemy implementation"
comments: true
categories: 
  - Tech
  - Blog
tags: 
  - DBAPI
  - OpenStack
  - sql
  - Memory
---
<p class="western" align="center"><strong>MEMORY OVERHEAD OF DBAPI IN SQLALCHEMY IMPLEMENTATION</strong></p>

<p class="western"><strong><span style="color:#000080;"><span lang="zxx"><a href="http://projects.curiousllc.com/examining-sqlalchemy-memory-usage.html"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:large;">Overview:</span></span></span></a></span></span></strong></p>

<p class="western"><span style="color:#000080;"><span style="color:#00000a;"><span lang="zxx"><span style="font-family:'Times New Roman', serif;">This investigation will go more deeply into sqlalchemy and its usage. The investigation continues with a thread that implements the database access to query the events from database. Now it shows the slight increment of memory usage.</span></span></span></span></p>

<p class="western"><span style="color:#000080;"><span lang="zxx"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:large;"><b>Investigation Analysis:</b></span></span></span></span></span></p>

<p class="western"><span style="color:#000080;"><span lang="zxx"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;">In our service, previously the suspicion appears that if it had the memory leak caused by python source code implementation; it would leak from very beginning of its deployment. Other considerations that there were not any information about other services/applications running at the same time of the service running that could affect the operation of it.</span></span></span></span></p>

<p class="western"><span style="color:#000080;"><span lang="zxx"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;">In addition, some previous investigations show that there are no problems in python code implementation so that the direction of this investigation focuses on sqlalchemy and database access implementation. After doing many experiments, it turned out that there is an overhead of database access by using query of sqlalchemy.</span></span></span></span></p>

<ol type="a">
    <li><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><b>Performance of querying the whole table in SqlAlchemy</b></span></span></li>
</ol>

<p class="western"><span style="color:#000080;"><span lang="zxx"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;">The below report shows the difference of memory usage between querying whole table and querying single columns of table.</span></span></span></span> <span style="color:#000080;"><span lang="zxx"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;">(Source: <a href="http://projects.curiousllc.com/examining-sqlalchemy-memory-usage.html">http://projects.curiousllc.com/examining-sqlalchemy-memory-usage.html</a>)</span></span></span></span></p>

<p class="western"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><u>Scenario 1: Queries the whole table.</u></span></span></p>

<p class="western"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">Line # Mem usage Increment Line Contents</span></span></span></p>

<p class="western"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">================================================</span></span></span></p>

<p class="western"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">56 46.0 MiB 0.0 MiB           @profile</span></span></span></p>

<p class="western"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">57                                          def get_bounced_emails():</span></span></span></p>

<p class="western"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">58 48.2 MiB 2.2 MiB             emails = db.session.query(EmailAddress).\</span></span></span></p>

<p class="western"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">59 </span></span></span><span style="color:#ff0000;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">48.3 MiB 0.0 MiB</span></span></span><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">                 join(send_history, send_history.c.email_id == EmailAddress.email_id).\</span></span></span></p>

<p class="western"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">60 </span></span></span><span style="color:#ff0000;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">372.1 MiB 323.9 MiB</span></span></span><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">                  filter(send_history.c.bounce == 1).\ all()</span></span></span></p>

<p class="western"></p>

<p class="western"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">63 372.1 MiB 0.0 MiB            email_dict = {}</span></span></span></p>

<p class="western"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">64 374.1 MiB 2.0 MiB            for email in emails:</span></span></span></p>

<p class="western"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">65 374.1 MiB 0.0 MiB                email_dict[email.email_address] = True</span></span></span></p>

<p class="western"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">67 314.9 MiB -59.2 MiB         del emails       </span></span></span></p>

<p class="western"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">68 314.9 MiB 0.0 MiB            return email_dict</span></span></span></p>

<p class="western"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><u>Scenario 2: Queries the single column of table.</u></span></span></p>

<pre class="western"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">Line </span></span></span><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;"><i># Mem usage Increment Line Contents</i></span></span></span>
<span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">================================================</span></span></span>
<span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">56 46.0 MiB 0.0 MiB      @profile</span></span></span>
<span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">57                               def get_bounced_emails():</span></span></span>
<span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">58 47.1 MiB 1.1 MiB       emails = db.session.query(EmailAddress.email_address).\</span></span></span>
<span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">59 </span></span></span><span style="color:#ff0000;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">47.1 MiB 0.0 MiB</span></span></span><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">           join(send_history, send_history.c.email_id == EmailAddress.email_id).\</span></span></span>
<span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">60 </span></span></span><span style="color:#ff0000;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">81.2 MiB 34.1 MiB</span></span></span><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">          filter(send_history.c.bounce == 1).\</span></span></span>
<span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">61                                           all()</span></span></span>
<span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">62</span></span></span>
<span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">63 81.2 MiB 0.0 MiB       email_dict = {}</span></span></span>
<span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">64 84.2 MiB 3.0 MiB       for email in emails:</span></span></span>
<span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">65 84.2 MiB 0.0 MiB           email_dict[email[0]] = True</span></span></span>
<span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">66</span></span></span>
<span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">67 65.9 MiB -18.2 MiB    del emails</span></span></span>
<span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">68 65.9 MiB 0.0 MiB       return email_dict</span></span></span></pre>

<p class="western"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;">The memory can increase so extremely if we query the whole table (in this example is EmailAddress) to get the entire record. After that, the memory consumption decreases outstandingly if we query only a single field (email_address) of the table. The below test is source code of cmha:</span></span></p>

<ol start="2" type="a">
    <li><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><b>Performance of querying all the objects in database. </b></span></span></li>
</ol>

<p class="western"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><u>After querying all the event:</u></span></span></p>

<p class="western"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">&lt;132&gt;Jan 21 14:52:29  stdout WARNING Line # Mem usage Increment Line Contents</span></span></span></p>

<p class="western"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">&lt;132&gt;Jan 21 14:52:29 stdout WARNING ================================================</span></span></span></p>

<p class="western"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">&lt;132&gt;Jan 21 14:52:29 stdout WARNING 69 <span style="color:#ff0000;">31.2 MiB 0.0 MiB</span>     @profile</span></span></span></p>

<p class="western"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">&lt;132&gt;Jan 21 14:52:29 stdout WARNING 70                                     def _get_events(self):</span></span></span></p>

<p class="western"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">&lt;132&gt;Jan 21 14:52:29 stdout WARNING 71 <span style="color:#ff0000;">31.2 MiB 0.0 MiB</span>            with self.factory.session_scope() as session:</span></span></span></p>

<p class="western"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">&lt;132&gt;Jan 21 14:52:29 stdout WARNING 72 <span style="color:#ff0000;">31.2 MiB 0.0 MiB    </span>                return session.query(Event).all()</span></span></span></p>

<p class="western"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">******************************************************************************************************</span></span></span></p>

<p class="western"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">&lt;132&gt;Jan 21 15:11:16 stdout WARNING Line # Mem usage Increment Line Contents</span></span></span></p>

<p class="western"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">&lt;132&gt;Jan 21 15:11:16 stdout WARNING ================================================</span></span></span></p>

<p class="western"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">&lt;132&gt;Jan 21 15:11:16 stdout WARNING 69 <span style="color:#ff0000;">31.3 MiB 0.0 MiB  </span>         @profile</span></span></span></p>

<p class="western"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">&lt;132&gt;Jan 21 15:11:16 stdout WARNING 70                                          def _get_events(self):</span></span></span></p>

<p class="western"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">&lt;132&gt;Jan 21 15:11:16 stdout WARNING 71 <span style="color:#ff0000;">31.3 MiB 0.0 MiB</span>                    with self.factory.session_scope() as session:</span></span></span></p>

<p class="western"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">&lt;132&gt;Jan 21 15:11:16 stdout WARNING 72 <span style="color:#ff0000;">31.3 MiB 0.0 MiB        </span>                     return session.query(Event).all()</span></span></span></p>

<p class="western"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">******************************************************************************************************</span></span></span></p>

<p class="western"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">&lt;132&gt;Jan 21 15:13:38 stdout WARNING Line # Mem usage Increment Line Contents</span></span></span></p>

<p class="western"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">&lt;132&gt;Jan 21 15:13:38 stdout WARNING ================================================</span></span></span></p>

<p class="western"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">&lt;132&gt;Jan 21 15:13:38 stdout WARNING 69 <span style="color:#ff0000;">31.4 MiB 0.0 MiB</span>             @profile</span></span></span></p>

<p class="western"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">&lt;132&gt;Jan 21 15:13:38 stdout WARNING 70                                            def _get_events(self):</span></span></span></p>

<p class="western"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">&lt;132&gt;Jan 21 15:13:38 stdout WARNING 71 <span style="color:#ff0000;">31.4 MiB 0.0 MiB</span>                        with self.factory.session_scope() as session:</span></span></span></p>

<p class="western"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">&lt;132&gt;Jan 21 15:13:38 stdout WARNING 72 <span style="color:#ff0000;">31.4 MiB 0.0 MiB  </span>                                return session.query(Event).all()</span></span></span></p>

<p class="western"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;">It is clear in the result that the memory usage increases by time in querying the whole Event table and is returned in a list. This amount of money may increase in the higher number with much more actors that usually access to database at the same time.</span></span></p>

<p class="western"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><u>After querying single event:</u></span></span></p>

<p class="western"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">&lt;132&gt;Jan 22 11:38:35 stdout WARNING Line # Mem usage Increment Line Contents</span></span></span></p>

<p class="western"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">&lt;132&gt;Jan 22 11:38:35 stdout WARNING ================================================</span></span></span></p>

<p class="western"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">&lt;132&gt;Jan 22 11:38:35 stdout WARNING 69 <span style="color:#ff0000;">28.3 MiB 0.0 MiB</span>         @profile</span></span></span></p>

<p class="western"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">&lt;132&gt;Jan 22 11:38:35 stdout WARNING 70                                        def _get_events(self):</span></span></span></p>

<p class="western"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">&lt;132&gt;Jan 22 11:38:35 stdout WARNING 71 <span style="color:#ff0000;">28.3 MiB 0.0 MiB</span>                      with self.factory.session_scope() as session:</span></span></span></p>

<p class="western"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">&lt;132&gt;Jan 22 11:38:35 stdout WARNING 72                                                              #return session.query(Event).all()</span></span></span></p>

<p class="western"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">&lt;132&gt;Jan 22 11:38:35 stdout WARNING 73 <span style="color:#ff0000;">28.3 MiB 0.0 MiB    </span>                            return session.query(Event)</span></span></span></p>

<p class="western"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">&lt;132&gt;Jan 22 11:38:35 stdout WARNING </span></span></span></p>

<p class="western"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">********************************************************************************************************</span></span></span></p>

<p class="western"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">&lt;132&gt;Jan 22 13:44:09 stdout WARNING Line # Mem usage Increment Line Contents</span></span></span></p>

<p class="western"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">&lt;132&gt;Jan 22 13:44:09 stdout WARNING ================================================</span></span></span></p>

<p class="western"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">&lt;132&gt;Jan 22 13:44:09 stdout WARNING 69 <span style="color:#ff0000;">29.0 MiB 0.0 MiB  </span>                  @profile</span></span></span></p>

<p class="western"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">&lt;132&gt;Jan 22 13:44:09 stdout WARNING 70                                                   def _get_events(self):</span></span></span></p>

<p class="western"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">&lt;132&gt;Jan 22 13:44:09 stdout WARNING 71<span style="color:#ff0000;"> 29.0 MiB 0.0 MiB  </span>                          with self.factory.session_scope() as session:</span></span></span></p>

<p class="western"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">&lt;132&gt;Jan 22 13:44:09 stdout WARNING 72                                                                  #return session.query(Event).all()</span></span></span></p>

<p class="western"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">&lt;132&gt;Jan 22 13:44:09 stdout WARNING 73 <span style="color:#ff0000;">29.0 MiB 0.0 MiB  </span>                                 return session.query(Event)</span></span></span></p>

<p class="western"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:small;">&lt;132&gt;Jan 22 13:44:09 stdout WARNING </span></span></span></p>

<p class="western"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;">It shows that the memory assignment is smaller than the scenario of querying all the rows but it still throws out the appearance of slight memory usage.</span></span></p>

<ol start="3" type="a">
    <li><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><b>Result Analysis:</b></span></span></li>
</ol>

<p class="western"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;">Most of DBAPIs pre-buffer all the rows as they are fetched in memory before returning it back to the objects. It means that before the SQLAlchemy ORM gets a hold of the returned results, the whole results are stored in memory. Since the underlying DBAPI pre-buffers the rows, there will be some memories overhead even this memory overhead is much less than the memory used for ORM mapped object. </span></span></p>

<p class="western"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;">The DBAPI of MySQL is MySQLDB (a.k.a. mysql-python) and yes, it also pre-buffers the rows. Let see the below example:</span></span></p>

<p class="western"><span style="color:#00000a;"> <span style="font-family:'Times New Roman', serif;">(1) with </span></span><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><i>self</i></span></span><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;">.factory.session_scope() as session:</span></span></p>

<p class="western"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;">           return session.query(Event).all()</span></span></p>

<p class="western"><span style="color:#00000a;"> <span style="font-family:'Times New Roman', serif;">(2) with </span></span><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><i>self</i></span></span><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;">.factory.session_scope() as session:</span></span></p>

<p class="western"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;">            return session.query(Event)</span></span></p>

<p class="western"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;">In [1], ORM will work on all the rows before starting to return them back. It will return all the collected data yielded by generator into a list (check the “all()” method of query). Meanwhile in [2], ORM works on each row as soon as its data arrives and then returns back – it seems like “DB streaming”. The second scenario will take less memory use and latency. </span></span></p>

<p class="western"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;">Otherwise, it is recommended that it should query the individual columns (e.g. session.query(Object.column1, Object.column2)) instead of the whole ORM object. This action will decrease the memory overhead of loading data through DBAPI.</span></span></p>

<pre class="western"><span style="color:#00000a;">                     <span style="font-family:'Times New Roman', serif;"><span style="font-size:medium;">+-----------+                               __________</span></span></span>
<span style="color:#00000a;">                    /<span style="font-family:'Times New Roman', serif;"><span style="font-size:medium;">---|   Pool    |---\                          (__________)</span></span></span>
<span style="color:#00000a;">        <span style="font-family:'Times New Roman', serif;"><span style="font-size:medium;">+-------------+    /     +-----------+     \     +--------+   |                     |</span></span></span>
<span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:medium;">connect() &lt;--|   Engine    |---x                            x----| DBAPI    |---|  database |</span></span></span>
<span style="color:#00000a;">        <span style="font-family:'Times New Roman', serif;"><span style="font-size:medium;">+-------------+    \    +-----------+     /     +--------+    |                    |  </span></span></span>
<span style="color:#00000a;">                   <span style="font-family:'Times New Roman', serif;"><span style="font-size:medium;">\---|  Dialect  |---/                            |__________|</span></span></span>
<span style="color:#00000a;">                    <span style="font-family:'Times New Roman', serif;"><span style="font-size:medium;">+-----------+                                  (__________)</span></span></span></pre>

<p class="western"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;">                Figure 1. General structure of sqlalchemy in database access. </span></span></p>

<p class="western"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;">                     (Source: http://docs.sqlalchemy.org/en/rel_0_5/dbengine.html)</span></span></p>

<p class="western"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;"><span style="font-size:large;"><b>Summary:</b></span></span></span></p>

<p class="western"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;">1. In case of querying the whole table, it seems that even using query for a single ORM object for returning a list of ORM objects, the DBAPI still pre-buffers the rows and it takes memory consumption (note that there is no difference in DB traffic among these queries). However, it is clear that the single object query takes less memory than returning the list of queried objects.</span></span></p>

<p class="western"><span style="color:#00000a;"><span style="font-family:'Times New Roman', serif;">2. In case of querying the single columns of table, it depends on the service's functionalities to work with events collected from database. It also depends on the design of event. Finally, it does not ensure that the memory overhead will disappear, but at first, it decreases the memory usage.</span></span></p>
