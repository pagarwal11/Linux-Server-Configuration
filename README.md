# Linux-Server-Configuration
This is the linux server configuration project for Udacity's Full Stack Web Developer Nanodegree Program.

The objective is to take a baseline installation of a Linux distribution on a virtual machine and prepare it to host your web applications, to include installing updates, securing it from a number of attack vectors and installing/configuring web and database servers.

1. The Linux distribution is Ubuntu 16.04 LTS.
2. The virtual private server is Amazon Lighsail.
3. The web application is the Catalog project created earlier(Project 4) Nanodegree program.
4. The database server is PostgreSQL.

Step 1:
Create an Ubuntu Linux Server instance on Amazon Lighsail.
Enter the details and wait for the instance to start up.

Step 2:
SSH into the server
Download the SSH key from account section and download the Default private key.
Move this private key file named LightsailDefaultPrivateKey-*.pem into the local folder ~/.ssh and rename it lightsail_key.rsa.
Run chmod 600 ~/.ssh/lightsail_key.rsa
Connect to the instance via the terminal: ssh -i ~/.ssh/lightsail_key.rsa -p 2200 ubuntu@13.126.94.85 

Step 3: Secure the server

Run sudo apt-get update
Run sudo apt-get upgrade

Step 4: Change port from 22 to 2200
Edit /etc/ssh/sshd_config and change port. 
sudo service ssh restart

Step 5: Configure the Uncomplicated Firewall (UFW)
sudo ufw status                  
sudo ufw default deny incoming   
sudo ufw default allow outgoing  
sudo ufw allow 2200/tcp          
sudo ufw allow www              
sudo ufw allow 123/udp           
sudo ufw deny 22 
sudo ufw enable

Click on the Manage option of the Amazon Lightsail Instance, then the Networking tab, and then change the firewall configuration to match the internal firewall settings above. 

Use Fail2Ban to ban attackers
sudo apt-get install fail2ban
Change the settings in /etc/fail2ban/jail.local file
sudo service fail2ban restart


Automatically install updates
sudo apt-get install unattended-upgrade

Run the following:
sudo apt-get update
sudo apt-get dist-upgrade
sudo shutdown -r now

Step 6: Give the grader access
Run sudo adduser grader
Fill out the information

Step 7: Give grader the permission to sudo
sudo visudo
root    ALL=(ALL:ALL) ALL
grader  ALL=(ALL:ALL) ALL
Save and exit.

Step 9: Create an SSH key pair for grader using the ssh-keygen tool
Step 10: Configure the local timezone to UTC
Step 11: Install and configure Apache to serve a Python mod_wsgi application
sudo apt-get install apache2
sudo apt-get install libapache2-mod-wsgi-py3
Enable mod_wsgi using: sudo a2enmod wsgi

Step 12: Install and configure PostgreSQL
sudo apt-get install postgresql
Create the catalog user with a password and give them the ability to create databases:

postgres=# CREATE ROLE catalog WITH LOGIN PASSWORD 'catalog';
postgres=# ALTER ROLE catalog CREATEDB;

Run sudo visudo
Do the changes:
root    ALL=(ALL:ALL) ALL
grader  ALL=(ALL:ALL) ALL
catalog  ALL=(ALL:ALL) ALL

Deploy the Item Catalog project:
sudo git clone https://github.com/pagarwal11/Catalog-Project.git
Replace: 
# app.run(host="0.0.0.0", port=8000, debug=True)
app.run()

# engine = create_engine("sqlite:///catalog.db")
engine = create_engine('postgresql://catalog:PASSWORD@localhost/catalog')

Step 13: Install the virtual environment and dependencies
Step 14:  Set up and enable a virtual host
Create /etc/apache2/sites-available/catalog.conf and update to configure the virtual host
Enable virtual host: sudo a2ensite catalog

Step 15:  Set up the Flask application
Restart Apache: sudo service apache2 restart

Step 16:Disable the default Apache site
Step 17: Launch the web application
Open your browser to http://13.126.94.85/


Resources:
https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps
https://www.networkworld.com/article/2726217/endpoint-protection/how-to-enforce-password-complexity-on-linux.html
https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-14-04
https://www.digitalocean.com/community/tutorials/how-to-configure-the-apache-web-server-on-an-ubuntu-or-debian-vps