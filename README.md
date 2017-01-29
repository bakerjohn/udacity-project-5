Configuring Linux Servers Course
IP address of server 35.164.246.46 port 2200
Complete url http://ec2-35-164-246-46.us-west-2.compute.amazonaws.com/



1. Create the virtual machine
1.  Create new development environment
2.  Downloand the Private Key from the udacity development environment website
3.  Move the private key file into the folder ~/.ssh 
    (where ~ is your environment's home directory). 
4.  Open Git Bash as your terminal and type in
    $ chmod 600 ~/.ssh/udacity_key.rsa
5.  SSH (connect) to the instance:
    $ ssh -i ~/.ssh/udacity_key.rsa root@ 35.164.246.46

2. Create a new User named 'grader' and grant 'grader' sudo permissions 
https://www.digitalocean.com/community/tutorials/how-to-add-and-delete-users-on-an-ubuntu-14-04-vps

1.	sudo adduser grader
2.	 password is grader
3.	Edit a new file under the sudoers.d directory
4.	sudo nano /etc/sudoers.d/grader
5.	add the following line to the file
6.	grader ALL=(ALL:ALL) ALL
7.	Save the file
5 - Update and upgrade all currently installed packages
http://askubuntu.com/questions/94102/what-is-the-difference-between-apt-get-update-and-upgrade

1.	Update packages
2.	Sudo apt-get update
3.	Install newest versions of packages
4.	Sudo apt-get upgrade


3. Create SSH key authentication for grader 

1.	On the local machine Created public and private key pair for grader
2.	Switch to user grader
	su grader
3.	move to the grader home directory
4.	cd
5.	create a new directory called .ssh
6.	mkdir .ssh
7.	create a new file called authorized_keys
8.	touch .ssh/authorized_keys
9.	move to the .ssh directory
10.	cd .ssh
11.	open the authorized_keys file
12.	sudo nano authorized_keys
13.	paste the public key you created on your local machine in the authorized_keys file
14.	save and exit
15.	move back to the home directory
16.	cd
17.	set permissions on the .ssh file so the owner can read/write/execute
18.	chmod 700 .ssh
19.	set permissions on the authorized_keys file so the owner can read and write
20.	chmod 644 .ssh/authorized_keys
21.	exit
22.	open a new terminal and log in with user grader

4 - Change the SSH port from 22 to 2200 and configure SSH access
http://askubuntu.com/questions/16650/create-a-new-ssh-user-on-ubuntu-server

1.	    open the /etc/ssh/sshd_config file
2.	sudo nano /etc/ssh/sshd_config
3.	change the ssh port from 22 to 2200
4.	change PermitRootLogin from without-password to PermitRootLogin no
5.	sudo service ssh reload



6.	Remove the “unable to resolve host” warning
http://askubuntu.com/questions/59458/error-message-when-i-run-sudo-unable-to-resolve-host-none

6.	Configure the uncomplicated firewall to only allow incoming connections for SSH, HTTP, and NTP
https://help.ubuntu.com/community/UFW

1.	Turn on UFW
2.	sudo ufw enable
3.	Allow incoming traffic on port 2200(SSH)
4.	sudo ufw allow 2200/tcp
5.	Allow incoming traffic on port 80
6.	sudo ufw allow 80/tcp
7.	Allow incoming traffic on port 123/udp
8.	sudo ufw allow 123/udp
9.	Verify the firewall status
10.	sudo ufw status verbose
7.          Configure firewall to monitor for repeated unsuccessful login attempts and ban attackers
1.	$ sudo apt-get install fail2ban
2.	 send the alerts to the admin user:
3.	 $ sudo apt-get install sendmail
4.	Create a file to safely customize the fail2ban functionality: 
5.	$ sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
6.	Open the jail.local and edit it: 
7.	$ sudo nano /etc/fail2ban/jail.local. 
8.	Set the destemail field to admin user's email address.

8.	Update currently installed packages

1.	sudo apt-get update
2.	sudo apt-get upgrade
	
   9.  Configure the local timezone to UTC
1. sudo dpkg-reconfigure tzdata
2. scroll to the bottom of the Continents list and select None of the Above; in the second list, select UTC 

10. Autmatically manage updates
	1. sudo dpkg-reconfigure --priority=low unattended-upgrades




11. Install Apache mod.wsgi
1. sudo apt-get install apache2
2. sudo apt-get install libapache2-mod-wsgi python-dev
3. sudo a2enmod wsgi
4. sudo service apache2 start

Remove message"Could not reliably determine the servers's fully qualified domain name" after restart Source: http://askubuntu.com/questions/256013/apache-error-could-not-reliably-determine-the-servers-fully-qualified-domain-n


12.	 Create an empty Apache config file with the hostname:

1. echo "ServerName HOSTNAME" | sudo tee /etc/apache2/conf-available/fqdn.conf
2.	Enable the new config file
3.	Sudo a2enconf fqdn


        12. Setup the apache/flask application
https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps

1.	Move to the /var/www directory
2.	cd /var/www
3.	create a new directory called catalog
4.	mkdir catalog
5.	move inside the catalog directory
6.	cd catalog
7.	create another directory called catalog
8.	sudo mkdir catalog
9.	move inside the catalog directory
10.	cd catalog
11.	create a static and a templates directory
12.	 sudo mkdir static templates
13.	Create the application file
14.	sudo nano __init__.py
15.	copy the following code

from flask import Flask
app = Flask(__name__)
@app.route("/")
def hello():
    return "Hello, I love Digital Ocean!"
if __name__ == "__main__":
    app.run()

16.	save and exit

23.	Install Flask and the virtual environment
1.	Install pip installer:
sudo apt-get install python-pip
2.	Install virtualenv:
sudo pip install virtualenv
3.	Set virtual environment to name 'venv':
sudo virtualenv venv
4.	Enable all permissions for the new virtual environment (no sudo should be used within):
Source: Stackoverflow
sudo chmod -R 777 venv
5.	Activate the virtual environment:
source venv/bin/activate
6.	Install Flask inside the virtual environment:
pip install Flask
7.	Run the app:
python __init__.py
8.	It should display “Running on http://localhost:5000/” or "Running on http://127.0.0.1:5000/". If you see this message, you have successfully configured the app.
9.	Deactivate the environment
10.	Deactivate


24.	Configure and enable the virtual host

1.	create the apache file
2.	sudo nano /etc/apache2/sites-available/catalog.conf
3.	copy the code
<VirtualHost *:80>
      ServerName 35.164.246.46
      ServerAdmin admin@35.164.246.46
      ServerAlias ec2-35-164-246-46.us-west-2.compute.amazonaws.com
      WSGIScriptAlias / /var/www/catalog/catalog.wsgi
      <Directory /var/www/catalog/catalog/>
          Order allow,deny
          Allow from all
      </Directory>
      Alias /static /var/www/catalog/catalog/static
      <Directory /var/www/catalog/catalog/static/>
          Order allow,deny
          Allow from all
      </Directory>
      ErrorLog ${APACHE_LOG_DIR}/error.log
      LogLevel warn
      CustomLog ${APACHE_LOG_DIR}/access.log combined
  </VirtualHost>

4.	Enable the virtual host
5.	sudo a2ensite catalog

25.	Create the .wsgi File and Restart Apache

1.	cd /var/www/catalog
2.	sudo nano catalog.wsgi
3.	copy the code

#!/usr/bin/python
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0,"/var/www/catalog")
from catalog import app as application
application.secret_key = 'super_secret_key'

4.	Restart Apache
5.	sudo service apache2 restart


26.	Clone GitHub repository and make it web inaccessible
https://help.github.com/articles/set-up-git/#platform-linux
1.	Install git
2.	sudo apt-get install git
3.	set your username
4.	git config --global user.name "YOUR NAME"
5.	Set up your email address 
6.	git config --global user.email "YOUR EMAIL ADDRESS"
7.	clone the repository
8.	sudo git clone https://github.com/bakerjohn/udacity-project-5.git
9.	move files from udacity-project-5 to catalog
10.	mv udacity-project-5 catalog
11.	Make the GitHub repository inaccessible
12.	cd /var/www
13.	create a .htaccess file
14.	sudo nano .htaccess
15.	add line of code
16.	RedirectMatch 404 /\.git
17.	Save and exit



27.	Install application dependencies into the virtual environment
1.	Activate virtual environment
2.	source venv/bin/activate
3.	Install application dependencies
4.	pip install httplib2
5.	pip install requests
6.	sudo pip install flask-seasurf
7.	sudo pip install --upgrade oauth2client
8.	sudo pip install Flask SQLALchemy
9.	sudo apt-get install python-psycopg2
10.	sudo pip install dicttoxml


28.	Install and configure PostgreSQL
https://www.digitalocean.com/community/tutorials/how-to-secure-postgresql-on-an-ubuntu-vps
1.	sudo apt-get install postgresql
2.	Check if no remote connections are allowed sudo vim /etc/postgresql/9.3/main/pg_hba.conf
3.	Login as user "postgres" 
4.	sudo su – postgres
5.	Get into postgreSQL shell psql
6.	Create a new database named catalog and create a new user named catalog
7.	CREATE DATABASE catalog;
8.	create a user named catalog
9.	CREATE USER catalog;
10.	Set password
11.	ALTER ROLE catalog WITH PASSWORD  ‘pick-a-password’;
12.	Give user catalog permissions to the database
13.	GRANT ALL PRIVILEGES ON DATABASE catalog TO catalog;
14.	Connect to the database
15.	\c catalog
16.	Revoke rights
17.	REVOKE ALL ON SCHEMA public FROM Public;
18.	Grant access to only the catalog role
19.	GRANT ALL ON SCHEMA public TO catalog;
20.	Quit and exit
21.	\q
22.	Create the database schema
23.	Python database_setup.py

29.	Working with OAUTH
1.	Open http://www.hcidata.info/host2ip.cgi to get your aws public host name
2.	Open the catalog.conf file
3.	sudo nano /etc/apache2/sites-available/catalog.conf
4.	add the server alias
5.	ServerAlias ec2-35-164-246-46.us-west-.compute.amazonaws.com
6.	Re-enable the virtual host
7.	sudo a2ensite catalog.conf
8.	go to the google developers console
9.	https://console.developers.google.com/project
10.	add your host name and public IP-address to your Authorized JavaScript origins and your host name + oauth2callback to Authorized redirect URIs
11.	
 

12.	Download and copy a new json file to your existing client_secrets.json
13.	Restart apache
14.	sudo service apache2 restart
15.	open a browser http://ec2-35-164-246-46.us-west-2.compute.amazonaws.com/

Trouble shooting tips:
Reading the apache error log
sudo tail -20 /var/log/apache2/error.log

Can’t find client_secrets.json
https://discussions.udacity.com/t/apache-cant-find-file-in-main-directory/24498
OAUTH2 mismatch
https://discussions.udacity.com/t/almost-there-origin-mismatch/27217
https://discussions.udacity.com/t/oauth-origin-mismatch/158586
Make sure that you download and copy a new json file to your existing client_secrets.json file after you have changed javascript origins

linux based configuration
https://discussions.udacity.com/t/project-5-resources/28343


