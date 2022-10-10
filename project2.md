# LEMP STACK IMPLEMENTATION
LEMP is an open-source web application stack used to develop web applications. The term LEMP is an acronym that represents L for the Linux Operating system, Nginx (pronounced as engine-x, hence the E in the acronym) web server, M for MySQL database, and P for PHP scripting language.

## Step 0 : Setting up the prerequisites
- Create a AWS account and Launch an EC2
- Connect to the EC2 cloud service provide with `ssh -i <private-key-name>.pem ubuntu@<Public-IP-address>`

## Step 1: Installing the NGINX web server
- Start off by updating the web server's package index `sudo apt update`
- Install Nginx web server using : `sudo apt install nginx`
- Confirm if Nginx has been successfully installed `sudo systemctl status nginx`
  ![nginxInstalled](https://user-images.githubusercontent.com/26335055/194863587-cc52db2e-acd2-455d-b154-80b0fb4911af.png)


- After server has been installed succefully, you can access it locally using `curl http://127.0.0.1:80` which returns an output of strange formmated text.
- Test if the Nginx server can responds to request on the internet by opening a web browser and access with 'http://<Public-IP-Address>:80`
  ![nginx](https://user-images.githubusercontent.com/26335055/194863496-9e3a7ace-830c-410e-8a12-e6d298af7daf.png)![lempPhPPage](https://user-images.githubusercontent.com/26335055/194879255-2e0f0bdd-e387-4bc5-a334-c40b8a7bb4bb.png)


 ## Step 2: Install MySQL
  MySQL will be installed as the Database Management System in the server to allow the storage and managing of data.
  - Use apt to install MySQL to install the software `sudo apt install mysql-server`
  - Login into MySQL console with 'sudo mysql`
  - Set up password (PassWord.1) for the root user as a default way of authentication 
    `ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'PassWord.1';`
  - Exit MySQL console with 'exit`
  
  ## Step 3: Install PHP
  PHP is needed for the processing of code and the generation of dynamic content for the Nginx web server. Unlike APACHE that embed PHP interpreter in each request, NGINX requires an external program to handle PHP processing and act as a bridge between the PHP interpreter itself and the web server. This allows for a better overall performance. 
  - Install php-fpm (PHP fastCGI process manager) and php-mysql and core PHP will be installed alongside
  `sudo apt install php-fpm php-mysql`
  
  ## Step 4: Configuring Nginx to use PHP Processor
  - Create a root directory for the domain with `sudo mkdir /var/www/projectLEMP`
  - Assign ownership of the directory with the $USER environment variable, which will reference the current system user:
  `sudo chown -R $USER:$USER /var/www/projectLEMP`
  - Open a new configuration file in Nginx’s sites-available directory using your preferred command-line editor. Used Nano
  `sudo nano /etc/nginx/sites-available/projectLEMP`
  - The command above will create a new blank file where you will paste the configuration below into.
  
  ```
    #/etc/nginx/sites-available/projectLEMP

      server {
          listen 80;
          server_name projectLEMP www.projectLEMP;
          root /var/www/projectLEMP;

          index index.html index.htm index.php;

          location / {
              try_files $uri $uri/ =404;
          }

          location ~ \.php$ {
              include snippets/fastcgi-php.conf;
              fastcgi_pass unix:/var/run/php/php8.1-fpm.sock;
           }

          location ~ /\.ht {
              deny all;
          }

      }
  ```
  
  - Activate the configuration by linking to the config file from Nginx’s sites-enabled directory:
  `sudo ln -s /etc/nginx/sites-available/projectLEMP /etc/nginx/sites-enabled/`
  - Test the configuration for syntax errors by typing
  `sudo nginx -t`
  ![lempError](https://user-images.githubusercontent.com/26335055/194876530-73c947cf-7272-40fe-b075-b772f79b1fa2.png)

  - Disable default Nginx host that is currently configured to listen on port 80 by running:
  `sudo unlink /etc/nginx/sites-enabled/default`
  - Reload Nginx to apply changes
  `sudo systemctl reload nginx`
  - Website is now active but there's need to create a html file in the web root (/var/www/projecLEMP)
  `sudo echo 'Hello LEMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectLEMP/index.html`
  - Open the website URL using the IP address
  `http://<Public-IP-Address>:80`
![lempPage](https://user-images.githubusercontent.com/26335055/194876390-8c4daf07-6d8e-416f-9582-bcdcdd86633b.png)

  ## Step 5: Testing PHP with Nginx
  - Create a new PHP file in the document root
  `sudo nano /var/www/projectLEMP/info.php`
  - Paste this line of code
  `<?php
  phpinfo();`
 - Accesss the test page using
`http://`server_domain_or_IP`/info.php`
![lempPhPPage](https://user-images.githubusercontent.com/26335055/194879330-85804dd3-07fb-4323-97ca-bc15212eea9f.png)

- Remove the PHP file for security purposes
  `sudo rm /var/www/your_domain/info.php`
