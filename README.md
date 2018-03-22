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
