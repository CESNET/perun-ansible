# Perun Ansible script

This repository serves for easier deployment of Perun server with default configuration.

## Requirements

 - 64-bit Debian system
 - Requires atleast 4GB free disk space
 - Ideally dedicated 2 CPUs and atleast 3GB RAM

## Installation of Ansible

- First you need to install Ansible to your system ([Installation of Ansible](http://docs.ansible.com/ansible/intro_installation.html)), which will be used to install Perun to remote server (or localhost).
- **The minimal version of Ansible is 2.4!**
- **You must be able to log into the remote server**, thus your public SSH key must be placed in the file /root/.ssh/authorized_keys on the remote server.
- **SSH and Python must be installed on both sides of communication.**

## Clone this repo

- Now you need to download our Ansible repository from our Github
  - [Perun-Ansible repository](https://github.com/CESNET/perun-ansible)

## Install TLS certificates

- At the target machine, preferably in the **/etc/perun/ssl** folder, generate a private key and a certificate request
- Get TLS certificate from a reputable certification authority together with the CA's chain of certificates from a trusted root CA 
- Put the certificate and chain files to the folder with the private key. 
- Install package **ssl-cert** and change the owning group of the private key file to the group **ssl-cert** (needed for LDAP and PostgreSQL servers to access it) 
- You will have to renew the certificate every couple of years, so we recommend to name the actual files with the certificate, key and chain in a way that marks the year in which they were issued, and to create symbolic links named like cert.pem, key.pem and chain.pem that you will use in all configuration files. In such setup, after renewing the certificate, you will just change the symbolic links instead of all configuration files.

## Set address of your server in inventories

- In **./inventories/prod** file you must **set hostname or ip address** of your Perun server. If you have some test server, you can set it in ./inventories/test file too.
- Ansible_user variable mentioned behind your hostname is user, which has root privileges (default root).
- For example fill this file with this row: **perun.example.com ansible_user=root**

## Create configuration files for your host

- Create a new folder under **host_vars/** named exactly as your host in the inventory file
- Copy there files **vars.yml** and **passwords.yml** from the folder **host_vars/perun.example.com/**
- Edit the file **vars.yml** with any text editor and set all variables to real values
- Change password of the **passwords.yml** vault with command: `ansible-vault rekey passwords.yml` (Default password is set to "test".)
- Edit the vault file with command `ansible-vault edit passwords.yml` and set real values
- If you have more than one Perun server, just create a folder with vars.yml and passwords.yml files under the host_vars/ folder for each server.

## Optional Oracle DB drivers

- Download Oracle DB drivers 'orai18n’ and 'ojdbc8’ from [here](http://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html) on your system with Ansible (**the system which you will use to run this playbook**).
- You need to register before downloading these files.
- Put these file into your home directory (at the local machine with Ansible), or set paths to these files in **group_vars/all/vars.yml** file (variables **ojdbc8_file_path** and **orai18n_file_path**).
- Set the variable **install_oracle: True** in the **vars.yml** file.
- This will put the drivers into Tomcat and into all the produced executable jars.

## Run Ansible playbook

- Now you can run ansible script with this command (**you need to be in downloaded Ansible repository**). It uses hostname from ./inventories/prod file. You can change it to ./inventories/test (or dev) to use hostname from another inventory.
  - `ansible-playbook -i inventories/prod --ask-vault-pass site.yml`
  - It will ask for your vault password (the file which stores passwords meantioned before)

- Perun should be running after installation on **https://[hostname]/[auth-type]/rpc/**, where [auth-type] is either cert for certificates, non for non-authorized access and ba for basic auth (our initial user perun).
- For example: **https://[hostname]/ba/gui/** will show GUI of Perun. First you need to fill username and password (user: perun, password: the one you set in passwords.yml file in password_perun_admin variable).

## After installation

Now you need to do stuff, which is not handled by Ansible script:

- **Generate SSH key for Perun for accessing slave machines**
  - As perun user use command: `ssh-keygen -t rsa -C "perun@[perun's_address]" -f ~/.ssh/id_rsa`
  - Public part of key must be set in /root/.ssh/authorized_keys on each slave machine to be controlled by Perun. 

- **Add certificates to truststore**
  - The truststore have to contain certificates of servers, which are contacted by Perun. More precisely, chain of their parent certificates (if they are not distributed with Java / browser). Keytool is used to add them:
  - `keytool -keystore /home/perun/.truststore -importcert -trustcacerts -file /tmp/file_with_cert -alias [cert alias, e.g. rt4.cesnet.cz]`

- **Install slave scripts at slave machines**
  - The slave scripts should be installed at the machines that Perun will control, not at the Perun server!
  - Add APT repository by creating file /etc/apt/sources.list.d/meta_repo.list containing the line `deb ftp://repo.metacentrum.cz/ all main pilot` and run `apt-get update`
  - Install slave scripts for each needed service, e.g.: `apt-get install perun-slave-process-passwd` for installation of **passwd service**
  - For **all services** install meta package perun-slave-full: `apt-get install perun-slave-full`
