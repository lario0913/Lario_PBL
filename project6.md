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

7.    Install lvm2 package using `sudo yum install lvm2` and Run `sudo lvmdiskscan` command to check for available partitions.
8.    Use pvcreate utility to mark each of 3 disks as physical volumes (PVs) to be used by LVM

      ```
      sudo pvcreate /dev/xvdf1
      sudo pvcreate /dev/xvdg1
      sudo pvcreate /dev/xvdh1
      ```
9.    Verify that your Physical volume has been created successfully by running sudo pvs
10.   Use vgcreate utility to add all 3 PVs to a volume group (VG). Name the VG webdata-vg

      `sudo vgcreate webdata-vg /dev/xvdh1 /dev/xvdg1 /dev/xvdf1`
11.   Verify that your VG has been created successfully by running `sudo vgs`

![vgs](https://user-images.githubusercontent.com/26335055/198135460-3d075eb0-52dd-4813-91bd-ef59f2d0991a.png)

12.   Use lvcreate utility to create 2 logical volumes. apps-lv (Use half of the PV size), and logs-lv Use the remaining space of the PV size. NOTE: apps-lv will be used to store data for the Website while, logs-lv will be used to store data for logs.

      `sudo lvcreate -n apps-lv -L 14G webdata-vg`
      `sudo lvcreate -n logs-lv -L 14G webdata-vg`
13.   Verify that your Logical Volume has been created successfully by running `sudo lvs`
14.   Verify the entire setup

      `sudo vgdisplay -v #view complete setup - VG, PV, and LV`
      `sudo lsblk`

![entire](https://user-images.githubusercontent.com/26335055/198151128-be754f76-53e1-48fb-9044-c8680c9ffea7.png)

15.   Use mkfs.ext4 to format the logical volumes with ext4 filesystem

      `sudo mkfs -t ext4 /dev/webdata-vg/apps-lv`
      `sudo mkfs -t ext4 /dev/webdata-vg/logs-lv`
16.   Create /var/www/html directory to store website files `sudo mkdir -p /var/www/html`
17.   Create /home/recovery/logs to store backup of log data `sudo mkdir -p /home/recovery/logs`
18.   Mount /var/www/html on apps-lv logical volume `sudo mount /dev/webdata-vg/apps-lv /var/www/html/`
19.   Use rsync utility to backup all the files in the log directory /var/log into /home/recovery/logs

      `sudo rsync -av /var/log/. /home/recovery/logs/`
20.   Mount /var/log on logs-lv logical volume. `sudo rsync -av /home/recovery/logs/. /var/log`

## Step 2: Update the /etc/fstab File
1. Get the UUID of the device as it will be used to update the /etc/fstab. Run `sudo blkid`

![6copyid](https://user-images.githubusercontent.com/26335055/198158064-2724485e-fecb-420e-b5f8-e9e637859cee.png)
2. Update /etc/fstab in this format using your own UUID and rememeber to remove the leading and ending quotes. `sudo vi /etc/fstab`

      ![6saveid](https://user-images.githubusercontent.com/26335055/198158283-8372b303-b1ed-493d-b7f6-c471b6fdfd8d.png)

3. Test the configuration and reload the daemon
      
      ```
       sudo mount -a
       sudo systemctl daemon-reload
      ```
4. Verify your setup by running `df -h`, output must look like this:

![6output](https://user-images.githubusercontent.com/26335055/198158216-c90b5173-a95b-4abb-a96e-a14de19319ee.png)

## Step 3: Prepare the Database Server
Launch a second RedHat EC2 instance that will have a role – ‘DB Server’
Repeat the same steps as for the Web Server, but instead of apps-lv create db-lv and mount it to /db directory instead of /var/www/html/.




