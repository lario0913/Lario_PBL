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

## Step 1: Prepare The Web Server
1.  Launch an EC2 instance that will serve as "Web Server". Create 3 volumes in the same AZ as your Web Server EC2, each of 10 GiB.

![vol-1](https://user-images.githubusercontent.com/26335055/198114570-db0afb41-c681-4dd3-ac40-51c6a293a08f.png)

![wp-vol-created](https://user-images.githubusercontent.com/26335055/198114631-3225028d-5fc6-4728-b8fb-094ffd10710a.png)

2.  Attach all three volumes one by one to your Web Server EC2 instance

![vol-2](https://user-images.githubusercontent.com/26335055/198114465-ac5717c5-44c2-4785-b5b7-25790dffd6ea.png)

3.  Open up the Linux terminal to begin configuration and use `lsblk` to inspect what block device are attached to the server. Make sure you see all 3 newly created block devices there – their names will likely be xvdf, xvdh, xvdg.

![wp-show-blocks](https://user-images.githubusercontent.com/26335055/198115471-a0f94f3d-64b7-43f8-a8de-6f6357adc7ef.png)

4.  Use df -h command to see all mounts and free space on your server
5.  Use gdisk utility to create a single partition on each of the 3 disks by running `sudo gdisk /dev/xvdf`
      - enter *n* to create new partition
      - set *partition number, First Sector, and Last Sector* to default by pressing enter key respectively
      - Change *Hex Code* to 8E00
      - Enter "w" to write and Y to proceed after which the operation will be successful

![wp-setting-partition](https://user-images.githubusercontent.com/26335055/198115735-523a10c8-7c82-4d0b-845c-4689e213b973.png)

6. Inspect the partitions made to the blocks using 'lsblk`

 ![wp-partitopn](https://user-images.githubusercontent.com/26335055/198117126-e1c18d76-bab1-4940-9c24-f89c202b06c5.png)

