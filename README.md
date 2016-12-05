# perun-ansible

**:construction_worker: This is work in progress :construction_worker:**
This repository serves for easier installation and preparation of Perun server.

## Requirements

 - 64-bit Debian system
 - Requires atleast 4GB free disk space
 - Ideally dedicated 2 CPUs and atleast 2GB RAM

## Installation of Ansible

- First you need to install Ansible to your system (http://docs.ansible.com/ansible/intro_installation.html), which will be used to install Perun to remote server (or localhost).
- **The minimal version of Ansible is 2.2.0.0!**

## Clone this repo

- Now you need to download our Ansible repository from our Github
  - [Perun-Ansible repository](https://github.com/CESNET/perun-ansible)
  - It doesn't matter where you will place this folder on your system.

## Set your passwords in Ansible Vault file

- Passwords, secret keys, etc. for your services and for Perun user account are stored in **./group-vars/passwords.yml** file.
- Go to group-vars folder and use command: `ansible-vault edit passwords.yml` (default password is set to **test**). Default editor is VIM (if you want to change it, do `export EDITOR=YOUR_FAVOURITE_EDITOR`).
- Set your passwords for services, certificates and perun user.
- Generate hash of your password for Perun User for example with command: `mkpasswd --method=sha-512` and put it to **password_user_sha512 variable**.
- Change password for this file with command: `ansible-vault rekey passwords.yml`.

## Set Local variables

- You need to edit configuration files in downloaded Ansible repository. See [the Ansible Documentation](http://docs.ansible.com/ansible/playbooks_variables.html) on where to put variables.
- Variable files are:
  - ./group_vars/all.yml
  - ./group_vars/passwords.yml
  - ./roles/*/vars/*

## Set address of your server in inventories

- In **./inventories/prod** file you must **set hostname or ip address** of your Perun server. If you have some test server, you can set it in ./inventories/test file too.
- Ansible_user variable mentioned behind your hostname is user, which has root privileges (default root).
- For example: **hostname-perun.com ansible_user=root**

## Install necessary certificates

- Put all your certificates you will use for user authentication to path defined by **ssl_ca_certificate_path** variable stored in **./group-vars/all.yml** file.

## Install prerequisite roles

- Some of the roles included here depend on roles themselves. Install these by doing : `ansible-galaxy install -r requirements.yml -p roles`

## Download Oracle DB drivers
- Download Oracle DB drivers 'orai18n’ and 'ojdbc7’ from [here](http://www.oracle.com/technetwork/database/features/jdbc/jdbc-drivers-12c-download-1958347.html) on your system with Ansible.
- You need to register before downloading these files.
- Set paths to these files in **./group-vars/all.yml** file (variables **ojdbc7_file_path** and **orai18n_file_path**).

## Choose roles you want to install

- In **site.yml** file comment roles you don't want to install by adding # before them. **Don't comment necessary roles**.
- Necessary roles are:
  - perun
  - apache-perun
  - tomcat-perun
  - postgre-perun
  - configuration-perun
  - build-perun
  - engine-perun

## Run Ansible playbook

- Now you can run ansible script with this command (**you need to be in downloaded ansible repository** (step 2)). It uses hostname from ./inventories/prod file. You can change it to ./inventories/test (or dev) to use hostname from another inventory.
  - `ansible-playbook -i inventories/prod --ask-become-pass --ask-vault-pass site.yml`

- Perun should be running after installation on **https://[hostname]/[auth-type]/rpc/**, where [auth-type] is either cert for certificates, non for non-authorized access and ba for basic auth (our initial user perun).

## After installation

Now you need to do stuff, which is not handled by Ansible script:

- **Generate SSH key for Perun**
  - As perun user use command: `ssh-keygen -t rsa -C "perun@[perun's_address]" -f ~/.ssh/id_rsa`
  - Public part of key must be set in /root/.ssh/authorized_keys on target machines, which have to be accessible for Perun. 

- **Truststore**
  - The truststore have to contain certificates of servers, which are contacted by Perun. More precisely, chain of their parent certificates (if they are not distributed with Java / browser). Keytool is used to add them, for example: `keytool -keystore /home/perun/.truststore -importcert -trustcacerts -file /tmp/file_with_cert -alias [cert alias, e.g. rt4.cesnet.cz]`
  - If Perun will contact other servers, which are consider as credible, set into start-up script of tomcat JAVA_OPTS with reference to .truststore: `JAVA_OPTS="-XX:MaxPermSize=512m -Xmx512m -XX:+UseConcMarkSweepGC -d64 -Dfile.encoding=UTF8 -Djavax.net.ssl.trustStore=/home/perun/.truststore -Djavax.net.ssl.trustStorePassword=[your_truststore_password] -Djavax.net.ssl.trustStoreType=JKS"`

- **PostgreDB**
  - If you want to access Perun using certificate, just create another ext_source and user_ext_source matching your user and certificate. Following example shows adding of certificate signed by Terena (use `psql` command to work with database):
  - Ext source defining certificate issuer: `insert into perun.ext_sources (id,name,type) values (nextval('ext_sources_id_seq'),'/C=NL/O=TERENA/CN=TERENA Personal CA','cz.metacentrum.perun.core.impl.ExtSourceX509');`
  - User ext source defining certificate DN: `insert into perun.user_ext_sources (id, user_id, login_ext, ext_sources_id, loa) values (nextval('user_ext_sources_id_seq'), currval('users_id_seq'), '/C=CZ/O=Masarykova univerzita/CN=Pavel Zl\xC3\xA1mal/unstructuredName=256627', currval('ext_sources_id_seq'), 0);`

- **Install slave scripts**
  - For example: `apt-get install perun-slave-process-passwd` for installation of **passwd service**
  - For **all services**: `apt-get install perun-slave-meta`
