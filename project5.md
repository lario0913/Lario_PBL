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
![remotedb](https://user-images.githubusercontent.com/26335055/197337168-f4dee4d1-0085-4ba7-917e-503723739efa.png)

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
By default, both of your EC2 virtual servers are located in the same local virtual network, so they can communicate to each other using local IP addresses.
- MySQL server uses TCP port 3306 by default, so you will have to open it by creating a new entry in ‘Inbound rules’ in ‘mysql server’ Security Groups
- configure MySQL server to allow connections from remote hosts.
    - sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf
    - locate *bind-address = 127.0.0.1* in the file
    - Replace ‘127.0.0.1’ to ‘0.0.0.0’
- For extra security, do not allow all IP addresses to reach your ‘mysql server’ – allow access only to the specific local IP address of your ‘mysql client’.
- Use mysql server's local IP address to connect from mysql client.

  `mysql -u USERNAME -p -h SERVER-IP`
  
  ![remotedb](https://user-images.githubusercontent.com/26335055/197337184-075047a8-6a98-4b2b-9f2f-5e6443856685.png)

  
- show server's database on the client mysql interface. `Show databases;`

![showdfc](https://user-images.githubusercontent.com/26335055/197337094-71600f74-7c4a-4757-aed5-de3b6d343785.png)

