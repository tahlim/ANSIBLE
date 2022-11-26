# Ansible Set Up Process:
```
1 Create 3 AWS ubuntu 18 instances
2 Name the 1st one as controller (control node or Master server) and remaining 2 as server1 and server2 as manage nodes
3 Establish Passwordless ssh from Controller(master server) to Server1 and Server2
  A.) Connect to server1
  B.) Setup password for the default user
     sudo passwd ubuntu
     
     Note: if you want to make communication between "ansible" user then use below commands
     
     Connect to Controller node (master server) then run below command
     sudo adduser ansible
     sudo passwd ansible
     sudo su - ansible
     
     Connect to manage node (Server1, Server2) then run below command
     sudo adduser ansible
     sudo passwd ansible
     sudo su - ansible
     
     Connect to Controller node (master server) and Generate the ssh keys
     sudo su - ansible
     ssh-keygen
     Copy the ssh keys
     ssh-copy-id ansible@private_ip_of_server1
     ssh sudo su - ansible
     
  C.) Edit the ssh configuration file
     sudo vim /etc/ssh/sshd_config
     Search for "PasswordAuthentication" and change it from no to yes
  D.) Restart ssh
     sudo service ssh restart
	 
    Note: Repeat the above steps from A. to D. on Server2 managed node also 
	
  E.) Now Connect to Controller(master server)
  F.) Generate the ssh keys
     ssh-keygen
  G.) Copy the ssh keys
     ssh-copy-id ubuntu@private_ip_of_server1
     Repeat step G. with ip address of Server2

4 Installing Ansible

Note: It will only run on controller node

  a) Update the apt repository
     sudo apt-get update
  b) Install software-properties-common
     sudo apt-get install -y software-properties-common
  c) Add the latest version of Ansible to apt repository
     sudo apt-add-repository ppa:ansible/ansible
  d) Update the apt repository
     sudo apt-get update
  e) Install ansible
     sudo apt-get install -y ansible

5 To check the version of ansible
  ansible --version

6 Ansible stores all the remote servers info in a file called as inventory file We should open this file and 
  store the ip address of all the managed nodes here

sudo vim /etc/ansible/hosts
Here copy and paste private ip addresses of the managed nodes

[webserver]
172.31.6.221

Note: vim /etc/ansible/ansible.cfg    ## We need to enable inventory and sudo user path from this conf file

7 Testing connectivity to the servers:
now you can ping and list the remote server ips by running below commands
ansible all --list-hosts
ansible webserver --list-hosts

ansible -m ping all
ansible -m ping webserver
```

Note: Grouping in inventory file if you want to do.

sudo vim /etc/ansible/hosts
```
[webserver]
172.31.56.218
172.31.51.60
[appserver]
172.31.45.55
[dbserver]
172.31.44.34
172.31.51.60
[servers:children]
appserver
dbserver
```
# ======================================
# Ansible perform remote configuration of servers in 3 ways
- 1 Ansible Ad Hoc command
- 2 Ansible Playbooks
- 3 Ansible Roles
# 1. Syntax of Ad Hoc command
ansible all/groupname/ipaddress -i /etc/ansible/hosts -m module_name -a  '    '

# Command module
```
- Ansible command to see the memory info of all managed nodes
- ansible all -i /etc/ansible/hosts -m command -a 'free'
- /etc/ansible/hosts is the default inventory file and when using it is not necessary to use -i option
- ansible all  -m command -a 'free'
- Command module is the default module of ansible and when using it we need not give -m option
- ansible all -a 'free'
```
# Copy Module
```
Ansible command to copy a file into all managed nodes
ansible all -m copy -a 'src=file1 dest=/tmp'

Copy moulde can also copy directories along with their sub directories
1 Create a chain of directories
  mkdir -p d1/d2/d3/d4

2 Copy this entire directory structure into the managed nodes
  ansible all -m copy -a 'src=d1 dest=/home/ubuntu'

Copy module can also change the ownership group ownership and permissions of files
ansible all -m copy -a 'src=file1 dest=/tmp owner=Sai group=Ravi mode=760' -b

Copy module can also send some content into a file on the managed nodes
ansible all -m copy -a 'content="Hello IntelliQUIt\n" dest=/tmp/file1' -b
```
# ========================================
# 2. Ansible Playbooks
- Ansible playbook to create a file
vim playbook1.yml
```
---
- name: Create a file
  hosts: all
  tasks:
   - name: Creating a file on servers
     file:
      name: /tmp/file100
      state: touch
...
```
- To check if the above playbook is syntactically correct
- ansible-playbook playbook1.yml --syntax-check
- To execute the playbook
- ansible-playbook playbook1.yml
======================================================
- Ansible playbook to configure apache2
vim playbook2.yml
```
---
- name: Configuring apache2
  hosts: all
  tasks:
   - name: Install apache2
     apt:
      name: apache2
      state: present
   - name: Edit the index.html file
     copy:
      content: "Welcome IntelliQIT\n"
      dest: /var/www/html/index.html
   - name: Restart apache2
     service:
      name: apache2
      state: restarted
   - name: Check url response of server1
     uri:
      url: http://172.31.83.146
      status_code: 200
   - name: Check the url response of server2
     uri:
      url: http://172.31.86.204
      status_code: 200
```
- ansible-playbook playbook2.yml
