# perun-ansible
**:construction_worker: This is work in progress :construction_worker:**

This repsitory contains the playbooks and roles necessary to deploy a Perun service.

  1. Install Ansible
  1. Clone this repo
  1. Set some variables
  1. Install prerequisite roles
  1. Run the `site.yml` playbook

## Installation of Ansible:
---------------------------
- First you need to install Ansible to your system (http://docs.ansible.com/ansible/intro_installation.html), which will be used to install Perun to remote server.
- After installation of Ansible to your system you need to add ip address of your Perun server to file /etc/ansible/hosts:
    - [perun-server]
    - PerunServer ansible_host=Your.IP.Address

## Clone this repo
---------------------------------------------------
- Now you need to download our Ansible repository from our Github
  - https://github.com/CESNET/perun-ansible
  - It doesn't matter where you will place this folder on your system.

## Set Local variables
----------------------------
- You need to edit configuration files in downloaded Ansible repository. See [the Ansible Documentation](http://docs.ansible.com/ansible/playbooks_variables.html) on where to put variables
- Configuration files are in folders:
  - ./group_vars/
  - ./host_vars/
  - ./roles/*/vars/

## Install prerequisite roles

Some of the roles included here in turnn depend on roles themselves. Install these by doing : `ansible-galaxy install -r requirements.yml -p roles`

## Run Ansible playbook
----------------------
- Now you can run ansible script with this command (you need to be in downloaded ansible repository (step 2))
  - `ansible-playbook --ask-become-pass site.yml`
  - It will require password that you have entered like hash to perun_password variable in file ./roles/common/vars/main.yml
