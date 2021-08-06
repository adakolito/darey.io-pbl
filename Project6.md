### WEB SOLUTION WITH WORDPRESS


This project consists of two parts:
Configure storage subsystem for Web and Database servers based on Linux OS. The focus of this part is to display practical experience of working with disks, partitions and volumes in Linux.
Install WordPress and connect it to a remote MySQL database server. 

### Part 1

Configure storage subsystem for Web and Database servers based on Linux OS.


### Step 1:
Launch an EC2 instance that will serve as "Web Server". Create 3 volumes in the same AZ as your Web Server EC2, each of 10 GiB.
![Ec2 Instance1](https://user-images.githubusercontent.com/10111342/128539932-9b549e18-08ce-43f6-9a71-111b8b6599b9.png)-bd3b-051245679aa3.png)


Created 3 volumes and attached them to the web-server instance

![Volumes2](https://user-images.githubusercontent.com/10111342/128540741-86a8f45d-bb22-4d60-a5ff-054ce3e90ba8.png)

Note: All the voulume disk are attached to the servers


### Step 2/3:
Connected to the Web server instance using MobaXterm 

Type lsblk on the terminal to inspect what block devices are attached to the server

![Connected to server3](https://user-images.githubusercontent.com/10111342/128541167-b7c31fc0-07c7-4505-bdea-3e784de9214c.png)

### Step 4: Use df -h command to see all mounts and free space on your server

### Step 5: Use gdisk utility to create a single partition on each of the 3 disks
sudo gdisk /dev/xvdf

sudo gdisk /dev/xvdg

sudo gdisk /dev/xvdh

### Step 6: Use lsblk utility to view the newly configured partition on each of the 3 disks

Output:

![Disk partition4](https://user-images.githubusercontent.com/10111342/128542554-d47670a7-09cf-4624-a60d-06c4cd2f6098.png)


### Step 7: Install lvm2 package using sudo yum install lvm2 

Run sudo lvmdiskscan command to check for available partitions.

Note: Previously, in Ubuntu we used apt command to install packages, in RedHat/CentOS a different package manager is used, so we shall use yum command instead.

Outputs: 

using which lvm  command confirms lvm is installed

![Lvm Installed5](https://user-images.githubusercontent.com/10111342/128543203-d9089856-5289-475d-9ae9-6490b58d2bb6.png)

![LvmChecks5B](https://user-images.githubusercontent.com/10111342/128543430-3a8b3582-c194-4411-b34e-0b58b7b66782.png)

### Step 8 :Use pvcreate utility to mark each of 3 disks as physical volumes (PVs) to be used by LVM

sudo pvcreate /dev/xvdf /dev/xvdg1 /dev/xvdh1

![PVS](https://user-images.githubusercontent.com/10111342/128544118-4f20a763-8197-4f0b-878e-9bb2137f472d.png)

### Step 9:Use vgcreate utility to add all 3 PVs to a volume group (VG). Name the VG webdata-vg 

sudo vgcreate webdata-vg /dev/xvdh1 /dev/xvdg1 /dev/xvdf1

![Vgcreate7](https://user-images.githubusercontent.com/10111342/128544347-49cd1fd5-8f1d-4d34-b087-51343b2dc2a8.png)


### Verify that your VG has been created successfully by running 

sudo vgs

![VgConfirm8](https://user-images.githubusercontent.com/10111342/128544524-447f6533-c818-4f6e-9ce7-e31cbf23c770.png)

## Step 10:Use lvcreate utility to create 2 logical volumes. apps-lv (Use half of the PV size), and logs-lv Use the remaining space of the PV size. 

NOTE: apps-lv will be used to store data for the Website while, logs-lv will be used to store data for logs.

sudo lvcreate -n apps-lv -L 14G webdata-vg

sudo lvcreate -n logs-lv -L 14G webdata-vg

###Verify that your Logical Volume has been created successfully by running 

sudo lvs

![Lvs](https://user-images.githubusercontent.com/10111342/128545044-f2906ddb-d9c1-4840-9d59-01b13ee04eca.png)

### Verify the entire setup

sudo vgdisplay -v #view complete setup - VG, PV, and LV

sudo lsblk

![Verify Setup10](https://user-images.githubusercontent.com/10111342/128545330-113755f1-0e61-4e1c-bbe2-d6abc2b54953.png)


### Step 11: Use Mkfs.ext4 to format the logical volumes with ext4 filesystem

sudo mkfs -t ext4 /dev/webdata-vg/apps-lv

sudo mkfs -t ext4 /dev/webdata-vg/logs-lv

![ext411](https://user-images.githubusercontent.com/10111342/128545504-38d935f3-4332-441b-a486-aec7f5904c24.png)

### Step 12: Create /var/www/html directory to store website file
 
 sudo mkdir -p/var/www/html
 
### Step 13: Create /home/recovery/logs to store backup of log data

sudo mkdir -p /home/recovery/logs

### Step 14 :Mount /var/www/html on apps-lv logical volume

sudo mount /dev/webdata-vg/apps-lv /var/www/html/

![step13](https://user-images.githubusercontent.com/10111342/128546181-1c270928-9e93-476e-a8ec-3a69dcef679d.png)

Use rsync utility to backup all the files in the log directory /var/log into /home/recovery/logs (This is required before mounting the file system)

sudo rsync -av /var/log/. /home/recovery/logs/

### Step 15:
Mount /var/log on logs-lv logical volume. (Note that all the existing data on /var/log will be deleted. That is why step 14 above is veryimportant)

sudo mount /dev/webdata-vg/logs-lv /var/log

![Screenshot 2021-08-06 131030](https://user-images.githubusercontent.com/10111342/128547453-84ac48a4-cc3b-4ea8-9172-aaa30bd83f9e.png)


### Restore log files back into /var/log directory
sudo rsync -av /home/recovery/logs/  /var/log

![varlog copied back14](https://user-images.githubusercontent.com/10111342/128547803-56d8b56d-3a6b-45b0-a504-7a49c2d1be1f.png)

### Step 16: Update /etc/fstab file so that the mount configuration will persist after restart of the server.

sudo vi /etc/fstab
   
sudo mount -a

sudo systemctl daemon-reload

![Fstabupdated16](https://user-images.githubusercontent.com/10111342/128548021-b27e907f-8250-4eb8-9cf8-37259e39d0aa.png)


### Part 2 — Prepare the Database Server

### Step 1: Repeated steps as configured on the web-server 

![Repeated Disk Config on DB Server17](https://user-images.githubusercontent.com/10111342/128548632-06979a63-6148-4ad9-be83-1524d8e3d645.png)

Checked to confirm that logical volume was created and mounted on the database server

![Screenshot 2021-08-06 132354](https://user-images.githubusercontent.com/10111342/128548941-9e877ae6-c7eb-49d1-b7fd-fc9f818f69d7.png)

### Step 2: Install WordPress on your Web Server EC2

sudo yum -y update to Update the repository
 
###Step 3: Install wget, Apache and it’s dependencies

sudo yum -y install wget httpd php php-mysqlnd php-fpm php-json

### Step 4 :Start Apache

sudo systemctl enable httpd

sudo systemctl start httpd

![Apache 18](https://user-images.githubusercontent.com/10111342/128549186-f8a4f936-7bdf-4ea0-8c8d-642d391382f2.png)

### Step 5: Install PHP and it’s depemdencies

sudo yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm

sudo yum install yum-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm

sudo yum module list php

sudo yum module reset php

sudo yum module enable php:remi-7.4

sudo yum install php php-opcache php-gd php-curl php-mysqlnd

sudo systemctl start php-fpm

sudo systemctl enable php-fpm

Sudo setsebool -P httpd_execmem 1

![PHP Running19](https://user-images.githubusercontent.com/10111342/128549339-1b35c56f-a48b-48e1-87b0-9208224da70d.png)

### Step 6: Restart Apachee

sudo systemctl restart httpd

Tested the Apache server and its running 

![Apache test20](https://user-images.githubusercontent.com/10111342/128549508-0f92fcce-4448-4afa-829b-ee6d7fe0f951.png)

### Step 7: Download wordpress and copy wordpress to var/www/html
mkdir wordpress

cd   wordpress
 
sudo wget http://wordpress.org/latest.tar.gz
  
sudo tar xzvf latest.tar.gz

sudo rm -rf latest.tar.gz
  
Sudo cp wordpress/wp-config-sample.php wordpress/wp-config.php
  
Sudo cp -R wordpress /var/www/html/

![word press 21](https://user-images.githubusercontent.com/10111342/128549848-00ad6268-6b5d-4c76-96b8-8399de439ea2.png)

### Step 8 Install MySQL on your DB Server EC2

sudo yum update

sudo yum install mysql-server

sudo systemctl restart mysqld

sudo systemctl enable mysqld

sudo systemctl status mysqld

![MySqlRunning22](https://user-images.githubusercontent.com/10111342/128550196-7fcb3767-42b9-4e76-a7be-579659860c7f.png)




### Step 9: Configure DB to work with WordPress

sudo mysql

CREATE DATABASE wordpress;

CREATE USER `myuser`@`<Web-Server-Private-IP-Address>` IDENTIFIED BY 'mypass';

GRANT ALL ON wordpress.* TO 'myuser'@'<Web-Server-Private-IP-Address>';
  
FLUSH PRIVILEGES;
  
SHOW DATABASES;
  
exit
 
  ![Mysqldbcreated23](https://user-images.githubusercontent.com/10111342/128550394-5c2ec67f-f627-43f1-9782-d20cb6a259b2.png)
  
 ### Step 10 : Configure WordPress to connect to remote database.
  
Add inbound rule on the instance
  
  ![Inbound rule DBServer23](https://user-images.githubusercontent.com/10111342/128550572-08462ccf-c574-407f-b44b-77a04314c40c.png)
  
### Step 11  :Install MySQL client and test that you can connect from your Web Server to your DB server by using mysql-client

sudo yum install mysql
  
sudo mysql -u myuser -p -h <DB-Server-Private-IP-address>
  

Verify if you can successfully execute SHOW DATABASES;command and see a list of existing databases
  
SHOW DATABASES;


![connected from web to Db server24](https://user-images.githubusercontent.com/10111342/128550922-64fbe288-4c17-4e35-9b14-eae78a01c647.png)
![Show Databases24](https://user-images.githubusercontent.com/10111342/128550974-28362306-cf16-4c83-b46e-316babf43708.png)


### Step 12:Change permissions and configuration so Apache could use WordPress
  
sudo vi wp-config.php  - change DB host name, username password and localhost ip address

sudo systemctl restart httpd
  
sudo mv /etc/httpd/conf.d/welcome.conf /etc/httpd/conf.d/welcome.conf_backup
  
![permission on Apache](https://user-images.githubusercontent.com/10111342/128551541-ecf65502-d457-4cc3-9bd8-3851fa9a5012.png)
  
 Enable TCP port 80 in Inbound Rules configuration for your Web Server EC2
  
![port 80 25](https://user-images.githubusercontent.com/10111342/128551633-eff4134d-060e-4fb9-82ae-5850d1389524.png)
 
 ### Using the public ip of the web-server, I was able to access wordpress (Using my created username and password)
  
 ![Word press installed and logged in 25](https://user-images.githubusercontent.com/10111342/128552148-850f8d28-e18a-487d-9965-63d98821db74.png)
![Wordpress Page 26](https://user-images.githubusercontent.com/10111342/128552165-34802411-3015-4a06-ab3a-8c4b8c844e48.png)




