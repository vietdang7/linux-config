# Linux Server Configuration 
Linux Server Configuration for a Python Web Application . This is the final project of part 5 -  Udacity Fullstack Nano Degree

## System Information
- Virtual Machine (VM) is created by using Amazon LightSail. VM is running Ubuntu 16.04.2 LTS
- Public IP: 18.196.4.252
- Private Key (not shared)

## Prerequisites
You need to set up an Amazon LightSail, create an Ubuntu instance and download Private Key from your account page.

## Project Tasks
### 1. Connect to VM on Amazon LightSail
- In you terminal, move your Private Key to .ssh folder: 
`mv ~/Downloads/PrivateKey.pem ~/.ssh/`
- Change permission of Private Key to 600: 
`chmod 600 ~/.ssh/PrivateKey.pem`
- Connect to VM: `ssh -i ~/.ssh/PrivateKey.pem ubuntu@18.196.4.252`

### 2. Create user named 'grader'
- Install `finger` to check user information later on: `sudo apt-get install finger`
- Create user named 'grader': `sudo adduser grader`
- See information of user named grader: `finger grader`

### 3. Change permission of grader
- Create and edit grader file in `sudoers.d`: `sudo nano /etc/sudoers.d/grader`
- Add this line: `grader ALL=(ALL:ALL) ALL`
- Confirm the new permission by typing some `sudo` commands

### 4. Generate ssh keys for grader
- On your local machine, run `ssh-keygen`, enter file's location and password. You will have two keys (example: grader and grader.pub)
- Read the key grader.pub by command: `cat .ssh/grader.pub`, copy the content

### 5. Add ssh key to grader
- Switch to grader by command: `su - grader`
- Make .ssh directory: `mkdir .ssh`
- Create authorized_keys file: `touch .ssh/authorized_keys`
- Open the file with nano: `nano .ssh/authorized_keys` and paste the content of grader.pub key
- Change folder and file permission: `chmod 700 .ssh` `chmod 644 .ssh/authorized_keys`
- Restart ssh service: `sudo service ssh restart`

### 6. Update installed software
- Get the latest list of package: `sudo apt-get update`
- Install newest package: `sudo apt-get upgrade`

### 7. Disable root login and change ssh port to 2200
- Edit the file: `sudo nano /etc/ssh/sshd_config`
- Change port from 22 to 2200
- Edit `PermitRootLogin without-password` to `PermitRootLogin no`

### 8. Configure UFW for ssh, http and ntp
- Check the status of firewall: `sudo ufw status`
- Block all incoming traffic for maximum security: `sudo ufw default deny incoming`
- Allow outgoing: `sudo ufw default allow outgoing`
- Allow incoming ssh connection (port 2200): `sudo ufw allow 2200/tcp`
- Allow incoming http connection (port 80): `sudo ufw allow 80/tcp`
- Allow incoming ntp connection (port 123): `sudo ufw allow 123/udp`
- Enable UFW: `sudo ufw enable`
- Login to your Amazon LightSailt > select your instance > Networking > Firewall > Add custom with port 2200
