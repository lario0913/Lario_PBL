# LAMP STACK TECHNOLOGY IMPLEMENTATION
A technology stack is a set of frameworks and tools used to develop a software product. This set of frameworks and tools are very specifically chosen to work together in creating a well-functioning software. LAMP stack Technology is a bundle of four different software technologies; -Linux (the operating system), -Apache (the web server), -MySQL (the database server), and -PHP/Python/Perl (the progamming language).
### Prequisite
- Create an AWS account
- Launch a new EC2 instance of t2.micro family with Ubuntu Server 20.04 LTS (HVM)
- Open terminal at where the PEM file is located
- Connect to ec2 instance by running:
  
  `ssh -i <private-key-name>.pem ubuntu@<Public-IP-address>`
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
### Step 2: Installing MySQL in the Apache Server
- Use ‘apt’ to acquire and install this MySql software
- Login to MySQL server
- Set Password for root user
- Change default password  from “Password.1” to any convenient password after which you press yes to all the questions that follows
-	When you’re finished, test if you’re able to log in to the MySQL console by typing:
  
  `sudo mysql -p`
### Step 3: Installing PHP
### Step 4: Creating a virtual Host for the website using Apache
### Step 5: Enabling PHP on the website
