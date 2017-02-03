Postgre-perun
========

This role server for installation of Postgre database. It also sets everything necessary in database files.


Requirements
------------

 - Perun user (role perun).
 - Cloned folder with Perun on local disk (https://github.com/CESNET/perun). You can do this with role perun.


Role Variables
--------------

 - You can fill **init_user_first_name** and **init_user_last_name** variables in **vars/main.yml** file.
 - Database name is set by default to perun, same as database user name. It's set in group-vars/perun-servers.yml file. We recommend to don't change it.

License
-------

BSD

Author Information
------------------

Jan Zvěřina, CESNET, zverina@cesnet.cz
