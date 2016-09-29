# perun-ansible

1) Installation of Ansible:
---------------------------
- First you need to install Ansible to your system (http://docs.ansible.com/ansible/intro_installation.html), which will be used to install Perun to remote server.
- After installation of Ansible to your system you need to add ip address of your Perun server to file /etc/ansible/hosts:
    # Virtual Debian Machine
    [perun-server]
    Webserver ansible_host=Your.IP.Address

2) Download our Ansible repository from our Github:
---------------------------------------------------
- Now you need to download our Ansible repository from our Github
  - https://github.com/CESNET/perun-ansible
  - It doesn't matter where you will place this folder on your system.

3) Edit configuration files:
----------------------------
- You need to edit configuration files in downloaded Ansible repository.
- Configuration files are in folders:
  - ./group_vars/
  - ./host_vars/
  - ./roles/common/vars/

4) Run Ansible script:
----------------------
- Now you can run ansible script with this command (you need to be in downloaded ansible repository (step 2))
  - ansible-playbook --ask-become-pass site.yml
  - It will require password that you have entered like hash to perun_password variable in file ./roles/common/vars/main.yml
