## Client/Server Architecture Using A MySQL Relational Database Management System

This project demonstrate a basic client-server using MySQL Relational Database Management System (RDBMS) 


### Step1 : Create and configure two Linux-based virtual servers (EC2 instances in AWS)

Output:
![Step1](https://user-images.githubusercontent.com/10111342/128232809-9f369527-a31b-48dd-94d4-54b160fbacd1.JPG)
 
 Notes: Connected to the two instances using Mobaxterm tool
 
 
### Step2: On mysql server Linux Server install MySQL Server software.

sudo apt update > this is to update dependencies and packages on the server

sudo apt install mysql-server -y  -- to install mysql database on the server

sudo systemctl enable mysql     -- to enable mysql services
sudo mysql --version      --to confirm and version of installed mysql

![mysql installed 2](https://user-images.githubusercontent.com/10111342/128233148-8bb34f5b-fa6d-4602-9251-08c303c28025.png)


### Step3: On mysql client Linux Server install MySQL Client software.

sudo apt update > this is to update dependencies and packages on the server 

sudo apt install mysql-client -y  -- to install mysql client software on the server


### Step4: By default, both of your EC2 virtual servers are located in the same local virtual network, so they can communicate to each other using local IP addresses. Use mysql server's local IP address to connect from mysql client. MySQL server uses TCP port 3306 by default, so you will have to open it by creating a new entry in ‘Inbound rules’ in ‘mysql server’ Security Groups. For extra security, do not allow all IP addresses to reach your ‘mysql server’ – allow access only to the specific local IP address of your ‘mysql client’.

Output:
![Config inbound rules](https://user-images.githubusercontent.com/10111342/128234165-ace00a27-8d6c-4acc-856b-7e83c74c2729.png)


### Step5: Configure MySQL server to allow connections from remote hosts.

On Mysql server change the bind-address = 0.0.0.0 and save the configuration

sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf 

Output:

![Change bind - address](https://user-images.githubusercontent.com/10111342/128235060-831dd2ab-1179-42ff-b3d7-61c5e9da6533.png)

### Step6: Install mysql db on the server
sudo mysql_secure_installation;  - -ensure to configure a password and click y for all remaining questions

sudo mysql;   --to connect to the Database

Output:

![Install MySql and Login](https://user-images.githubusercontent.com/10111342/128235373-12be0392-a3d1-42a0-a774-042998951fa9.png)



 ### Step7: Create user in mysql database
 
CREATE USER 'remote_user'@'%' IDENTIFIED WITH mysql_native_password BY 'password';

Output:

![Mysql user created](https://user-images.githubusercontent.com/10111342/128235667-6e50a244-4127-4bf8-94df-cd6b610e4bf6.png)

### Step8: Create the database, Grant privileges, flush the privileges which (will reloads the grant tables in the mysql database enabling the changes to take effect without reloading or restarting mysql service

CREATE DATABASE test_db;

GRANT ALL ON test_db.* TO 'remote_user'@'%' WITH GRANT OPTION;

FLUSH PRIVILEGES;

Output:

![Create Db](https://user-images.githubusercontent.com/10111342/128235998-82b42e8b-913e-4a37-9cab-faa23b70caf7.png)

### Step9: Restart Mysql service since I made changes to the bind- address in step5

sudo systemctl restart mysql

### Step10:From mysql client Linux Server connect remotely to mysql serverDatabase Engine without using SSH You must use the mysql utility to perform this action.

Sudo mysql -u remote_user -h ‘ip address of mysql server’ -p

Output:

![Client connected to the remote server 8](https://user-images.githubusercontent.com/10111342/128236460-204aa70a-b299-4350-a45b-c56536958749.png)

Note: As you can see from the client server,  remote_user was able to connect to mysql database without SSH


### Step11: Remote_user successfully connected to remote MySQL server and can perform  SQL queries.

SELECT user();

![Remote user](https://user-images.githubusercontent.com/10111342/128237479-49e6f361-92df-4b87-bd3b-051245679aa3.png)


show databases;

![remote user can view the DB 10](https://user-images.githubusercontent.com/10111342/128236721-1a638fc8-2631-49d7-88ab-7602ed801dcf.png)










 
