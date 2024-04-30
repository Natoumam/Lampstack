# LAMPSTACK
This repository explains the hands-on steps in creating and hosting a LAMP server on AWS. Detailed explanation is available in the Readme.md file


###Lamp
LAMP stack (Linux + Apache + MySQL + PHP/Perl/Python) are a popular setup for web servers. There is a plethora of Open Source applications written using the LAMP application stack. Some popular LAMP applications are Wiki’s, Content Management Systems, and Management Software such as phpMyAdmin.

![picture](./images/screenshots/Lampstack.png)

One advantage of LAMP is the substantial flexibility for different database, web server, and scripting languages. Popular substitutes for MySQL include SQLite and PostgreSQL. PHP can be replaced with Python, Perl while Nginx, Cherokee and Lighttpd can replace Apache in other web stack implementatios like the LEMP application stack.

For this LAMP stack project Linux, APache, MySQL and PHP will be used. All four of these technologies are open source, which means they are community maintained and freely available for anyone to use. Developers use LAMP stacks to create, host, and maintain web content. It is a popular solution that powers many of the websites you commonly use today. To read more about LAMP stack, checkout this AWS documentation
https://aws.amazon.com/what-is/lamp-stack/

-------
----
##What is LAMP stack used for?
----
A LAMP stack is used for backend or server-side development. A backend application is software that runs in an environment that’s hidden from end users. Backend applications consist of the following:

Data processing software
Database components
Business logic in code
API for communicating with other applications
The webpage that shows up on your browser is called the frontend application. When you interact with the page, such as by clicking on a button, your browser communicates with the backend application to retrieve the required information.

Developers use a LAMP stack to create both static and dynamic web content. Static webpages

Static webpage information from the web server is the same for all users. For example, the address on a company's website is static content. Web developers create static webpages with HTML and CSS programming languages and store them as files in the web server application. Dynamic webpages

Dynamic webpages contain information that changes depending on the user viewing the webpage or web application. For example, a website message that changes based on your location is dynamic content. The web server delivers dynamic websites by processing business logic or retrieving data from a database.

----
----
# Implementing The Lamp Stack on AWS
----
----
## Step 1: Create a Virtual Server on AWS
* Login to the AWS console
* Search for EC2 (Elastic Compute Cloud)
* Select your preferred region (the closest to you) and launch a new EC2 instance of t2.micro family with Ubuntu Server 20.04 LTS (HVM)
* Type a name e.g My_Lamp_Server Click create a new key pair, use any name of your choice as the name for the pem file and select .pem.
    * Linux/Mac users, choose .pem for use with openssh. This allows you to connect to your server using open ssh clients.
    * For windows users choose .ppk for use with putty. Putty is a software that lets you connect remotely to servers
* Save your private key (.pem file) securely and do not share it with anyone! If you lose it, you will not be able to connect to your server ever again!

![](./images/screenshots/image1%20ec2.png)
* On your local computer, open the terminal and change directory to the Downloads folder, type

`cd ~/Downloads`

* Change permissions for the private key file (.pem), otherwise you can get an error “Bad permission”

`sudo chmod 0600 <private-key-name>. pem`

* Connect to the instance by running

`ssh -i <private-key-name>. pem ubuntu@<Public-IP-address>`

Note: For Ubuntu server, the usernanme is ubuntu and for other servers like Fedora Amazon Machine image (AMI), the username is fedora or ec2-user.
Congratulations! You have just created your very first Linux Server in the Cloud and our set up looks like this now: (You are the client)

![](./images/screenshots/connect%20to%20instance%20with%20ssh.png)
---
---

## Step 2: Installing Apache and Updating the Firewall
Apache HTTP Server is the most widely used web server software. Developed and maintained by Apache Software Foundation, Apache is an open-source software available for free. It runs on 67% of all webservers in the world. It is fast, reliable, and secure. It can be highly customized to meet the needs of many different environments by using extensions and modules. Most WordPress hosting providers use Apache as their web server software.

* Update a list of packages in package manager

`sudo apt update`

![](./images/screenshots/update%20app%20with%20sudo%20apt%20upda.png)

* Run apache2 package installation

`sudo apt install apache2`

![](./images/screenshots/apache2.png)

* To verify that apache2 is running as a Service

`sudo systemctl status apache2`

![](./images/screenshots/apache%202%20running.png)

Before we can receive any traffic by our Web Server, we need to open TCP port 80 which is the default port that web browsers use to access web pages on the Internet As we know, we have TCP port 22 open by default on our EC2 machine to access it via SSH, so we need to add a rule to EC2 configuration to open inbound connection through port 80: I configured the inbound when I was creating the virtual server

![](./images/screenshots/port%2080%20enabled.png)

* Our server is running and we can access it locally and from the Internet (Source 0.0.0.0/0 means ‘from any IP address’). First, let us try to check how we can access it locally in our Ubuntu shell, run:

`curl http://localhost:80`

* Open a web browser of your choice and try to access following url

` http://<Public-IP-address>`

![](./images/screenshots/apache%202%20web%20page.png)

* Another way to retrieve your Public IP address, other than to check it in AWS Web console, is to use following command:

`curl -s http://169.254.169.254/latest/meta-data/public-ipv4`

----
---

## Step 3: Installing Mysql

Now that you have a web server up and running, you need to install a Database Management System (DBMS) to be able to store and manage data for your site in a relational database. MySQL is a popular relational database management system used within PHP environments, so we will use it in our project. Again, use ‘apt’ to acquire and install this software:

`$ sudo apt install mysql-server`

![](./images/screenshots/log%20to%20my%20sql.png)

When prompted, confirm installation by typing Y, and then ENTER.

When the installation is finished, log in to the MySQL console by typing:

`$ sudo mysql`

![](./images/screenshots/log%20to%20my%20sql.png)

This will connect to the MySQL server as the administrative database user root, which is inferred by the use of sudo when running this command. You should see output like this:

`Welcome to the MySQL monitor. Commands end with; or \g. Your MySQL connection id is 11 Server version: 8.0.22-0ubuntu0.20.04.3 (Ubuntu) Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved. Oracle is a registered trademark of Oracle Corporation and/or its affiliates. Other names may be trademarks of their respective owners.`

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

`mysql>`

It’s recommended that you run a security script that comes pre-installed with MySQL. This script will remove some insecure default settings and lock down access to your database system. Before running the script, you will set a password for the root user, using mysql_native_password as default authentication method. We’re defining this user’s password as PassWord.1.

`ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'PassWord.1';`

Exit the MySQL shell with:

`mysql> exit Start the interactive script by running: $ sudo mysql_secure_installation`

----
----

## Step 4: Installing PHP

You have Apache installed to serve your content and MySQL installed to store and manage your data. PHP is the component of our setup that will process code to display dynamic content to the end user. In addition to the php package, you’ll need php-mysql, a PHP module that allows PHP to communicate with MySQL-based databases. You’ll also need libapache2-mod-php to enable Apache to handle PHP files. Core PHP packages will automatically be installed as dependencies. To install these 3 packages at once, run:

`sudo apt install php libapache2-mod-php php-mysql`

![](./images/screenshots/installing%20PHP.png)

Once the installation is finished, you can run the following command to confirm your PHP version:

`php -v PHP 7.4.3 (cli) (built: Oct 6 2020 15:47:56) (NTS) Copyright (c) The PHP Group Zend Engine v3.4.0, Copyright (c) Zend Technologies`

#### At this point, your LAMP stack is completely installed and fully operational.
* Linux (Ubuntu)
* Apache HTTP server
* MySQL
* PHP

To test your setup with a PHP script, it’s best to set up a proper Apache Virtual Host to hold your website’s files and folders. Virtual host allows you to have multiple websites located on a single machine and users of the websites will not even notice it.

---
---
### Step 5: Creating a Virtual Host For your website using Apache

In this project, you will set up a domain called projectlamp, but you can replace this with any domain of your choice. Apache on Ubuntu 20.04 has one server block enabled by default that is configured to serve documents from the /var/www/html directory. We will leave this configuration as is and will add our own directory next next to the default one. Create the directory for projectlamp using ‘mkdir’ command as follows:

`sudo mkdir /var/www/projectlamp`

Next, assign ownership of the directory with your current system user:

`sudo chown -R $USER:$USER /var/www/projectlamp`

Then, create and open a new configuration file in Apache’s sites-available directory using your preferred command-line editor. Here, we’ll be using vi or vim (They are the same by the way):

`sudo vi /etc/apache2/sites-available/projectlamp.conf`

This will create a new blank file. Paste in the following bare-bones configuration by hitting on i on the keyboard to enter the insert mode, and paste the text:

`VirtualHost *:80>
            ServerName projectlamp
            ServerAlias www.projectlamp 
            ServerAdmin webmaster@localhost
            DocumentRoot /var/www/projectlamp
            ErrorLog ${APACHE_LOG_DIR}/error.log
            CustomLog ${APACHE_LOG_DIR}/access.log combined
        VirtualHost>`


You can use the ls command to show the new file in the sites-available directory

`sudo ls /etc/apache2/sites-available`

You will see something like this;

`000-default.conf default-ssl.conf  projectlamp.conf`

With this VirtualHost configuration, we’re telling Apache to serve projectlamp using /var/www/projectlampl as its web root directory. If you would like to test Apache without a domain name, you can remove or comment out the options ServerName and ServerAlias by adding a # character in the beginning of each option’s lines. Adding the # character there will tell the program to skip processing the instructions on those lines. You can now use a2ensite command to enable the new virtual host:

`sudo a2ensite projectlamp`

You might want to disable the default website that comes installed with Apache. This is required if you’re not using a custom domain name, because in this case Apache’s default configuration would overwrite your virtual host.

`sudo a2dissite 000-default`

To disable Apache’s default website use a2dissite command, type:

`sudo a2dissite 000-default`

To make sure your configuration file doesn’t contain syntax errors, run:

`sudo apache2ctl configtest`

Finally, reload Apache so these changes take effect:

`sudo systemctl reload apache2`

Your new website is now active, but the web root /var/www/projectlamp is still empty. Create an index.html file in that location so that we can test that the virtual host works as expected:

 `sudo echo 'Hello LAMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectlamp/index.html`

Now go to your browser and try to open your website URL using IP address: http://:80

----
-----

### Step 6: Enable PHP on the Website
With the default DirectoryIndex settings on Apache, a file named index.html will always take precedence over an index.php file. This is useful for setting up maintenance pages in PHP applications, by creating a temporary index.html file containing an informative message to visitors. Because this page will take precedence over the index.php page, it will then become the landing page for the application. Once maintenance is over, the index.html is renamed or removed from the document root, bringing back the regular application page. In case you want to change this behavior, you’ll need to edit the /etc/apache2/mods-enabled/dir.conf file and change the order in which the index.php file is listed within the DirectoryIndex directive:

`sudo vim /etc/apache2/mods-enabled/dir.conf`

`#Change this:
        #DirectoryIndex index.html index.cgi index.pl index.php index.xhtml index.htm
        #To this:
        DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
    </IfModule>`

After saving and closing the file, you will need to reload Apache so the changes take effect:

`sudo systemctl reload apache2`

Finally, we will create a PHP script to test that PHP is correctly installed and configured on your server. Now that you have a custom location to host your website’s files and folders, we’ll create a PHP test script to confirm that Apache is able to handle and process requests for PHP files. Create a new file named index.php inside your custom web root folder:

`vim /var/www/projectlamp/index.php`

This will open a blank file. Add the following text, which is valid PHP code, inside the file:

`.<?php
phpinfo();`

When you are finished, save and close the file, refresh the page and you will see a page similar to this:

![](./images/screenshots/php%20screenshot.png)

This page provides information about your server from the perspective of PHP. It is useful for debugging and to ensure that your settings are being applied correctly. If you can see this page in your browser, then your PHP installation is working as expected. After checking the relevant information about your PHP server through that page, it’s best to remove the file you created as it contains sensitive information about your PHP environment -and your Ubuntu server. You can use rm to do so: sudo rm /var/www/projectlamp/index.php You can always recreate this page if you need to access the information again later.

Do not forget to stop your EC2 instance in order not to accrue cost.

### !!! Congratulations, you have successfully hosted your LAMP server on AWS






