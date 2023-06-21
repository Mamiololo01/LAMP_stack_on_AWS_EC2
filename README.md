# LAMP_stack_on_AWS_EC2
LAMP STACK IN AWS

A technology stack is a set of frameworks and tools used to develop a software product. This set of frameworks and tools are very specifically 
hosen to work together in creating a well-functioning software. They are acronymns for individual technologies used together for a specific technology product. 

LAMP (Linux, Apache, MySQL, PHP or Python, or Perl)

LEMP (Linux, Nginx, MySQL, PHP or Python, or Perl)

MERN (MongoDB, ExpressJS, ReactJS, NodeJS)

MEAN (MongoDB, ExpressJS, AngularJS, NodeJS

Preparing prerequisites
In order to complete this project you will need an AWS account and a virtual server with Ubuntu Server OS.

AWS account setup and Provisioning an Ubuntu Server

Connecting to your EC2 Instance

Register a new AWS account following this instruction.

Select your preferred region (the closest to you) and launch a new EC2 instance of t2.micro family with Ubuntu Server 20.04 LTS (HVM)
launch EC2

The Apache web server is among the most popular web servers in the world. It’s well documented, has an active community of users, and has been in wide use for much of the history of the web, which makes it a great default choice for hosting a website.

Install Apache using Ubuntu’s package manager ‘apt’:

#update a list of packages in package manager

sudo apt update

#run apache2 package installation

sudo apt install apache2

To verify that apache2 is running as a Service in our OS, use following command

sudo systemctl status apache2
If it is green and running, then you did everything correctly – you have just launched your first Web Server in the Clouds!

Before we can receive any traffic by our Web Server, we need to open TCP port 80 which is the default port that web browsers use to access web pages on the Internet

As we know, we have TCP port 22 open by default on our EC2 machine to access it via SSH, so we need to add a rule to EC2 configuration to open inbound connection through port 80:

Open inbound port 80

Our server is running and we can access it locally and from the Internet (Source 0.0.0.0/0 means ‘from any IP address’).

First, let us try to check how we can access it locally in our Ubuntu shell, run:

 curl http://localhost:80
or
 curl http://127.0.0.1:80
 
These 2 commands above actually do pretty much the same – they use ‘curl’ command to request our Apache HTTP Server on port 80 (actually you can even try to not specify any port – it will work anyway). The difference is that: in the first case we try to access our server via DNS name and in the second one – by IP address (in this case IP address 127.0.0.1 corresponds to DNS name ‘localhost’ and the process of converting a DNS name to IP address is called "resolution"). We will touch DNS in further lectures and projects.

As an output you can see some strangely formatted test, do not worry, we just made sure that our Apache web service responds to ‘curl’ command with some payload.

Now it is time for us to test how our Apache HTTP server can respond to requests from the Internet.
Open a web browser of your choice and try to access following url

http://<Public-IP-Address>:80

Another way to retrieve your Public IP address, other than to check it in AWS Web console, is to use following command:

curl -s http://169.254.169.254/latest/meta-data/public-ipv4

The URL in browser shall also work if you do not specify port number since all web browsers use port 80 by default.

If you see following page, then your web server is now correctly installed and accessible through your firewall.


Apache on Ubuntu 20.04 has one server block enabled by default that is configured to serve documents from the /var/www/html directory.
We will leave this configuration as is and will add our own directory next next to the default one.

Create the directory for lamp_stack using ‘mkdir’ command as follows:

sudo mkdir /var/www/lamp_stack

<img width="789" alt="Screenshot 2023-06-21 at 17 51 27" src="https://github.com/Mamiololo01/lamp_stack_on_AWS_EC2/assets/67044030/571db63a-aa43-4b0c-a432-4f5e91abbd20">

Next, assign ownership of the directory with your current system user:

sudo chown -R $USER:$USER /var/www/lamp_stack

<img width="813" alt="Screenshot 2023-06-21 at 17 53 09" src="https://github.com/Mamiololo01/lamp_stack_on_AWS_EC2/assets/67044030/7bb95b91-f0dc-4366-b227-67574c51baf3">
 
Then, create and open a new configuration file in Apache’s sites-available directory using your preferred command-line editor. Here, we’ll be using vi or vim (They are the same by the way):

sudo vi /etc/apache2/sites-available/lamp_stack.conf

This will create a new blank file. Paste in the following bare-bones configuration by hitting on i on the keyboard to enter the insert mode, and paste the text:

<VirtualHost *:80>
    ServerName lamp_stack
    ServerAlias www.lamp_stack 
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/lamp_stack
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>

To save and close the file, simply follow the steps below:

Hit the esc button on the keyboard
Type :
Type wq. w for write and q for quit

Hit ENTER to save the file

You can use the ls command to show the new file in the sites-available directory

<img width="826" alt="Screenshot 2023-06-21 at 17 54 21" src="https://github.com/Mamiololo01/lamp_stack_on_AWS_EC2/assets/67044030/c2d8a260-7302-4cbd-be54-0b5786f91468">

sudo ls /etc/apache2/sites-available

You will see something like this;

000-default.conf  default-ssl.conf  lamp_stack.conf


<img width="817" alt="Screenshot 2023-06-21 at 17 55 23" src="https://github.com/Mamiololo01/lamp_stack_on_AWS_EC2/assets/67044030/24ae4bed-9dba-4113-9972-704912490100">

With this VirtualHost configuration, we’re telling Apache to serve lamp_stack using /var/www/lamp_stack as its web root directory. If you would like to test Apache without a domain name, you can remove or comment out the options ServerName and ServerAlias by adding a # character in the beginning of each option’s lines. Adding the # character there will tell the program to skip processing the instructions on those lines.

You can now use a2ensite command to enable the new virtual host:

sudo a2ensite lamp_stack

<img width="750" alt="Screenshot 2023-06-21 at 17 57 33" src="https://github.com/Mamiololo01/lamp_stack_on_AWS_EC2/assets/67044030/53021d8b-7646-4371-a00d-f3f6786db73f">

You might want to disable the default website that comes installed with Apache. This is required if you’re not using a custom domain name, because in this case Apache’s default configuration would overwrite your virtual host. To disable Apache’s default website use a2dissite command , type:

sudo a2dissite 000-default

<img width="725" alt="Screenshot 2023-06-21 at 17 59 12" src="https://github.com/Mamiololo01/lamp_stack_on_AWS_EC2/assets/67044030/fa66b4ab-803c-4a83-9aed-c7e5d8e760ac">

To make sure your configuration file doesn’t contain syntax errors, run:

sudo apache2ctl configtest

<img width="822" alt="Screenshot 2023-06-21 at 18 00 45" src="https://github.com/Mamiololo01/lamp_stack_on_AWS_EC2/assets/67044030/114ba68a-09f9-49ea-822d-3d8837cab7e0">

Finally, reload Apache so these changes take effect:

sudo systemctl reload apache2

Your new website is now active, but the web root /var/www/lamp_stack is still empty. Create an index.html file in that location so that we can test that the virtual host works as expected:


<img width="789" alt="Screenshot 2023-06-21 at 18 25 26" src="https://github.com/Mamiololo01/lamp_stack_on_AWS_EC2/assets/67044030/3ce68666-68a6-46c1-8577-0cb72e910b4b">

sudo echo 'Hello LAMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/lamp_stack/index.html

<img width="801" alt="Screenshot 2023-06-21 at 18 26 40" src="https://github.com/Mamiololo01/lamp_stack_on_AWS_EC2/assets/67044030/2160d9f6-29b2-4adf-b5da-2961d35474b6">


<img width="790" alt="Screenshot 2023-06-21 at 18 27 08" src="https://github.com/Mamiololo01/lamp_stack_on_AWS_EC2/assets/67044030/1f723d2c-bcbb-44cb-8e73-ab2fb5c31078">

Now go to your browser and try to open your website URL using IP address:

http://Public-IP-Address:80

If you see the text from ‘echo’ command you wrote to index.html file, then it means your Apache virtual host is working as expected.
In the output you will see your server’s public hostname (DNS name) and public IP address. You can also access your website in your browser by public DNS name, not only by IP – try it out, the result must be the same (port is optional)

http://Public-DNS-Name:80

You can leave this file in place as a temporary landing page for your application until you set up an index.php file to replace it. Once you do that, remember to remove or rename the index.html file from your document root, as it would take precedence over an index.php file by default.


In fact, it is the same content that you previously got by ‘curl’ command, but represented in nice HTML formatting by your web browse

Now that you have a web server up and running, you need to install a Database Management System (DBMS) to be able to store and manage data for your site in a relational database. MySQL is a popular relational database management system used within PHP environments, so we will use it in our project.

Again, use ‘apt’ to acquire and install this software:

$ sudo apt install mysql-server

When prompted, confirm installation by typing Y, and then ENTER.

When the installation is finished, log in to the MySQL console by typing:

$ sudo mysql

This will connect to the MySQL server as the administrative database user root, which is inferred by the use of sudo when running this command. You should see output like this:

Welcome to the MySQL monitor.  Commands end with ; or \g.

Your MySQL connection id is 11

Server version: 8.0.22-0ubuntu0.20.04.3 (Ubuntu)

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its affiliates. Other names may be trademarks of their respective owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> 

It’s recommended that you run a security script that comes pre-installed with MySQL. This script will remove some insecure default settings and lock down access to your database system. Before running the script you will set a password for the root user, using mysql_native_password as default authentication method. We’re defining this user’s password as PassWord.1.

ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'PassWord.1';

Exit the MySQL shell with:

mysql> exit

Start the interactive script by running:

$ sudo mysql_secure_installation

This will ask if you want to configure the VALIDATE PASSWORD PLUGIN.

Note: Enabling this feature is something of a judgment call. If enabled, passwords which don’t match the specified criteria will be rejected by MySQL with an error. It is safe to leave validation disabled, but you should always use strong, unique passwords for database credentials.

Answer Y for yes, or anything else to continue without enabling.

VALIDATE PASSWORD PLUGIN can be used to test passwords and improve security. It checks the strength of password and allows the users to set only those passwords which are secure enough. Would you like to setup VALIDATE PASSWORD plugin?

Press y|Y for Yes, any other key for No:

If you answer “yes”, you’ll be asked to select a level of password validation. Keep in mind that if you enter 2 for the strongest level, you will receive errors when attempting to set any password which does not contain numbers, upper and lowercase letters, and special characters, or which is based on common dictionary words e.g PassWord.1.

There are three levels of password validation policy:

LOW    Length >= 8

MEDIUM Length >= 8, numeric, mixed case, and special characters

STRONG Length >= 8, numeric, mixed case, special characters and dictionary              file

Please enter 0 = LOW, 1 = MEDIUM and 2 = STRONG: 1

Regardless of whether you chose to set up the VALIDATE PASSWORD PLUGIN, your server will next ask you to select and confirm a password for the MySQL root user. This is not to be confused with the system root. The database root user is an administrative user with full privileges over the database system. 

Even though the default authentication method for the MySQL root user dispenses the use of a password, even when one is set, you should define a strong password here as an additional safety measure. We’ll talk about this in a moment.

If you enabled password validation, you’ll be shown the password strength for the root password you just entered and your server will ask if you want to continue with that password. If you are happy with your current password, enter Y for “yes” at the prompt:

Estimated strength of the password: 100 

Do you wish to continue with the password provided?(Press y|Y for Yes, any other key for No) : y

For the rest of the questions, press Y and hit the ENTER key at each prompt. This will prompt you to change the root password, remove some anonymous users and the test database, disable remote root logins, and load these new rules so that MySQL immediately respects the changes you have made.

When you’re finished, test if you’re able to log in to the MySQL console by typing:

$ sudo mysql -p
Notice the -p flag in this command, which will prompt you for the password used after changing the root user password.

To exit the MySQL console, type:

mysql> exit

Notice that you need to provide a password to connect as the root user.

For increased security, it’s best to have dedicated user accounts with less expansive privileges set up for every database, especially if you plan on having multiple databases hosted on your server.

Note: At the time of this writing, the native MySQL PHP library mysqlnd doesn’t support caching_sha2_authentication, the default authentication method for MySQL 8. For that reason, when creating database users for PHP applications on MySQL 8, you’ll need to make sure they’re configured to use mysql_native_password instead.

Your MySQL server is now installed and secured. Next, we will install PHP, the final component in the LAMP stack.

ou have Apache installed to serve your content and MySQL installed to store and manage your data. PHP is the component of our setup that will process code to display dynamic content to the end user. In addition to the php package, you’ll need php-mysql, a PHP module that allows PHP to communicate with MySQL-based databases. You’ll also need libapache2-mod-php to enable Apache to handle PHP files. Core PHP packages will automatically be installed as dependencies.

To install these 3 packages at once, run:

sudo apt install php libapache2-mod-php php-mysql

Once the installation is finished, you can run the following command to confirm your PHP version:

php -v

PHP 7.4.3 (cli) (built: Oct  6 2020 15:47:56) ( NTS )

Copyright (c) The PHP Group

Zend Engine v3.4.0, Copyright (c) Zend Technologies
At this point, your LAMP stack is completely installed and fully operational.



