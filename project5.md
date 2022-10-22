# IMPLEMENT a CLIENT-SERVER ARCHITECTURE USING MYSQL DBMS
Client-Server refers to an architecture in which two or more computers are connected together over a network to send and receive requests between one another. 
In their communication, each machine has its own role: the machine sending requests is usually referred as "Client" and the machine responding (serving) is called "Server"

If a Database is added to the set up, we will get the diagram below:

![architecture](https://user-images.githubusercontent.com/26335055/197334049-bb2d1e2a-6ed7-4418-8b6d-3e5096f10962.png)


## Step 1: Create and configure two Linux-based virtual servers (EC2 instances in AWS).

```
Server A name - `mysql server`
Server B name - `mysql client`
```

![ec2](https://user-images.githubusercontent.com/26335055/197334155-d8155cd9-5e23-4080-ba5f-94a75235ddc9.png)

## Step 2: Install MYSQL server software on the Linux Server A (mysql server)
- Run the command `sudo apt install mysql-server`
- In the MySQL shell, run the the ALTER command to secure root user access to the once that requires passowrd. Feel free to change *root* and *password* to desired and strong string that can be used as password

`ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password';`
- Create a user and database. Make sure all access is given to the new user created.

```
CREATE USER 'Lario'@'%' IDENTIFIED WITH mysql_native_password BY '091393';
CREATE DATABASE larioDB;
GRANT ALL PRIVILEGES ON larioDB.* TO 'Lario'@'%' WITH GRANT OPTION;
```
- Show the database to make sure its created. `show databases;`

![showdb](https://user-images.githubusercontent.com/26335055/197334575-79cc5001-bb4e-4aca-8746-1e494bd1c2ee.png)

## Step 3: Install MYSQL client software on the Linux Server A (mysql client)
- Run the command `sudo apt install mysql-client`
- Check if its installed successfully `mysql -v`

## Step 4: Configuring the Servers to allow communication through Local IP addresses
