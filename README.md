Zabbix Server and agent
=======================

This roles helps to install zabbix Server and agent across RHEL and Ubuntu variants.
Apart from installing the zabbix server or agent, it install either mariadb or psgrel 
and apply basic hardening, like securing the root account with password, and 
removing test databases. The role can also be used to add databases to the 
MySQL server and create users in the database.

Requirements
------------

This role requires Ansible 1.4 or higher, and platform requirements are listed
in the metadata file.

Role Variables
--------------

Variable used to customized the installaton

Zabbix
```
zabbix_version: 3.4 # Zabbix version

## Database connection ##
#
db_backend: mysql     #Backend - it is posible to use mysql(mariadb) and postgrel
zabbix_db: zabbix     # Zabbix database name
zabbix_user: zabbix   # Zabbix database user user
zabbix_pass: zabbix   # Zabbix databse user password

```
see https://github.com/Sifungurux/ansible-mysql
For mysql variables

NB: at the moment it is need to set password two places

The variables that can be passed to this role and a brief description about
them are as follows:

Add role with
```
    - ansible-zabbix
```
Install zabbix play
*******************
```
---
- hosts: PRODUCTION:&ZABBIX_SERVER
  become: yes
  become_method: sudo
  gather_facts: yes
  roles:
    - ansible-zabbix
## Zabbix db variables are set in default/main.yml ##
## Declare them local in play here by add
    - mysql_db: zabbix
    - mysql_users: zabbix
    - zabbix_pass: ****
``` 

Add screen with from playbook
*****************************
```
---
- hosts: PRODUCTION
  become: yes
  become_method: sudo
  gather_facts: yes
  vars:
    config_screens:
      admin: "admin"
      pass: "zabbix"
      screen_name: "test"
      host_group: "Linux servers"
      graph:
        - "CPU load"
        - "CPU jumps"
  roles:
    - ansible-zabbix

``` 

Installing agent from commandline
---------------------------------

Running this command will install zabbix agent.
```
ansible-playbook zabbix.yml -i inventory --tags "repo, agent.install" -l zabbix-agent
```
Install userparameters for the zabbix agent
------------------------------------------
```
ansible-playbook zabbix.yml -i inventory --tags "userparam" -l zabbix-agent --extra-vars '{"userparam_dir":"/userparm_dir", "userperm":[{"name": "test", "script":"test.conf"},{ "name":"test2", "script":"test2.conf"}]}'
```
Creating, updating and deleting screens
---------------------------------------
```
Creating a screen without graphs - POC
ansible-playbook zabbix.yml -i inventory --tags "config_screen" -l zabbix-server --extra-vars '{"config_screens":[{"admin": "admin", "pass":"zabbix", "screenname":"test", "host_group":"Linux servers"}]}'
```

Creating a screen with multiple graphs e.g the 'CPU load' and the 'CPU jumps'
```
ansible-playbook zabbix.yml -i inventory --tags "config.screen" -l zabbix-server --extra-vars "{'config_screens':[{'admin': 'admin', 'pass':'zabbix', 'screen_name':'test2', 'host_group':'Linux servers', 'graph':'[\"CPU load\", \"CPU jumps\"]'}]}"
```

```
 ansible-playbook zabbix.yml -i .vagrant/provisioners/ansible/inventory/vagrant_ansible_inventory --tags "agent.install"
 ```

 Install proxy
 installing proxy will reload config on server in chosen group. Change group to affect in zabbix_proxy.yml

 ansible-playbook zabbix_proxy.yml -i .vagrant/provisioners/ansible/inventory/vagrant_ansible_inventory --tags "mysql, proxy.install"


