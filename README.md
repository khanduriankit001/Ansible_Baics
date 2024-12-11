# Ansible Installation & Configurations

# 1. Update Your System
Before installing Ansible, it's essential to update your package list to ensure you have the latest software. </br>

- **For Debian/Ubuntu**:

```
  sudo apt update
  sudo apt upgrade -y
```

- **For RHEL/CentOS**:
```
  sudo yum update -y
```

# 2. Install Ansible

- **On Debian/Ubuntu**:
  Ansible is available via the official package repository. Install it using the following commands. </br>

```
  sudo apt install software-properties-common -y
  sudo add-apt-repository --yes --update ppa:ansible/ansible
  sudo apt install ansible -y
```

- **On RHEL/CentOS**:
  On RHEL/CentOS, you can install Ansible by enabling the EPEL repository:

```
  sudo yum install epel-release -y
  sudo yum install ansible -y
```

# 3. Verify Installation
After the installation is complete, verify it by checking the version of Ansible installed. </br>
```
  ansible --version
```

 # 4. You should see output similar to

```
  ubuntu@ip-172-31-4-74:~$ ansible --version
ansible [core 2.17.7]
  config file = /etc/ansible/ansible.cfg
  configured module search path = ['/home/ubuntu/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/lib/python3/dist-packages/ansible
  ansible collection location = /home/ubuntu/.ansible/collections:/usr/share/ansible/collections
  executable location = /usr/bin/ansible
  python version = 3.12.3 (main, Sep 11 2024, 14:17:37) [GCC 13.2.0] (/usr/bin/python3)
  jinja version = 3.1.2
  libyaml = True
ubuntu@ip-172-31-4-74:~$

```
---

# Ansible Controller Managed-Nodes Architecture Implementation!

![Ansible Controller Managed-Nodes](https://raw.githubusercontent.com/Skchoudhary/blog-asset/master/dgplug-blog/ansible-arch.png)

# 1. Configure Ansible & Set Up AWS Environment
  Create three EC2 instances on AWS cloud. Ensure you have an SSH key pair to access them, assign a security group that allows SSH (port 22) and HTTP (port 80). </br>

- **Controller Node**: The machine where Ansible is installed and from where tasks are executed.</br></br>
- **Managed Node-1**: The machines that the controller manages and configures. This will be the first target node.</br></br>
- **Managed Node-2**: This will be the second target node.</br></br>
- **Configure Security Groups**: Make sure the security groups for the slave servers allow traffic from the master server, especially on SSH (port 22) and HTTP (port 80).</br></br>

# 2. Install and Configure Ansible on the Master Server

- **SSH into the Master Server**:

```
 ssh -i "ansible-master-key.pem" ubuntu@ec2-public-ip.ap-south-1.compute.amazonaws.com
```

- **Update the System**:

```
  sudo apt update
```

- **Install Ansible on Ubuntu**:

```
  sudo apt install software-properties-common -y
  sudo add-apt-repository --yes --update ppa:ansible/ansible
  sudo apt install ansible -y
```
- **Verify Installation**:

```
  ansible --version
```

- **run command ssh-keygen**

```
  ssh-keygen
```

- **go to the publickey file**:

```
ubuntu@ip-172-31-4-74:~$ ssh-keygen
Generating public/private ed25519 key pair.
Enter file in which to save the key (/home/ubuntu/.ssh/id_ed25519):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/ubuntu/.ssh/id_ed25519
Your public key has been saved in /home/ubuntu/.ssh/id_ed25519.pub
The key fingerprint is:
SHA256:iuiZ56k1lG/LaUbEZN44RBo1/pPb6HFQCX2Ou9/j5SU ubuntu@ip-172-31-4-74
The key's randomart image is:
+--[ED25519 256]--+
|    .o+ ..       |
|     ++. ....    |
|    .*.o  o+     |
|     .*..o. .    |
|    o. .S  .     |
|   o o.. *.      |
|  . +.+ + o.  E o|
| . +.=o+ o.  ..+.|
|  =+oo+ .  ...o..|
+----[SHA256]-----+
ubuntu@ip-172-31-4-74:~$ cd ~/.ssh/
ubuntu@ip-172-31-4-74:~/.ssh$ ls
authorized_keys  id_ed25519  id_ed25519.pub
ubuntu@ip-172-31-4-74:~/.ssh$
ubuntu@ip-172-31-4-74:~/.ssh$ ls
authorized_keys  id_ed25519  id_ed25519.pub  
```

- **copy the data from public key for passwordless auth:**

``
ubuntu@ip-172-31-4-74:~/.ssh$ cat id_ed25519.pub
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAILM6ozopf7Gd4PcKYFKNm4loj3SjPlm8FurruiH1Imwm ubuntu@ip-172-31-4-74
ubuntu@ip-172-31-4-74:~/.ssh$

```
**paste this key in the slave/serving node authorized_keys**:

--- 
go to authorized_keys of your serving node and paste it there 

ubuntu@ip-172-31-11-43:~$ ssh-keygen
Generating public/private ed25519 key pair.
Enter file in which to save the key (/home/ubuntu/.ssh/id_ed25519):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/ubuntu/.ssh/id_ed25519
Your public key has been saved in /home/ubuntu/.ssh/id_ed25519.pub
The key fingerprint is:
SHA256:Hu+TBk32wpM/mjDHiXKR1osUNnZfzHDxFEuFj7N/Zuc ubuntu@ip-172-31-11-43
The key's randomart image is:
+--[ED25519 256]--+
|            . o==|
|             =oo.|
|        = .   ++.|
|       o *o. .o .|
|        S=.o.  o |
|       +.B*o. .  |
|      . B.*=   . |
|       o =+.o   *|
|         .+o . +E|
+----[SHA256]-----+
ubuntu@ip-172-31-11-43:~$ cd ~/.ssh
ubuntu@ip-172-31-11-43:~/.ssh$ ls
authorized_keys  id_ed25519  id_ed25519.pub
ubuntu@ip-172-31-11-43:~/.ssh$ vi authorized_keys


ssh the serving node ip from the master node 
ubuntu@ip-172-31-4-74:~$ ssh ubuntu@13.235.71.171
Welcome to Ubuntu 24.04.1 LTS (GNU/Linux 6.8.0-1018-aws x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

 System information as of Wed Dec 11 10:03:12 UTC 2024

  System load:  0.0               Processes:             110
  Usage of /:   34.6% of 6.71GB   Users logged in:       1
  Memory usage: 27%               IPv4 address for enX0: 172.31.11.43
  Swap usage:   0%


Expanded Security Maintenance for Applications is not enabled.

46 updates can be applied immediately.
25 of these updates are standard security updates.
To see these additional updates run: apt list --upgradable

Enable ESM Apps to receive additional future security updates.
See https://ubuntu.com/esm or run: sudo pro status


Last login: Wed Dec 11 09:40:26 2024 from 49.47.68.187

---

 ### 3. Set Up Ansible Inventory

 - **Edit Inventory File**:
  The Ansible hosts file **(/etc/ansible/hosts)** is where you define your inventory of servers.

- **Open the hosts file**:

```
  sudo vim /etc/ansible/hosts
```

- **Add your servers under a specific group**:

```
  [servers]
  12.112.79.285
  12.143.270.190

```

- **Verify Ansible inventory list**:

```
  ansible-inventory --list
```

- **Test Connectivy**:
  Ping all the nodes from the controller without needing a password.

```
  ansible -m ping servers
```

- **To check RAM & ROM**:
  To check RAM & ROM in the all nodes from controller using a ad-hoc commands.

```
  ansible -a "free -h" servers # to check RAM
  ansible -a "df -h" servers   # to check ROM
```

- **To update of all servers**:
  If you want to update the nodes system from the controller use the below ad-hoc command.

```
  ansible -a "sudo apt-get update" servers
```

- **To check uptime of all servers**:
  If you want to check uptime of the all nodes from the controller use the below ad-hoc command.

```
  ansible -a "uptime" server
```

 ### 4. Write an Ansible Playbook to Install NGINX

 - **Create the Playbook File**:
   Create a directory for your Ansible project and a playbook file.

```
  mkdir ~/ansible-projects
  cd ~/ansible-projects
  vi install_nginx.yml

```

- **Write the Playbook (install_nginx.yaml)**:

```
---
- name: This playbook will install nginx
  hosts: servers
  become: yes  # to use sudo
  tasks:
    - name: Install nginx
      apt:
        name: nginx
        state: latest

    - name: Start and enable nginx service
      service:
        name: nginx
        state: started
        enabled: yes
```

 - **Run the Ansible Playbook**:
   Run the playbook to install and start nginx webserver on both slave servers.

```
  ansible-playbook -i /etc/ansible/hosts install_nginx.yml
```

 ### 5. Verify the Set Up

 - **Check NGINX installation on Slave Servers**:
   Open your web browser and navigate to the public IP of both slave servers.

```
  http://12.112.79.285:80
  http://12.143.270.190:80
```

- **Note:- You should see the default NGINX welcome page**

---
