## DEVOPS TOOLING WEBSITE SOLUTION
The goal of this project is to Implement a tooling website solution which makes access to DevOps tools within the corporate infrastructure easily accessible.
In this project I will implement a solution that consists of following components:

Infrastructure:
AWS

Webserver Linux:Red Hat Enterprise Linux 8

Database Server: Ubuntu 20.04 + MySQL

Storage Server: Red Hat Enterprise Linux 8 + NFS Server

Programming Language: PHP

Code Repository: GitHub

###Step 1 – Prepare NFS Server

Spin up a new EC2 instance with RHEL Linux 8 Operating System.

Using Mobaxterm- login to the instance

![Step1](https://user-images.githubusercontent.com/10111342/129738324-accd762b-3ced-4e13-a0af-f0970ab60329.png)

Configure LVM on the Server and format them as xfs

   Created a volume and attached to the NFS instance
   
   ![NFS Volume Attached](https://user-images.githubusercontent.com/10111342/129741228-2295fd00-0088-4e8f-bc21-68c7c9b6288a.png)
   
   Create disk partion, physical volume, vloume group and 3  logical volumes
   
   Create Mount points on /mnt directory
   
   Formatted the 3 volumes with the xfs file system

`sudo mkfs.xfs /dev/webdata-vg/lv-apps`

`sudo mkfs.xfs /dev/webdata-vg/lv-logs`

`sudo mkfs.xfs /dev/webdata-vg/lv-opt
   
   
   ![Created all logical volumes](https://user-images.githubusercontent.com/10111342/129936073-62972d2c-9884-4436-8da9-f6628278e311.png)
   
   
   ![logical volumes created](https://user-images.githubusercontent.com/10111342/129937735-4d996ebc-07d2-4a12-9502-a74caae73f33.png)

   
   Update the /etc/fstab file so the mount configuration will persist. If the /etc/fstab file is not updated and the server is restarted the configuration will be deleted
   
   
![FstabStep3 -4](https://user-images.githubusercontent.com/10111342/129937047-43a75582-846d-4a60-b82d-b77d793113de.png)

   
   Install NFS Server

`sudo yum -y update`

`sudo yum install nfs-utils -y`

`sudo systemctl start nfs-server.service`

`sudo systemctl enable nfs-server.service`

`sudo systemctl status nfs-server.service`


![Screenshot 2021-08-18 124653](https://user-images.githubusercontent.com/10111342/129938881-dde2034d-3112-4fb0-afe4-88dca2498e5a.png)

   Set up permission to allow the Web servers to read, write and execute files on the NFS server and restarted the NFS server - 
   
sudo chown -R nobody: /mnt/apps

sudo chown -R nobody: /mnt/logs

sudo chown -R nobody: /mnt/opt
 
sudo chmod -R 777 /mnt/apps

sudo chmod -R 777 /mnt/logs

sudo chmod -R 777 /mnt/opt

sudo systemctl restart nfs-server.service

Configure access to NFS for clients within the same subnet (example of Subnet CIDR – 172.31.32.0/20 ):

![4](https://user-images.githubusercontent.com/10111342/129944295-4fd6b967-0971-4310-921b-816bb1f90a5f.png)


Export the mounts for web servers' subnet cidr to connect as clients. For simplicity, you will install your all three Web Servers inside the same subnet, but in production set up you would probably want to separate each tier inside its own subnet for higher level of security.To check your subnet cidropen your EC2 details in AWS web console and locate ‘Networking’ tab and open a Subnet link:

![Screenshot 2021-08-18 125215](https://user-images.githubusercontent.com/10111342/129940132-0c9952c8-67c8-426a-bc26-715ddde250ee.png)

Check which port is used by NFS server and open it using Security Groups (add new Inbound Rule)

![Screenshot 2021-08-18 131746](https://user-images.githubusercontent.com/10111342/129943118-92bf7524-eb38-4254-adc4-b7bcd3f9e0ff.png)

![Inbound rulesStep1-6](https://user-images.githubusercontent.com/10111342/129940703-f3a9c4d5-a609-4a09-8a24-a7f3a41f2ef5.png)

### Step 2 — Configure the Database Server

Login to Database server (Ubuntu) and run sudo apt update  -- to update the server
 
Install MySQL server

Create a database and name it tooling

Create a database user and name it webaccess

Grant permission to webaccess user on tooling database to do anything only from the webservers  subnet cidr

 ![Step2-1to 4](https://user-images.githubusercontent.com/10111342/129945046-68e906b6-77ae-492b-af3b-6baa97fba0fe.png)
 
 
![Webaccess user](https://user-images.githubusercontent.com/10111342/129946322-6b6b78b9-0d96-41f4-864e-7a5cef7b0902.png)


Step 3 — Prepare the Web Servers

Launch a new EC2 instance with RHEL 8 Operating System

Install NFS client

sudo yum install nfs-utils nfs4-acl-tools -y  --to install NFS clients

Mount /var/www/and target the NFS server’s export for apps

sudo mkdir /var/www

sudo mount -t nfs -o rw,nosuid <NFS-Server-Private-IP-Address>:/mnt/apps /var/www


![Screenshot 2021-08-18 135329](https://user-images.githubusercontent.com/10111342/129947947-aa27aa34-d8b4-46bb-9f27-836292b1138f.png)

Verify that NFS was mounted successfully by running df -h. Make sure that the changes will persist on Web Server after reboot:

sudo vi /etc/fstab

add following line <NFS-Server-Private-IP-Address>:/mnt/apps /var/www nfs defaults 0 0


![Screenshot 2021-08-18 135620](https://user-images.githubusercontent.com/10111342/129948319-eb628e7c-677d-409b-ae6c-d99f6894b220.png)

Install Apache

![apache](https://user-images.githubusercontent.com/10111342/129948540-3b4ab0bd-8efb-43fe-b841-b394c4733201.png)

Verify that Apache files and directories are available on the Web Server in/var/www and also on the NFS server in /mnt/apps. If you see the same files – it means NFS is mounted correctly.

![Screenshot 2021-08-18 135932](https://user-images.githubusercontent.com/10111342/129948758-d1691136-8094-4a06-b6a3-62fd52fca5f7.png)

Deploy the tooling website’s code to the Webserver by Forking the tooling source code from Darey.io ⦁	Github⦁	 Account . 

Before cloning the github account install git

`sudo yum install git -y`

Clone the github account

![git clone](https://user-images.githubusercontent.com/10111342/129949120-d223b48b-3649-40d9-b3aa-83d06ac9f867.png)

	Deploy the html folder to /var/www/html directory
   
   ![Screenshot 2021-08-18 140502](https://user-images.githubusercontent.com/10111342/129949464-51dcc271-3bac-4a97-b859-16640cb1edc3.png)
   
   Update the website’s configuration to connect to the database
   
    ⦁	Updated the functions.php as shown in the image below:
    
`sudo vi functions.php`

![FunctionPHP edit](https://user-images.githubusercontent.com/10111342/129951065-20f752ef-b631-405f-acff-43ed7bd75090.png)


Install mysql-server so that the web server can communicate with mysql-server on the database server. 

sudo yum install mysql-server -y

Update the website’s configuration to connect to the database (in functions.php file). Apply tooling-db.sql script.

![Applied Tooling](https://user-images.githubusercontent.com/10111342/129950390-15f50ded-2589-41d5-975d-65568254c7c2.png)

Create in MySQL a new admin user with username: myuser and password: password:

![created myuser on mysql db](https://user-images.githubusercontent.com/10111342/129950511-34257e27-eccd-4f9a-8e4e-e22261f93f61.png)

Open the website in your browser http://<Web-Server-Public-IP-Address-or-Public-DNS-Name>/index.php

![Login Page](https://user-images.githubusercontent.com/10111342/129950697-bfd73fe2-dafd-4cbe-998c-d109f16919f5.png)

 Using admin usernane and admin password you can access the Tooling Page as show below:
  
 ![Tooling page](https://user-images.githubusercontent.com/10111342/130269769-90ab6075-0cd3-4f22-b66f-0c1a02cc327a.png)


   
