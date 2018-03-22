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
