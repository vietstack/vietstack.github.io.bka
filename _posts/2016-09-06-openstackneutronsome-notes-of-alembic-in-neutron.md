---
title: [OpenStack][Neutron]Some notes of Alembic in Neutron
date: 2016-09-06 17:34
comments: true
categories: 
  - Tech
tags: 
  - Chia sẻ
  - Kinh nghiệm
  - VietStack
  - Neutron
  - OpenStack
---
The reason why Neutron use alembic for db migration you can get reference from here with some advantages of alembic to compare to migration_repo of SqlAlchemy in Nova: <a href="https://bitbucket.org/zzzeek/alembic">https://bitbucket.org/zzzeek/alembic</a>. Someone may ask that why Nova does not move to Alembic, IMHO there might be the reason besides the advantages of Alembic that Nova has a lot db migration scripts and it probably takes a lot of time to move to alembic (and of course, it is a non-trivial job).

In Neutron, the db migration is executed by the help of Alembic – a db migration tool for SqlAlchemy. For more information of Alembic tutorial, I suggest to read its official documentation as below, it is well written and full details: <a href="http://alembic.zzzcomputing.com/en/latest/index.html">http://alembic.zzzcomputing.com/en/latest/index.html</a>

There are couple things in Alembic I think we should take care about:

<ol>
<li>alembic.ini file that will be stored in the working directory for Alembic configurations such as location of migration scripts, logging, etc. In Mitaka version, it is inside /neutron/db/migration directory</p></li>
<li>env.py will take the parameters from alembic.ini and set the working environment for alembic. It uses alembic config object to provide the access to alembic.ini. It must be stored in the directory where migration scripts are located. In Mitaka version, it is stored inside /neutron/db/migration/alembic_migration.</p></li>
<li><p>cli.py: The script that maps each of option when we run 'neutron-db-manage' into appropriate methods inside.</p></li>
</ol>

<p>When we want to do db migration in neutron, we trigger the command of 'neutron-db-manage' that will do nothing else just calling 'main()' function of cli.py script.

If you want to test with Alembic with/without Devstack enviroment, I strongly suggest the below official page of OpenStack:

<a href="http://docs.openstack.org/developer/neutron/devref/alembic_migrations.html">http://docs.openstack.org/developer/neutron/devref/alembic_migrations.html</a>

I have had a small test with my local Devstack environment and indeed, it was funny :d. I just created another function called “vietstack” instead of “neutron-db-manage” and did couple of db manipulations.

5/9/2016

VietStack
