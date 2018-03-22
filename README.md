# Udacity-Linux-server-configuration
Submission


# Credentials

## SSH
ip: 18.197.120.34
password: grader
port: 2200

## HTTP

http://ec2-18-197-120-34.eu-central-1.compute.amazonaws.com/


# 1.  Update all currently installed packages
	sudo apt-get update        # Fetches the list of available updates
	sudo apt-get upgrade       # Strictly upgrades the current packages
	sudo apt-get dist-upgrade 
# 2. Change SSH Port 
`sudo nano /etc/ssh/sshd_config`

Set port to 2200, save and restart with

`sudo service sshd restart`

# 3. Configure Uncomplicated Firewall
	sudo ufw default deny incoming
	sudo ufw default allow outgoing
	sudo ufw allow 2200/tcp
	sudo ufw allow www
`	sudo ufw allow ntp
	sudo ufw enable
	
# 4. Create user grader 
	sudo createuser grader # create new user and add password
	sudo adduser grader sudo # add to sudo
	sudo su - grader # change to grader
	mkdir /home/grader/.ssh 
	touch /home/grader/.ssh authorited_keys  # paste public key here 
	chmod 700 .ssh
	chmod 644 .ssh/authorized_keys 

# 5. change timezone 
	sudo dpkg-reconfigure tzdata
	
Choose last option then choose UTC

# 6. install apache2  and mod_wsgi
	sudo apt-get install apache2 libapache2-mod-wsgi
	
# 7. Configure Postgresql
	sudo apt-get install postgresql

check remote connections on

	sudo nano /etc/postgresql/9.3/main/pg_hba.conf

source: https://www.digitalocean.com/community/tutorials/how-to-secure-postgresql-on-an-ubuntu-vps

change to postgres user and start

	sudo su - postgres
	psql

create db,user and passwort catalog (in psql console)

	CREATE DATABASE catalog;
	CREATE USER catalog;
	ALTER ROLE catalog WITH PASSWORD 'catalog';
	postgres=# GRANT ALL PRIVILEGES ON DATABASE catalog TO catalog;
	
quit with 

	\q
	
and exit to current user
	
	exit
	

# 8. install git 
	
	sudo apt-get install git
	
# 9. Deploy Catalog Project
My Catalog project was never uploaded on git. Feel free to use that submission.

## Create folder stucture
	
	sudo mkdir /var/www/FlaskApp
	sudo mkdir /var/www/FlaskApp/FlaskApp
	
## configure python project

Paste content of catalog Project into FlaskApp/FlaskApp
rename catalog.py into __init__.py

	sudo mv /var/www/FlaskApp/FlaskApp/catalog.py /var/wwww/FlaskApp/FlaskApp/__init__.py

Install pip and virtualenvironment

	sudo apt-get install python-pip
	pip install virtualenv

Setup virtual environment

	sudo virtualenv /var/www/FlaskApp/FlaskApp/venv
	source /var/www/FlaskApp/FlaskApp/venv/bin/activate
	sudo chmod -R 777  /var/www/FlaskApp/FlaskApp/venv  # allow installation of pip packages
	pip install flask sqlalchemy requests psycopg2
	pip install psycopg2-binary
	pip install --upgrade oauth2client

setup test data 

	sudo python /var/www/FlaskApp/FlaskApp/setup_database.py
	sudo python /var/www/FlaskApp/FlaskApp/create_data.py

deactivate venv afterwards 

	deactivate # leave virtualenvironment

source : https://github.com/google/oauth2client

	
Create wsgi file 
	
	sudo nano /var/www/FlaskApp/flaskapp.wsgi

And paste content 

	#!/usr/bin/python
	import sys
	import logging
	logging.basicConfig(stream=sys.stderr)
	sys.path.insert(0,"/var/www/FlaskApp/")

	from FlaskApp import app as application
	application.secret_key = 'super_secret_key'

#setup apache

Create config file
	
	sudo nano /etc/apache2/sites-available/FlaskApp.conf
	
Insert
	
	<VirtualHost *:80>
		ServerName 18.197.120.34
		ServerAdmin adminguy
		WSGIDaemonProcess FlaskApp python-path=/var/www/FlaskApp:/var/www/FlaskApp/FlaskApp/venv/lib/python2.7/site-packages
		WSGIProcessGroup FlaskApp
		WSGIScriptAlias / /var/www/FlaskApp/flaskapp.wsgi
		<Directory /var/www/FlaskApp/FlaskApp/>
			Order allow,deny
			Allow from all
		</Directory>
		Alias /static /var/www/FlaskApp/FlaskApp/static
		<Directory /var/www/FlaskApp/FlaskApp/static/>
			Order allow,deny
			Allow from all
		</Directory>
		ErrorLog ${APACHE_LOG_DIR}/error.log
		LogLevel warn
		CustomLog ${APACHE_LOG_DIR}/access.log combined
	</VirtualHost>

Enable Config
	
	sudo a2ensite FlaskApp

Restart Apache2
	
	sudo service apache2 restart 

	
