# A. Steps to deploy a Linux Server
Based on [Python Flask Tutorial: Deploying Your Application (Option #1) - Deploy to a Linux Server](https://www.youtube.com/watch?v=goToXTC96Co&list=PL-osiE80TeTs4UjLw5MM6OjgkjFeUxCYH&index=13) 

by Corey Schafer (0:00:00 -> 0:26:48)

## 1. Create a cloud virtual machine (VM)
For instance at Linode or AWS

Linode Label: flask-server

It's recommended to use linux bash

## 2. Config VM
### 2.1 (LOCAL) Access virtual machine
`ssh root@xx.xx.xx.xx`

Enter root password

### 2.2 (SERVER) Update and upgrade
`apt update && apt upgrade`

- update - update list of available packages
- upgrade - upgrade the system by installing/upgrading packages 

* *** System restart required *** might need reboot through linode site

### 2.3 (SERVER) Set host name
`hostnamectl set-hostname flask-server`

Check by typing `hostname`
Should return `flask-server`

### 2.4 (SERVER) Include our virtual machine IP in hosts
`nano /etc/hosts`
Include ip 45.79.xx.xx in the list

### 2.5 (SERVER) Create a non-root user with limited priveleges because its safer
`adduser loureiro`

Enter new password

### 2.6 (SERVER) Add new user to sudo group
`adduser loureiro sudo`

### 2.6 (SERVER) Logout from root user and (LOCAL) login as new user
`exit`
`ssh loureiro@45.xx.xx.xx`

## 3. Set SSH Key-based authentication 
Why?
- To login to our server without a password
- This is more secure and convenient because a key can't be brute forced (a password can)
- It's great to connect with remote scripts that connect to your web server

### 3.1 (SERVER) Create .ssh folder
`mkdir .ssh`

### 3.2 (LOCAL) On local bash, Generate a SSH key
`ssh-keygen -b 4096`
Use default filename

### 3.3 (LOCAL) Copy key to server as file authorized_keys
`scp ~/.ssh/id_rsa.pub loureiro@45.xx.xx.xx:~/.ssh/authorized_keys`

### 3.4 (SERVER) Change permissions of .ssh folder and files
`sudo chmod 700 ~/.ssh/` Owner permission for the directory: read, write and execute
`sudo chmod 600 ~/.ssh/*` Owner permission for the files: read and write

`sudo systemctl reboot`
### 3.5 Exit and test access without password
`exit`
`ssh loureiro@xx.xx.xx.xx`

### 3.6 (SERVER) Disallow root logins over SSH 
`sudo nano /etc/ssh/sshd_config`

change `PermitRootLogin yes` to `PermitRootLogin no`
change `#PasswordAuthentication yes` to `PasswordAuthentication no`

Why?
- To avoid hackers brute-force the password

From now on it'll be impossible to authenticate through password.

### 3.7 (SERVER) Restart SSH service 23:55
`sudo systemctl restart sshd`

## 4. (SERVER) Set up Firewall
### 4.1 Install Uncomplicated FireWall
`sudo apt install ufw`

### 4.2 Allow outgoing traffic
`sudo ufw default allow outgoing`

### 4.3 Deny incoming traffic
`sudo ufw default deny incoming`

### 4.4 Allow rules for SSH and port 5000
`sudo ufw allow ssh`

`sudo ufw allow 5000`

### 4.5 Enable previous configurations
`sudo ufw enable`

### 4.6 View firewall status
`sudo ufw status`

Expected print:
```
Status:active                                                                                                                                       
To                         Action      From
--                         ------      ----                                                                                            
22/tcp                     ALLOW       Anywhere
5000                       ALLOW       Anywhere
22/tcp (v6)                ALLOW       Anywhere (v6)
5000 (v6)                  ALLOW       Anywhere (v6) 
```
___




# B. Deploy web-application (local -> server)

## 1. (LOCAL) Generate requirements.txt
- `pip freeze > requirements.txt`

## 2. (LOCAL) Upload application to server
- Move with scp
- [](https://superuser.com/questions/1066261/how-to-access-windows-folders-from-bash-on-ubuntu-on-windows)
- `cd /mnt/c/Users/loure/sandbox/flask-01` access local application
- `scp -r flask-01 loureiro@xx.xx.xx.xx:~/` upload application to server
- Check if the application directory was uploaded (SERVER) `ls`

## (SERVER) 3. Set up Virtual Environment
### 3.1 Install Pip with Python
`sudo apt install python3-pip`

### 3.2 Install Virtual Environment with Python
`sudo apt install python3-venv` 

### 3.3 Create Virtual Environment
`python3 -m venv flask-01/venv`
Check for venv directory

### 3.4 Activate venv
`source venv/bin/activate`

Notice you're now inside the (venv)
(venv) loureiro@flask-server:~/flask-01$   

### 3.5 (SERVER VENV) Install requirements in the venv (=~34:00)
`pip install -r requirements.txt`


```
error: invalid command 'bdist_wheel' 

Failed building wheel for autopep8   
Failed building wheel for blinker 
Failed building wheel Flask-Bcrypt 
Failed building wheel Flask-Mail
Failed building wheel wrapt
Failed building wheel contextvars 

```


## Create config

### (LOCAL) Grab environment variables
```
import os
os.environ.get('SECRET_KEY')
os.environ.get('SQLALCHEMY_DATABASE_URI')
os.environ.get('EMAIL_USER')
os.environ.get('EMAIL_PASS')
```

### (SERVER) Create config file
`sudo touch /etc/config.json`
`sudo nano /etc/config.json`


```
config.json = {
    "SECRET_KEY": "2041axxxxx",
    "SQLALCHEMY_DATABASE_URI": "sqlite:///site.db",
    "EMAIL_USER": "xxxxx@gmail.com",
    "EMAIL_PASS": "xxxxx"
}
```

minute 43

edit config.py with nano

`flask run --host=0.0.0.0`

`sudo apt install nginx`
`pip install gunicorn`
`sudo rm /etc/nginx/sites-enabled/default`

`sudo nano /etc/nginx/sites-enabled/flaskblog`

```
server {
...
}
```

`sudo ufw allow http/tcp`
`sudo ufw delete allow 5000`
`sudo ufw enable`
`sudo systemctl restart nginx`
`nproc --all`
`gunicorn -w 3 run:app`
`sudo apt install supervisor`
`sudo nano /etc/supervisor/conf.d/flaskblog.conf`
`sudo supervisorctl reload`


