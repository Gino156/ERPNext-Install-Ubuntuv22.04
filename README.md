ERPNext Installation using Ubuntu v22.04

 After setting up the VirtualBox, go to cmd terminal
In CMD:
•	Development Setup
sudo apt-get clean -y
sudo apt-get autoremove -y
sudo apt --fix-broken install -y
sudo dpkg --configure -a
sudo apt-get install -f
sudo apt-get update && sudo apt-get upgrade

•	root@server22
sudo su

•	Firewall
ufw allow 22,25,143,80,443,3306,3022,8000/tcp
ufw enable

•	Pre-requisites
apt-get install git python3-dev python3.10-dev python3-setuptools python3-pip python3-distutils redis-server -y
apt install python3.10-venv -y
sudo apt-get update -y
apt-get install xvfb libfontconfig wkhtmltopdf -y

•	Setting your root password
sudo passwd root
password: dC_0jt22!?

•	Database configuration
apt-get install mariadb-server mariadb-client -y

•	Use this command to prompt the ff options:
mysql_secure_installation

Switch to unix_socket authentication [Y/n] n
Change the root password? [Y/n] y
password: dC_0jt22!?
Remove anonymous users? [Y/n] y
Disallow root login remotely? [Y/n] y
Remove test database and access to it? [Y/n] y
Reload privilege tables now? [Y/n] y

•	MSQL Development files
apt-get install libmysqlclient-dev

•	MariaDB Configuration
mysql -u root -p

•	Verifying MariaDB authentication plugin:
USE mysql;
select User, plugin from user;
flush privileges;
exit;

•	Edit the file
nano /etc/mysql/mariadb.conf.d/50-server.cnf

•	Add or modify the following lines:

[mysqld]
innodb-file-format=barracuda
innodb-file-per-table=1
innodb-large-prefix=1
character-set-client-handshake=FALSE
character-set-server=utf8mb4
collation-server=utf8mb4_unicode_ci

•	Save the file by ctrl O, Enter and ctrl X, Enter.
systemctl restart mariadb 

•	Creating User for ERPNEXT:
useradd -m -s /bin/bash erpnext

•	Set password
passwd erpnext

•	Add erpnext user to sudo group so that it can execute sudo command:
usermod -aG sudo erpnext

•	Login the erpnext user and set up the environment variables:
su - erpnext
sudo nano ~/.bashrc

•	Add the following line:
PATH=$PATH:~/.local/bin/

•	Save and close the file, then activate the environment variable:
source ~/.bashrc

•	Install curl
sudo apt install curl

•	Install Node.js
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.39.1/install.sh | bash

source ~/.bashrc

nvm install 16

•	Install Yarn using NPM
npm install -g yarn

•	Install ERPNext
su – erpnext

•	Create a new directory for bench.
sudo mkdir /opt/bench

•	Grant permission to erpnext user:
sudo chown -R erpnext:erpnext /opt/bench

•	Change directory to your newly created folder. Clone bench.
cd /opt/bench
git clone https://github.com/frappe/bench bench-repo
•	Install bench.
pip3 install -e bench-repo

•	Initialize frappe.
bench init erpnext --frappe-branch version-14

•	Change directory to the newly created folder. Create new site for your desired domain.
cd erpnext
bench new-site YOUR_SITE(mysite) 

•	Get the ERPNext app with version 14.
bench get-app --branch version-14 erpnext 

•	Production Setup
sudo apt-get install supervisor nginx nodejs

•	Install frappe-bench from pip.
sudo pip3 install frappe-bench

•	Setup for production environment. Run this twice.
sudo ~/.local/bin/bench setup production erpnext

•	Done.

•	Check supervisor status to verify that frappe/erpnext services are running.

sudo supervisorctl status

it must be:
erpnext-redis:erpnext-redis-cache                RUNNING
erpnext-redis:erpnext-redis-queue                RUNNING
erpnext-redis:erpnext-redis-socketio             RUNNING
erpnext-web:erpnext-frappe-web                   RUNNING
erpnext-web:erpnext-node-socketio                RUNNING
erpnext-workers:erpnext-frappe-default-worker-0  RUNNING
erpnext-workers:erpnext-frappe-long-worker-0     RUNNING
erpnext-workers:erpnext-frappe-schedule          RUNNING
erpnext-workers:erpnext-frappe-short-worker-0    RUNNING

•	Start the localhost
bench start
