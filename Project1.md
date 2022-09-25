_Project 1: WEB STACK IMPLEMENTATION (LAMP STACK) IN AWS__
1. LAMP (Linux, Apache, MySQL, PHP or Python, or Perl)
- Create Security Group in AWS and assigned it to  your instance
![A-SecurityGroup](https://user-images.githubusercontent.com/10111342/190007118-12fa9529-896e-441a-866d-6015ebe856b7.png)
- Instance -Project1 Created
![B-Instance](https://user-images.githubusercontent.com/10111342/190007812-f2b76efb-795e-46fc-8692-b08d91abdeb8.png)
- SSh connection info
![C-Connect to Instance](https://user-images.githubusercontent.com/10111342/190008177-a4d227db-0bd6-4bbd-8cd0-139670f60c79.png)
- Conected to the Instance via windows terminal
![D-Logged Into the Instance via Window terminal](https://user-images.githubusercontent.com/10111342/190008628-1e5fa322-d77f-46d5-b0cc-5fbfb7cb402e.png)
- Step 1 : Install Apache Server

#Update a list of packages in package manager
```
sudo apt update

#Run apache2 package installation

sudo apt install apache2

#Verify that Apache2 is running as a service in our OS

sudo systemctl status apache2
```

![E-Apache Server Running](https://user-images.githubusercontent.com/10111342/190009050-da15b0d7-3dd1-4b53-a2c9-3312325a3df0.png)
*If server is not running due to server shutdown Use* 

`sudo systemctl enable apache2`



![F-Apache2 Webserver](https://user-images.githubusercontent.com/10111342/191115011-846d58d3-359d-4eb4-846a-9738eef69583.png)

![G-HistoryCommands](https://user-images.githubusercontent.com/10111342/191113943-dad3abcb-b2ad-493a-9d88-9e79a2b602dc.png)

- STEP 2 — INSTALLING MYSQL
Again, use ‘apt’ to acquire and install this software:
```
$ sudo apt install mysql-server
```
When prompted, confirm installation by typing Y, and then ENTER.
When the installation is finished, log in to the MySQL console by typing:
```
$ sudo mysql
```
This will connect to the MySQL server as the administrative database user root, which is inferred by the use of sudo when running this command. You should see output like this:

![H-Sudo mysql](https://user-images.githubusercontent.com/10111342/191113247-6833a3a3-847a-424f-9bb8-2b150a10e78c.PNG)

It’s recommended that you run a security script that comes pre-installed with MySQL. This script will remove some insecure default settings and lock down access to your database system. Before running the script you will set a password for the root user, using mysql_native_password as default authentication method. We’re defining this user’s password as PassWord.1
```
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'PassWord.1';
```

Type:
flush privileges;
Exit

- Start the interactive script by running:

![I-mysql secure installation](https://user-images.githubusercontent.com/10111342/191113294-cdac5ac1-f99e-407f-a25d-2a340e0c5620.png)

When you’re finished, test if you’re able to log in to the MySQL console by typing:
```
$ sudo mysql -p
```
Notice the -p flag in this command, which will prompt you for the password used after changing the root user password.
To exit the MySQL console, type:
```
mysql> exit
```
![J- Mysql login and Exit](https://user-images.githubusercontent.com/10111342/191113186-3573c668-6575-4960-ba69-0308d888cb40.png)

Notice that you need to provide a password to connect as the root user.
For increased security, it’s best to have dedicated user accounts with less expansive privileges set up for every database, especially if you plan on having multiple databases hosted on your server
Your MySQL server is now installed and secured. Next, we will install PHP, the final component in the LAMP stack.

- STEP 3 — INSTALLING PHP
To install these 3 packages at once, run:
```
sudo apt install php libapache2-mod-php php-mysql
```
Once the installation is finished, you can run the following command to confirm your PHP version:
```
php -v
```


![K-Php version](https://user-images.githubusercontent.com/10111342/191114064-be650bf2-d9da-45bd-a295-79fd67d992f1.PNG)

At this point, LAMP stack is completely installed and fully operational.
- Linux (Ubuntu)
- Apache HTTP Server
- MySQL
- PHP

To test your setup with a PHP script, it’s best to set up a proper Apache Virtual Host to hold your website’s files and folders. Virtual host allows you to have multiple websites located on a single machine and users of the websites will not even notice it.

- STEP 4 — CREATING A VIRTUAL HOST FOR YOUR WEBSITE USING APACHE

In this project, you will set up a domain called projectlamp, but you can replace this with any domain of your choice.
Apache on Ubuntu 20.04 has one server block enabled by default that is configured to serve documents from the /var/www/html directory.
We will leave this configuration as is and will add our own directory next next to the default one.
Create the directory for projectlamp using ‘mkdir’ command as follows:
```
sudo mkdir /var/www/projectlamp
```


![L-ProjectLAMP](https://user-images.githubusercontent.com/10111342/191114099-cacedc38-df5b-4642-8d58-f823dff58023.png)

Next, assign ownership of the directory with your current system user:
```
 sudo chown -R $USER:$USER /var/www/projectlamp
```
Then, create and open a new configuration file in Apache’s sites-available directory using your preferred command-line editor. Here, we’ll be using vi or vim (They are the same by the way):
```
sudo vi /etc/apache2/sites-available/projectlamp.conf
```

![M-Config file](https://user-images.githubusercontent.com/10111342/191114129-488d6cf7-d798-4a26-ac6f-28d9f2690195.PNG)


![N-Apache Virtual Host](https://user-images.githubusercontent.com/10111342/191114158-672b86ac-78c4-44ad-bb7c-77c70a7dd363.png)

![O-Index-html file](https://user-images.githubusercontent.com/10111342/191114192-e91b9131-b880-4864-9fc2-e82bec76ed19.png)

![P-PHP page](https://user-images.githubusercontent.com/10111342/191114236-9f8bc69e-50f8-48df-bbf7-9f6e6bb8f6dd.png

![P-PHPpage](https://user-images.githubusercontent.com/10111342/191114663-22df07fa-1f87-471f-bdb4-a4eaafee1e8b.png)