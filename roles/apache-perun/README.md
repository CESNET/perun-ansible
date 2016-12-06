Apache-perun
=========

This role contains tasks for installation of Apache to your server.


Requirements
------------

- Perun user (create him with role perun)


Role Variables
--------------

Variables stored in this role are only for inner purposes. You have to fill **apache_certificate** variables in **group-vars/all.yml**. Directory **group-vars** is in root directory of this Ansible repository. If you will not change them, script will use fake snakeoil certificate.


License
-------

BSD


Author Information
------------------

Jan Zvěřina, CESNET, zverina@cesnet.cz
Bruce Becker, CSIR Meraka Institute
