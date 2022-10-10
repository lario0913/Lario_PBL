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
  ![nginx](https://user-images.githubusercontent.com/26335055/194863496-9e3a7ace-830c-410e-8a12-e6d298af7daf.png)

 ## Step 2: Installing MySQL
  MySQL will be installed as the Database Management System in the server to allow the storage and managing of data.
  - Use apt to install MySQL to install the software `sudo apt install mysql-server`
  - Login into MySQL console with 'sudo mysql`
  - Set up password (PassWord.1) for the root user as a default way of authentication 
    `ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'PassWord.1';`
  - Exit MySQL console with 'exit`
  
  
