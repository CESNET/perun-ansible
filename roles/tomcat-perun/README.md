Tomcat-perun
========

This role serves for installation of Tomcat with recommended settings for Perun.


Requirements
------------

- Apache (install it with role apache-perun)
- If you use default snake oil certificate, you need to generate it with apache-perun role
- Perun user (create him with role perun)


Role Variables
--------------

You have to set variables in group-vars/all.yml and group-vars/passwords.yml files. Directory group-vars is placed in root directory of this Ansible repository. How to access group-vars/passwords.yml file is written in README.md in root directory of this Ansible repository.

The script will export certificate of Apache to format .p12. You have to set password for it in file group-vars/passwords.yml (variable password_tomcat_certificate).


License
-------

BSD


Author Information
------------------

Jan Zvěřina, CESNET, zverina@cesnet.cz
