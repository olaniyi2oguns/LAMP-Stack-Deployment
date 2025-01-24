# LAMP-Stack-Deployment
Apache HTTP Server

## Description
*What is Apache?*

Apache HTTP Server is one of the most widely used web servers globally. Developed by the Apache Software Foundation, it's open-source, free, fast, reliable, and secure. It’s highly customizable with extensions and modules and is commonly used by hosting providers, including WordPress. Other web server alternatives include Nginx and Microsoft IIS.

Apache is well-documented, has a large user community, and is a reliable choice for hosting websites.

## Steps

**Install Apache on Ubuntu**

- Create your instance on AWS Ubuntu 22
- Link your instance to your local machine via ssh
- Update the package index and install Apache using the commands below:

`sudo apt update`

`sudo apt install apache2`

You should see the result similar to the one in the images below:

![instance update](/Lamp%20Images/update.jpg)

![install apache](/Lamp%20Images/apacheInstallation.jpg)

- Verify if apache is running properly. Use this command to check if Apache is running:

`sudo systemctl status apache2`

You should see a green "active (running)" status. This means your Apache web server is live! 

- Test Apache locally

1. To do this, you need to go back to your instance on AWS console
2. Open your security group and add an inbound rule for HTTP (port 80) with source 0.0.0.0/0 (that is, it is assecible from any IP)
3. After you must have opened port 80, run the either of the commands below to test Apache locally:

`curl http://localhost:80`
OR
`curl http://127.0.0.1:80`

Both commands should return an Apache test page as raw HTML text as shown below

![html text](/Lamp%20Images/localhost1.jpg)

- Test apache on the internet

1. Retrieve your EC2 instances's public IP address with this command:

`curl -s http://169.254.169.254/latest/meta-data/public-ipv4`

you can as well copy the ip address directly from your instance.

2. Open a web browser and navigate to http://(your-public-IP-address):80

If the apache test page appears, it means your web server is successfully running and accessible as shown below:

![apache-testing](/Lamp%20Images/Ubuntu-page.jpg)

**Installing MySQL**

Now that Apache is running, install MySQL to manage your database.

- To install MySQL, run the command below:

`sudo apt install mysql-server`

- Login to the mysql after you have installed mysql-server using the command below:

`sudo mysql`

- Secure MySQL. Set a password to secure your database (mysql). To do this, run the following commands to set the root password and ply security settings:

`ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '(your password of choice)';
`

![password](/Lamp%20Images/mysql%20set%20password.jpg)

Once this is done, exit the MySQL console with this command

`exit`

- Run the MySQL security script below to validate your password and set its strength

`sudo mysql_secure_installation`

- During the script, answer the prompts based on your requirements:

*Enable the VALIDATE PASSWORD PLUGIN by answering yes.
Remove anonymous users, disallow root login remotely, remove the test database, and reload privilege tables.*

see the images below:

![validation 1](/Lamp%20Images/mysqlpassword1.jpg)

![validation 2](/Lamp%20Images/mysqlpassword2.jpg)

![validation 3](/Lamp%20Images/Mysqlpassword3.jpg)

- Test if your MySQL is working as expected by running the command `sudo mysql -p`

Note: you will be asked to enter password you created earlier.

![readymysql](/Lamp%20Images/Testing%20Mysql.jpg)

**Installing PHP**

PHP enables dynamic content for websites. To integrate PHP with Apache and MySQL, install the necessary packages. 
- Install PHP and all important Modules by running the command below:

`sudo apt install php libapache2-mod-php php-mysql
`

- Verify if PHP is properly installed by checking the version `php -v`

![php](/Lamp%20Images/phpversion.jpg)

**Creating a Virtual Host for YOur Website**

By default, Apache serves files from /var/www/html. To host multiple sites or organize files better, create a custom virtual host.

- Create a Project Directory by running the command below:

`mkdir /var/www/lampstack`

Note: you can replace `lampstack` with any name of your choice.

- Assign ownership of the directory with current system user by running this command

`sudo chown -R $USER:$USER /var/www/lampstack`

- Create a virtual host configuration file by running this command `sudo vim /etc/apache2/sites-available/lampstack.conf`

This will open an empty file; paste the following configuration  into the file by clickin i to change to insert mode:

```<VirtualHost *:80>
    ServerName lampstack
    ServerAlias www.lampstack
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/lampstack
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
To exit the file, click `esc` and then  `:` and enter `wq` and hit enter.

- Enable the virtuall host by running the following commands individually:

`sudo a2ensite lampstack`

`sudo a2dissite 000-default`

`sudo apache2ctl configtest`

`sudo systemctl reload apache2`

- Test the virtual host if working properly as expected

`echo 'Hello LAMP from EC2!' > /var/www/lampstack/index.html`

Access your site via `http://<Public-IP-Address>:80`

YOu should see something similar to the image below

![index](/Lamp%20Images/final.1.jpg)


- Enabling PHP on the Website

By default, Apache prioritizes index.html over index.php. Modify this behavior:

Edit Apache Directory Index Configuration by running command `sudo vim /etc/apache2/mods-enabled/dir.conf`

In the file, you will see the line of code below 

`DirectoryIndex index.html index.cgi index.pl index.php index.xhtml index.htm`

Replace the code with:

`DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm`

Reload Apache to ensure your changes is being effected 

`sudo systemctl reload apache2`

- Test PHP Integration if it is working well. Do so by running this command

`sudo vim /var/www/lampstack/index.php`

Then add the following line of codes

```<?php
phpinfo();
?>
```

- Test your set ups by entering your `<public-ip-addres>:80`

You should see a PHP information page that looks like the one in the image below:

![php](/Lamp%20Images/php%20page.jpg)

Don't forget to remove this file after you have seen necessary information. YOu can remove the file by running this command `sudo rm /var/www/lampstack/index.php`


# Congratulations!

You’ve successfully deployed a LAMP stack on an AWS EC2 instance. Your setup includes:

Linux (Ubuntu 20.04)
Apache (Web Server)
MySQL (Database Management System)
PHP (Dynamic Content Processor)
Your LAMP stack is now ready for hosting dynamic websites and applications!






