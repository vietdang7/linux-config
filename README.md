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
- Add custom with port 123 for UDP
- Add custom with port 80 for HTTP

### 9. Change local time to UTC
- `sudo dpkg-reconfigure tzdata` and select `None of above` and `UTC`

### 10. Install Apache2 and its configuration for mod_wsgi
- `sudo apt-get install apache2`
- Edit for WSGI module: `sudo nano /etc/apache2/sites-enabled/000-default.conf`, add this line `WSGIScriptAlias / /var/www/html/myapp.wsgi` before the closing tag </VirtualHost>

### 11. Install git to clone catalog application from github.com
- `sudo apt-get install git`

### 12. Install and enable mod_wsgi
- Run command: `sudo apt-get install libapache2-mod-wsgi python-dev`
- Enable mod_wsgi: `sudo a2enmod wsgi`

### 13. Create new folder, clone from github.com and wsgi configuration
- Move to: `cd /var/www`
- Make new folder named catalog: `sudo mkdir catalog` and move to: `cd catalog/`
- Clone the application from github.com under new folder 'catalog': `sudo git clone https://github.com/vietdang7/catalog.git catalog`
- Create 'catalog.wsgi': `sudo touch catalog.wsgi`
- Edit the file: `sudo nano catalog.wsgi` and add these line: `
#!/usr/bin/python
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0, "/var/www/catalog/")

from catalog import app as application
application.secret_key = 'supersecretkey'`
- Move to: `cd catalog/`
- Change name of 'catalog.py' to: `sudo mv catalog.py __init__.py`

### 14. Install virtualenv and Flask
- Install pip: `sudo apt-get install python-pip`
- Install virtualenv : `sudo pip install virtualenv`
- Create a new virtual environment: `sudo virtualenv venv`
- Active venv to install Flask: `source venv/bin/activate`
- Install Flask: `sudo pip install Flask`

### 15. Install other dependencies
- `sudo pip install httplib2 oauth2client sqlalchemy psycopg2 sqlalchemy_utils`
- `sudo pip install requests`
- `sudo pip install flask-seasurf`

### 16. Update link to client_secrets.json
- Edit the file: `sudo nano __init__.py`
- Change client_secrets.json to: `/var/www/catalog/catalog/client_secrets.json`

### 17. Config the virtual host
- Run command: `sudo nano /etc/apache2/sites-available/catalog.conf`
- Enable the virtual host `sudo a2ensite catalog`

### 18. Install and config PostgreSQL
- Run this command to install PostgreSQL: `sudo apt-get install postgresql`
- Change to user 'postgres': `sudo su - postgres`
- Run postgreSQL shell: `psql`
- Create table and user name: `CREATE DATABASE catalog;` and `CREATE USER catalog;`
- Create a password for user 'catalog': `ALTER ROLE catalog WITH PASSWORD 'password';`
- Give user 'catalog' permission to table 'catalog': `GRANT ALL PRIVILEGES ON DATABASE catalog TO catalog;`
- Run command to quit and exit: `\q` and `exit`

### 19. Correct link to database in __init__.py and database_setup.py
- Edit and change `engine = create_engine('sqlite:///catalog.db')` to `engine = create_engine('postgresql://catalog:password@localhost/catalog')`

