# Ansible Set Up Process:
```
1 Create 3 AWS ubuntu 18 instances
2 Name the 1st one as controller (control node or Master server) and remaining 2 as server1 and server2 as manage nodes
3 Establish Passwordless ssh from Controller(master server) to Server1 and Server2
  A.) Connect to server1
  B.) Setup password for the default user
     sudo passwd ubuntu
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

Ansible stores all the remote servers info in a file called as inventory file We should open this file and store the ip address of all the managed nodes here
sudo vim /etc/ansible/hosts
Here copy and paste the ip addresses of the managed nodes
```

