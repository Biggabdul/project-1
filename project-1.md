# PROJECT 1 
## THE IMPLEMENTATION OF LAMP STACK

### Step 1 : Installing apache and updating the fire wall



- update a list of packages in package manager 

`sudo apt update`
![sudo apt update](./images/01-sudo-apt-update.PNG)

- run apache2 package installation

`sudo apt install apache2`
![](./images/02%20sudo%20apt%20istall%20apache2.PNG)

To verify that apache2 is running as a Service in our OS, use  command:

`sudo systemctl status apache2`

![](./images/03%20sudo%20systemctl%20status%20apache2.PNG)


As we know, we have TCP port 22 open by default on our EC2 machine to access it via SSH, so we need to add a rule to EC2 configuration to open inbound connection through port 80:

![](./images/edit%20inbound%20rules.PNG)

we can access our running server locally and from the Internet (Source 0.0.0.0/0 means ‘from any IP address’).

 Now we check if we can access the server locally in our Ubuntu shell by, using ‘curl’ command to request our Apache HTTP Server on port 80.
 
 run command:
 
  `curl http://localhost:80`

  ![](./images/04%20check%20for%20local%20host.PNG)

  As we can see Apache web service responded to ‘curl’ command with some payload.



We test if our Apache HTTP server can respond to requests from the Internet by Open a web browser of our choice and try to access it with the following url

`http://<44.205.250.41>:80`


On seeing the following page, confirms the web server is now correctly installed and accessible through the firewall.

![](./images/05%20it%20works.PNG)


### STEP 2 — INSTALLING MYSQL


We need a relational  Database Management System (DBMS) to be able to store and manage data for the site and  MySQL is a popular relational database management system used within PHP environments, so we will use it.

To install it we will use the 'sudo apt' command

`$ sudo apt install mysql-server`

![](./images/06%20installed%20my%20sql.PNG)


 After the installation was done, logged into the MySQL console by typing:

 `$ sudo mysql`

 ![](./images/07%20sudo%20mysql.PNG)


It’s recommended that you run a security script that comes pre-installed with MySQL. This script will remove some insecure default settings and lock down access to your database system. Before running the script you will set a password for the root user, using mysql_native_password as default authentication method. We’re defining this user’s password as PassWord.1.

`ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'PassWord.1';`

![](./images/08%20mysql%20password.PNG)

Exit the MySQL shell with:

`mysql> exit`

![](./images/09%20mysql%20exit.PNG)


Start the interactive script,this will ask if you want to configure the VALIDATE PASSWORD PLUGIN.

Note: Enabling this feature is something of a judgment call.



run: `$ sudo mysql_secure_installation ` 

![](./images/10%20password%20validation.PNG)

Answer Y for yes, or anything else to continue without enabling

![](./images/11%20password%20validation%20plugin%20finished.PNG)


After we finish, we test our ability to log in to the MySQL console by typing:

 `$ sudo mysql -p`


An 'Enter Password' prompt comes up, enter root user password for access

 To exit the MySQL console, type:
 
  `mysql> exit`

 ![](./images/12%20compleated%20mysql%20installation.PNG)

 ### STEP 3 — INSTALLING PHP


 PHP is the component of our setup that will process code to display dynamic content to the end user. 



In addition to the php package, you’ll need php-mysql, a PHP module that allows PHP to communicate with MySQL-based databases. You’ll also need libapache2-mod-php to enable Apache to handle PHP files.

To install these 3 packages at once, run:

`sudo apt install php libapache2-mod-php php-mysql`
![](./images/13%20install%20php%20libapache2-mod-php%20php-mysql.PNG)

type 'y' to continue

![](./images/14%20compleated%20install%20php%20libapache2-mod-php%20php-mysql.PNG)

 Run the following command to confirm  PHP version:

 `php -v`


![](./images/15%20confirm%20php%20version.PNG)

At this point, your LAMP stack is completely installed and fully operational.


### STEP 4 — CREATE A VIRTUAL HOST FOR YOUR WEBSITE USING APACHE

In this project, we will set up a domain called projectlamp.

Create the directory for projectlamp using ‘mkdir’ command as follows:


`sudo mkdir /var/www/projectlamp`

![](./images/16%20make%20directory%20for%20project%20lamp.PNG)

Next, assign ownership of the directory with your current system user:


`sudo chown -R $USER:$USER /var/www/projectlamp`

![](./images/17%20changed%20ownership%20to%20current%20user.PNG)


Then, create and open a new configuration file in Apache’s sites-available directory using your preferred command-line editor. Here, we’ll be using vi or vim (They are the same by the way):


`sudo vi /etc/apache2/sites-available/projectlamp.conf`

![](./images/18%20create%20vi%20file.PNG)

This will create a new blank file. Paste in the following bare-bones configuration by hitting on i on the keyboard to enter the insert mode,

![](./images/19%20click%20i%20to%20into%20insert%20mode.PNG)

 and paste the text:

 `<VirtualHost *:80>
    ServerName projectlamp
    ServerAlias www.projectlamp 
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/projectlamp
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
`

![](./images/20%20copy%20and%20paste%20file.PNG)


To save and close the file, simply follow the steps below:

- Hit the esc button on the keyboard

![](./images/21%20hit%20esc%20botton.PNG)

- Type :Type wq (w for write and q for quit)

![](./images/22%20type%20wq.PNG)

- Hit ENTER to save the file

use the ls command: (to show the new file in the sites-available directory)

`sudo ls /etc/apache2/sites-available`

![](./images/23%20sudo%20ls%20%20etc%20apache2%20sites-available.PNG)

With this VirtualHost configuration, we’re telling Apache to serve projectlamp using /var/www/projectlampl as its web root directory.

We can now use a2ensite command to enable the new virtual host:

`sudo a2ensite projectlamp`

![](./images/24%20enable%20new%20virtual%20host.PNG)

Disable the default website that comes installed with Apache, so it's default configuration would'nt overwrite the virtual host. To disable Apache’s default website use a2dissite command , type:

`sudo a2dissite 000-default`

![](./images/25%20disable%20default%20site.PNG)


To make sure your configuration file doesn’t contain syntax errors, run:

`sudo apache2ctl configtest`

![](./images/26%20apache2%20config%20test.PNG)


Finally, reload Apache so these changes take effect:

`sudo systemctl reload apache2`

![](./images/27%20reload%20apache2.PNG)

The new website is now active, but the web root /var/www/projectlamp is still empty. Create an index.html file in that location so that we can test that the virtual host works as expected:


`sudo echo 'Hello LAMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectlamp/index.html
`

![](./images/28%20echo%20hello%20LAMP.PNG)



Now go to your browser and try to open your website URL using IP address:

http://54.166.73.55:80

![](./images/29%20result%20of%20echo%20hello%20LAMP.PNG)

Seeing the text from ‘echo’ command written to index.html file, means the Apache virtual host is working as expected.

### STEP 5 — ENABLE PHP ON THE WEBSITE

With the default DirectoryIndex settings on Apache, a file named index.html will always take precedence over an index.php file.

To change this behavior, you’ll need to edit the /etc/apache2/mods-enabled/dir.conf file and change the order in which the index.php file is listed within the DirectoryIndex directive:

`sudo vim /etc/apache2/mods-enabled/dir.conf`

![](./images/30%20etc%20apache2%20mods-enabled%20dir.conf.PNG)

Delete vim content with these steps


- hit esc key
- type : 
- type %d


![](./images/31%20delete%20vim%20content.PNG)

- hit enter key

hit 'i' key to insert:

`<IfModule mod_dir.c>
        #Change this:
        #DirectoryIndex index.html index.cgi index.pl index.php index.xhtml index.htm
        #To this:
        DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
</IfModule>
`

to change vim content


![](./images/32%20change%20vim%20config%20command.PNG)


After saving, close the file:

![](./images/33%20save%20config.PNG)

hit Enter to save

 you will need to reload Apache so the changes take effect:

`sudo systemctl reload apache2`

![](./images/34%20reload%20apache%202.PNG)

Finally, we will create a PHP script to test that PHP is correctly installed and configured on your server.


Create a new file named index.php inside your custom web root folder:


`vim /var/www/projectlamp/index.php`

![](./images/35%20new%20php%20folder.PNG)

 Add the following text, which is valid PHP code, inside the file:

 `<?php
phpinfo();
`

![](./images/36%20saved%20php%20file.PNG)

save and close the file, refresh the page and this is displayed:

![](./images/37%20php%20landing%20page.PNG)


since we can see this page on the browser, this means the PHP installation is working as expected.

it’s best to remove this file we created as it contains sensitive information about our PHP environment and Ubuntu server. You can use 'rm' to do so:

`sudo rm /var/www/projectlamp/index.php`

![](./images/38%20remove%20php%20landind%20page.PNG)

We are back to this display:

![](./images/39%20php%20removed.PNG)


With this we have come to the end of this project which is the implementation of LAMP stack on an EC2 instance on AWS cloud.

Thank you.

