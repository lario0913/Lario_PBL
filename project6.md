# WEB SOLUTION WITH WORDPRESS

WordPress is a free and open-source content management system written in PHP and paired with MySQL or MariaDB as its backend Relational Database Management System (RDBMS).
The Project consist of two parts
1.  Configuring the storage subsystem for Web and Database servers based on Linux OS. 
2.  Installing WordPress and connect it to a remote MySQL database server.

In this project, you will have the hands-on experience that showcases Three-tier Architecture while also ensuring that the disks used to store files on the Linux servers are adequately partitioned and managed through programs such as gdisk and LVM respectively.

Step 0: Three Tier Setup
- A Laptop or PC to serve as a client
- An EC2 Linux Server as a web server (This is where you will install WordPress)
- An EC2 Linux server as a database (DB) server

For this projects, we will launch our ec2 on a very popular distribution called ‘RedHat’ (it also has a fully compatible derivative – CentOS) instead of Ubuntu and when connecting to it via SSH/Putty or any other tool, use *ec2-user* user instead of *ubuntu* user

`ec2-user@<Public-IP>`
