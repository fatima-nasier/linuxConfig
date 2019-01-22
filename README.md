# linux Server Configuration

Udacity-Full-Stack-Nanodegree-Project.

## About
This is the sixth project for the Udacity Full Stack Nanodegree. This project involves taking a baseline installation of Linux on a virtual machine and preparing it to host web applications. This includes installing updates, securing the server from attacks, and installing / configuring web and database servers.

## Server Info
* Public IP: 52.163.201.89
* Port: 2200
* URL: [http://52.163.201.89.xip.io/](http://52.163.201.89.xip.io/)

## Start a new Ubuntu Linux Server instance on Azure portal

1. Create an [free account](https://azure.microsoft.com/en-us/free/?WT.mc_id=A261C142F) account. 
1. Choose Create a resource in the upper left corner of the Azure portal.
1. select Ubuntu Server 18.04.1 LTS by Canonical, then choose Create.
1. In the Basics tab, under Project details type name of the resource group 
1. Under Instance details, give your Virtual machine name
1. Under Administrator account, select SSH public key.
1. Under Inbound port rules > Public inbound ports, choose Allow selected ports.
1. Wait for startup
1. Once the instance has started up, follow the instructions provided to SSH into your server.
1. For more detail [https://docs.microsoft.com/en-us/azure/virtual-machines/linux/quick-create-portal](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/quick-create-portal) 

# Configuration changes
### Configuration of Uncomplicated Firewall (UFW)
* Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123).

```sudo ufw allow 2200/tcp  
  sudo ufw allow 80/tcp 
  sudo ufw allow 123/tcp 
  sudo ufw enable
```
*  Change the SSH port from 22 to 2200
1. Find the Port line in the same file above, i.e /etc/ssh/sshd_config and edit it to 2200.
1. Save the file.
1. Run ``$ sudo service ssh restart`` to restart the service.

### Create New User
1. Create a new user account grader:``$ sudo adduser grader``
1. ``$ sudo nano /etc/sudoers``
1. Create a file named grader under this path: ``$ sudo touch /etc/sudoers.d/grader``
* give grader sudo access
1. Edit this file: ``$ sudo nano /etc/sudoers.d/grader``, add code ``grader ALL=(ALL:ALL) ALL``. Save and exit

### Set SSH login using keys
1. Create an SSH key pair for grader using the``ssh-keygen``tool on your local machine. Save it in ``~/.ssh`` path
1. Deploy public key on development environment
     - On your local machine, read the generated public key ``cat ~/.ssh/FILE-NAME.pub``
    - On your virtual machine
    `` $ mkdir .ssh `` ``$ touch .ssh/authorized_keys `` `` $ nano .ssh/authorized_keys ``

    - Copy the public key to this authorized_keys file on the virtual machine and save
1. Run ``chmod 700 .ssh ``and ``chmod 644 .ssh/authorized_keys ``on your virtual machine to change file permission
1. Restart SSH: ``$ sudo service ssh restart``
1. Now you are able to login in as grader:`` $ ssh -i ~/.ssh/graderKey -p 2200 grader@52.163.201.89``
1. You will be asked for grader's password. To unable it, open configuration file 
1. again: ``$ sudo nano /etc/ssh/sshd_config``
1. Change ``PasswordAuthentication yes`` to`` no``
1. Restart SSH: ``$ sudo service ssh restart``

### Update all currently installed packages
1. Run ``sudo apt-get update ``to update packages
1. Run ``sudo apt-get upgrade`` to install newest versions of packages
1. Set for future updates:`` sudo apt-get dist-upgrade``

### Disable root login
* Change the following line in the file ``/etc/ssh/sshd_config``:

From ``PermitRootLogin without-password ``to ``PermitRootLogin no``.

Also, uncomment the following line so it reads:``PasswordAuthentication no``

* Do ``service ssh restart ``for the changes to take effect.

Will now do all commands using the ``grader`` user, using ``sudo`` when required.

### Change timezone to UTC
Check the timezone with the ``date`` command. This will display the current timezone after the time. If it's not UTC change it like this:
``sudo timedatectl set-timezone UTC``
### Install and configure Apache
1. Install Apache: ``$ sudo apt-get install apache2``
1. Go to [http://52.163.201.89/](http://52.163.201.89/), if Apache is working correctly, a Apache2 Ubuntu Default Page will show up
### Install and configure Python mod_wsgi
1. Install the mod_wsgi package: ``$ sudo apt-get install libapache2-mod-wsgi python-dev``
1. Enable mod_wsgi: ``$ sudo a2enmod wsgi``
1. Restart Apache: ``$ sudo service apache2 restart``
### Install and configure PostgreSQL:
Run`` $ sudo apt-get install postgresql``
Create new PostgreSQL user called catalog
### Install git and clone catalog application from github
Run ``$ sudo apt-get install git``
### Edit client_secrets.json file
1. Create a new project on Google API Console and download ``client_scretes.json`` file
1. Copy and paste contents of downloaded ``client_scretes.json`` to the file with same name under directory ``/var/www/catalog/catalog/client_secrets.json``
### Setup for deploying a Flask App on Ubuntu VPS
1. Install pip:`` $ sudo apt-get install python-pip``
1. Install packages:
   ``$ sudo pip install httplib2``
  `` $ sudo pip install requests``
  `` $ sudo pip install --upgrade oauth2client``
  `` $ sudo pip install sqlalchemy``
  `` $ sudo pip install flask``
  `` $ sudo apt-get install libpq-dev``
  `` $ sudo pip install psycopg2 ``
### Setup and enble a virtual host
1. Create file: ``$ sudo touch /etc/apache2/sites-available/catalog.conf``
1. Run ``$ sudo a2ensite catalog`` to enable the virtual host
1. Restart Apache: ``$ sudo service apache2 reload``
### Configure .wsgi file
1. Create file: ``$ sudo touch /var/www/catalog/catalog.wsgi & sudo nano /var/www/catalog/catalog.wsgi ``
1. Restart Apache: $ sudo service apache2 reload
### Disable defualt Apache page 
``$ sudo a2dissite 000-defualt.conf``
Restart Apache: ``$ sudo service apache2 reload``
### Enable the catalog app virtual host:
``$ sudo a2ensite catalog-app.conf``
### Set up database schema
1. Run ``$ sudo python database_setup.py``
1. Run ``$ sudo python lotsofitems.py``
1. Restart Apache: $ sudo service apache2 reload
1. Now follow the link to [http://52.163.201.89.xip.io/](http://52.163.201.89.xip.io/) the application should be runing online
1. If internal errors occur: check the [Apache error file](https://www.a2hosting.com/kb/developer-corner/apache-web-server/viewing-apache-log-files)
## Resources
* [https://help.ubuntu.com/community/PostgreSQL](https://help.ubuntu.com/community/PostgreSQL)
* [https://www.udacity.com/](https://www.udacity.com/)
