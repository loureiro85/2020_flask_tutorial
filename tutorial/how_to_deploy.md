# Steps to deploy a Linux Server
Based on (Python Flask Tutorial: Deploying Your Application (Option #1) - Deploy to a Linux Server)[https://www.youtube.com/watch?v=goToXTC96Co&list=PL-osiE80TeTs4UjLw5MM6OjgkjFeUxCYH&index=13] by Corey Schafer

It's recommended to use linux bash

## Access virtual machine
`ssh root@45.xx.xx.xx`

Enter root password

## Update and upgrade
`apt update && apt upgrade`

- update - update list of available packages
- upgrade - upgrade the system by installing/upgrading packages 

* *** System restart required *** might need reboot through linode site

## Set host name
`hostnamectl set-hostname flask-server`

Check by typing `hostname`
Should return `flask-server`

## Include our virtual machine IP in hosts
`nano /etc/hosts`

## Create a non-root user with limited priveleges because its safer
`adduser loureiro`

Enter new password

## Add new user to sudo group
`adduser loureiro sudo`

## Logout from root user and login as new user
`exit`
`ssh loureiro@45.xx.xx.xx`

## Set SSH Key-based authentication 
Why?
- To login to our server without a password
- This is more secure and convenient because a key can't be brute forced (a password can)
- It's great to connect with remote scripts that connect to your web server

## Create .ssh folder
`mkdir .ssh`

## On local bash, Generate a SSH key
`ssh-keygen -b 4096`

## Copy key to server as file authorized_keys
`scp ~/.ssh/id_rsa.pub loureiro@45.xx.xx.xx:~/.ssh/authorized_keys`

## Change permissions of .ssh folder and files
`sudo chmod 700 ~/.ssh/`
`sudo chmod 600 ~/.ssh/*`

## Exit and test access without password
`exit`
`ssh loureiro@45.xx.xx.xx`

## Disallow root logins over SSH 
`sudo nano /etc/ssh/sshd_config`

change `PermitRootLogin yes` to `PermitRootLogin no`
change `#PermitRootLogin yes` to `PermitRootLogin no`

Why?
- To avoid hackers brute-force the password

From now on it'll be impossible to authenticate through password.

## Restart SSH service
`sudo systemctl restart sshd`