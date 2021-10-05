## __Load Balancer Solution with NGINX and SSL/TLS__

## Task: Configure an Nginx load balancer for Tooling website and ensure connections to the web solution is secured using SSL/TLS certificate

Architecture:

![Summary Diagram](https://user-images.githubusercontent.com/10111342/134198649-100f19be-752d-496e-bc68-510bc351a67e.png)

## Configure Nginx as a load balancer
 - Launch a new Ec2 intance based on Ubuntu server 20.04 LTS named Nginx LB

 -  Open port 80 (for http connections) and port 443 (for https connections) on the Nginx LB



![Port](https://user-images.githubusercontent.com/10111342/134209791-3eee6249-9872-4e15-8fbf-61b62bb1a1c6.png)

 Note: All ports are open for practice purposes only

- Update /etc/hosts file for local DNS settings with web servers' names `web1` and `web2` and their IP addresses

![EtcHosts](https://user-images.githubusercontent.com/10111342/134210296-5e13416b-27ca-43a8-ad66-55c55bdfcd38.png)

Install and configure Nginx as a load balancer to be used to point traffic to the web servers

    sudo apt update
    sudo apt install nginx

![Nginx running6](https://user-images.githubusercontent.com/10111342/134210827-0620abcf-094f-4a38-a502-6a3d153629c4.png)


Configure the Nginx load balancer using web server's name defined in /etc/hosts by editing ```` /etc/nginx/nginx.conf```





![LB webserver named defined5](https://user-images.githubusercontent.com/10111342/134211067-7daecf34-88db-498b-af40-0619a186eb87.png)

Restart Nginx service and ensure the service is running


![Nginx running6](https://user-images.githubusercontent.com/10111342/134211387-d59f468e-4e18-4ece-ba29-8b5ee926d7ce.png)


## __Register a new domain name and configure secured connection using SSL/TLS__
- Register a domain name using domain name registrars like GoDaddy, ClouDNS, or bluehost or Freenom

![Freenom Domain 7](https://user-images.githubusercontent.com/10111342/134212839-bdce17a3-31fa-4385-b0f4-382e09345327.png)

- Assign an Elastic IP on AWS to the Nginx load balancer to have a static IP address for the load balancer that does not change on reboot

![Elastic IP Config](https://user-images.githubusercontent.com/10111342/134213148-4ae2c7f9-4bc0-4997-8572-5382ee4f2226.png)

- Update an A namesservers domain name registrar to point to the Nginx lb using the nameservers

![Screenshot 2021-09-21 125103](https://user-images.githubusercontent.com/10111342/134213630-78814789-c2e4-48f7-8c9d-12ef9a76dc64.png)

The content of the name servers as shown above are from Route53 with A records created

![Route53 config9](https://user-images.githubusercontent.com/10111342/134214042-3f1f000a-6160-462d-8406-2f29114df70c.png)


- Configure Nginx to rcognize your new domain name. Update server_name directory in the /etc/nginx/nginx.conf file

![Screenshot 2021-09-21 125957](https://user-images.githubusercontent.com/10111342/134214975-97ce5dfa-3065-432f-9f0f-f7d318d48d5b.png)
 

 - Install Php on the Nginx load balancer to avoid 502 Bad Gateway error when trying to access the new domain assigned to the LB

       sudo apt install software-properties-common
       sudo add-apt-repository ppa:ondrej/php
       sudo apt update
       sudo apt install php7.4 php7.4-fpm
 After installing the packages php7.4-fpm service will automatically be started. To make sure, type:
       sudo systemctl status php7.4-fpm

- The domain can now be accessed on the internet, and it loads the tooling website as an unsecured site

![tooling iduh galogin](https://user-images.githubusercontent.com/10111342/134265113-3301b9df-946f-476b-8ed0-29568c233a5d.png)

- Next install Snapd-a software packaging and installing system to be used to install CertBot

      sudo apt install snapd
      sudo systemctl status snapd

- Install CertBot

       sudo snap install --classic certbot

-  Request a new SSL/TLS certifcate for the nginx domain, following the instructions of the certbot

       sudo ln -s /snap/bin/certbot /usr/bin/certbot
       sudo certbot --nginx

- Configure certbot to renew the certificate at least every 60 days (LetsEncrypt certificate are valid for 90 days)

      Testing certificate renew command sudo certbot renew --dry-run
      Best practice: Schedule a job that runs the renew command periodically using cronjobs

      crontab -e
      e.g Adding the following line to the crontab file will run the renew command 8:00am everyday

      * 8 * * *   root /usr/bin/certbot renew > /dev/null 2>&1

-  Secure access to tooling website and Certificate details:

![Certificates 10](https://user-images.githubusercontent.com/10111342/134266148-d3c5bc88-0176-4b44-b344-d5d56cf9b433.png)

