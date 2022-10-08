# LAMP STACK TECHNOLOGY IMPLEMENTATION
A technology stack is a set of frameworks and tools used to develop a software product. This set of frameworks and tools are very specifically chosen to work together in creating a well-functioning software. LAMP stack Technology is a bundle of four different software technologies; -Linux (the operating system), -Apache (the web server), -MySQL (the database server), and -PHP/Python/Perl (the progamming language).
### Prequisite
- Create an AWS account
- Launch a new EC2 instance of t2.micro family with Ubuntu Server 20.04 LTS (HVM)
- Open terminal at where the PEM file is located
- Connect to ec2 instance by running:
  
  `ssh -i <private-key-name>.pem ubuntu@<Public-IP-address>`
  
  ![prerequisite](https://user-images.githubusercontent.com/26335055/194688682-79dd59a7-0932-4d2d-bfa3-132bca08ce71.png)

### Step 1: Installing Apache Server
- Update the list of packages in the package manager

  `sudo apt-get update`
- Run Apache2 package Installation

  `sudo apt-get install apache2`
- Verify that the Apache Server is running

  `sudo systemctl status apache2`
- Edit the Inbound rules of the instances in the security groups so that the web server can receive traffic from all web users
- Open a web browser to show how the Apache HTTP server can respond to requests from the Internet.

  `http://<Public-IP-Address>:80`
  
  ![web page](https://user-images.githubusercontent.com/26335055/194688763-47f88a6c-40b7-416b-99cf-7a8de90f8e7a.png)

### Step 2: Installing MySQL in the Apache Server
- Use ‘apt’ to acquire and install this MySql software

  `sudo mysql`
- Login to MySQL server

  `sudo apt install mysql-server`
- Set Password for root user

  `ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'PassWord.1';`
- Change default password  from “Password.1” to any convenient password after which you press yes to all the questions that follows

  `sudo mysql_secure_installation`
  
  ![mysql-1](https://user-images.githubusercontent.com/26335055/194688806-2a9d6525-6244-4751-877c-30504c30182b.png)

-	When you’re finished, test if you’re able to log in to the MySQL console by typing:
  
  `sudo mysql -p`
- Exit the MySQL console

  `mysql> exit`
### Step 3: Installing PHP
PHP will help to process the code to display dynamic content to the end user. Alongside PHP, php-mysql and libapache2-mod-php will be installed to communicate with MySQL-based databases and enabling Apache to handle PHP files respectively.
- Install PHP, libapache2-mode-php and php-mysql

  'sudo apt install php libapache2-mod-php php-mysql`
- Confirm your php version

  `php -v`
### Step 4: Creating a virtual Host for the website using Apache
-	Create the directory for projectlamp using ‘mkdir’ command as follows

  `sudo mkdir /var/www/projectlamp`
-	Next, assign ownership of the directory with your current system user:

  `sudo chown -R $USER:$USER /var/www/projectlamp`
-	Then, create and open a new configuration file in Apache’s sites-available directory using your preferred command-line editor. Here, we’ll be using vi or vim

  `sudo vi /etc/apache2/sites-available/projectlamp.conf`
-	This will create a new blank file. Paste in the following bare-bones configuration by hitting on i on the keyboard to enter the insert mode, and paste the text:

  ```
  <VirtualHost *:80>
   ServerName projectlamp
    ServerAlias www.projectlamp 
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/projectlamp
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
-	To save and close the file, simply follow the steps below
    - Hit the esc button on the keyboard
    - Type :
    - Type wq (W to write and q to quit)
    - Hit ENTER key to save the file
-	You can now use a2ensite command to enable the new virtual host:

  `sudo a2ensite projectlamp`
-	To disable Apache’s default website use a2dissite command , type

  `sudo a2dissite 000-default`
-	To make sure your configuration file doesn’t contain syntax errors, run:

  `sudo apache2ctl configtest`
-	Finally, reload Apache so these changes take effect:

  `sudo systemctl reload apache2`
-	Create an index.html file in that location so that we can test that the virtual host works as expected

  `sudo echo 'Hello LAMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectlamp/index.html`
-	Now go to your browser and try to open your website URL using IP address

  `http://<Public-IP-Address>:80`
  
  ![indexpage](https://user-images.githubusercontent.com/26335055/194688849-ae4ed762-fa69-42d2-9a2b-5702f6c42f81.png)

### Step 5: Enabling PHP on the website
-	Edit the /etc/apache2/mods-enabled/dir.conf file and change the order in which the index.php file is listed within the DirectoryIndex directive:

  `sudo vim /etc/apache2/mods-enabled/dir.conf`
-	Rearranged the files with index.php as the first, index.html as the second and so on

  ```
  <IfModule mod_dir.c>
       #Change this:
       #DirectoryIndex index.html index.cgi index.pl index.php index.xhtml index.htm
</IfModule>

  <IfModule mod_dir.c>
      #To this:
        DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
</IfModule>
```
-	After saving, reload the apache serve:

  `sudo systemctl reload apache2`
-	Create a PHP script to test that PHP is correctly installed and configured on your server.
-	Create a new file named 'index.php' in your custom web root folder

  `vim /var/www/projectlamp/index.php`
-	Enter the following text:

  ```
    <? php
    phpinfo();
  ```
-	It’s best to remove the file you created as it contains sensitive information about your PHP environment -and your Ubuntu server.

  `sudo rm /var/www/projectlamp/index.php`
