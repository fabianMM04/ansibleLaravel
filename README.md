# Ansible 
 

## 1. Install ansible Ubuntu
 
To configure the PPA on your machine and install ansible run these commands:
``` 
$ sudo apt-get install software-properties-common
$ sudo apt-add-repository ppa:ansible/ansible
$ sudo apt-get update
$ sudo apt-get install ansible
```

## 2. Remote connection with ansible
Ansible by default assumes you are using SSH keys.
* Generate a ssh key
```     
cd ~/.ssh
ssh-keygen -t rsa -b 2048
Generating public/private rsa key pair.
Enter a file in which to save the key(/c/Users/you/.ssh/id_rsa): key
Enter passphrase (empty for no passphrase): [Press enter]
Enter same passphrase again: [Press enter]
``` 
* Copy ssh key to servers
```
ssh-copy-id -i ~/.ssh/key.pub root@45.55.173.77->your_server_ip
ssh-copy-id -i ~/.ssh/key.pub root@45.55.92.89->your_server_ip
ssh-copy-id -i ~/.ssh/key.pub root@198.211.123.56->your_server_ip
```
# 3. Cloning a repository
```
cd /home
git clone   https://github.com/fabianMM04/ansible.git
``` 
 
## 4. edit ansible files
* Edit the ansible/hosts file, and enter the IP address of the server you are deploying to.
```
cd ansible
nano hosts

- hosts
[webservers]
45.55.173.77 -> your_server_ip_1
45.55.92.89-> your_server_ip_2
198.211.123.56-> your_server_ip_3
```
 
* Edit the ansible/group_vars/webservers.yml file, and enter the IP address of the server you are deploying to.
```
nano  group_vars/webservers.yml

- webservers.yml
ansible_user: root
ansible_ssh_private_key_file: ~/.ssh/key
server_hostname: 45.55.173.77-> your_server_ip_1
server_hostname_2: 45.55.92.89 -> your_server_ip_2
server_hostname_3: 198.211.123.56-> your_server_ip_3
``` 
 
* Edit the ansible/roles/nginx/templates/default.conf file,
```
nano roles/nginx/templates/default.conf
 
- default.conf
# Handle requests to server_hostname on port 80
server {
       listen 45.55.173.77:80 -> your_server_ip_1;
       server_name {{ server_hostname }};
               # Handle all locations
       location / {
                       # Pass the request to Gunicorn
               proxy_pass http://0.0.0.0:8000;
               # Set some HTTP headers so that our app knows where the
               # request really came from
               proxy_set_header Host $host;
               proxy_set_header X-Real-IP $remote_addr;
               proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
       }
}
 
 
# Handle requests to server_hostname_2 on port 80
server {
       listen 45.55.92.89:80 -> your_server_ip_2;
       server_name {{ server_hostname_2}};
               # Handle all locations
       location / {
                       # Pass the request to Gunicorn
               proxy_pass http://0.0.0.0:8000;
               # Set some HTTP headers so that our app knows where the
               # request really came from
               proxy_set_header Host $host;
               proxy_set_header X-Real-IP $remote_addr;
               proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
       }
}
 
 
# Handle requests to server_hostname_3 on port 80
server {
    	listen 198.211.123.56:80;
    	server_name {{ server_hostname_3 }};
 
            	# Handle all locations
    	location / {
                    	# Pass the request to Gunicorn
            	proxy_pass http://0.0.0.0:8000;
 
            	# Set some HTTP headers so that our app knows where the
            	# request really came from
            	proxy_set_header Host $host;
            	proxy_set_header X-Real-IP $remote_addr;
            	proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    	}
}
```	
## 5. Run the playbook:
```
ansible-playbook -i hosts site.yml -K
```
## 6. Go to
```
http://45.55.173.77/     ->your_server_ip_1
http://45.55.92.89/       ->your_server_ip_2
http://198.211.123.56/  ->your_server_ip_3
```
