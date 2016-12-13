LDAP-Perun
========

Sets LDAP for Perun.


Requirements
------------

Perun is installed on your system.
Apache installed with apache-perun role.

Role Variables
--------------

You need to set password for LDAP root in **./group-vars/passwords.yml** file. **Group-vars** directory is stored in root directory of this Ansible repository.
You need to set paths for your certificates in **./group-vars/all.yml** file. If you don't set them, it will use certificates of Apache.

License
-------

BSD


Author Information
------------------

Jan Zvěřina, CESNET, zverina@cesnet.cz
