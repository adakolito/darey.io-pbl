## CONTINUOUS INTEGRATION PIPELINE FOR TOOLING WEBSITE

The focus of this project is the automation of repeated routine tasks with **Jenkins** continuous integration tool. We will be demonstrating how to automatically deploy to the Tooling website straight from github repo upon every commit using webhook

# Step 1 - Install Jenkins server

1. Create an AWS EC2 server based on Ubuntu Server 20.04 LTS and name it "Jenkins"

2. Install JDK (since Jenkins is a Java-based application)
```
sudo apt update
sudo apt install default-jdk-headless


3. Install Jenkins

wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -

sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > \
    /etc/apt/sources.list.d/jenkins.list'

sudo apt update
sudo apt-get install jenkins
```

**Make sure Jenkins is up and running**

`sudo systemctl status jenkins`

![Jenkins Running 1](https://user-images.githubusercontent.com/10111342/131689869-fac41393-3f7e-4c0c-8e5c-63f630f3a082.png)

4. By default Jenkins server uses TCP port 8080 – open it by creating a new Inbound Rule in your EC2 Security Group
  
  My Security group is open to all traffice for the purpose of practice only.


5. Perform initial Jenkins setup .From your browser access
   
    http://your jenkins server public IP or Public DNS Name:8080

    You will be prompted to provide a default admin password

    ![Jenkins Web page 2](https://user-images.githubusercontent.com/10111342/131691471-6ddd2af3-d481-4e63-bb39-5eee47755bae.png) 

    Retrieve password key as shown below:


![Unclock Jenkins 3](https://user-images.githubusercontent.com/10111342/131693608-0984f1f4-86b9-4200-8829-47817e3328e1.png)

Getting started page, select ‘Install suggested plugins’ and click continue
![Screenshot 2021-09-01 105424](https://user-images.githubusercontent.com/10111342/131694117-f9c434b0-4c8a-49b2-ac3b-a8df9453a979.png)

Once plugins installation is done – create an admin user and you will get your Jenkins server address.

![Admin user Jenkins 5](https://user-images.githubusercontent.com/10111342/131694356-5671f46c-4b51-4000-9eb5-e90a71bd5079.png)

Save and continue

![Jenkins is Ready 6](https://user-images.githubusercontent.com/10111342/131694636-dc453c5c-2181-4440-a689-66f18f29a6ab.png)

Login to Jenkins and you should see the dashborad

![Jenkins Dashboarb 7](https://user-images.githubusercontent.com/10111342/131694829-35e99b4c-b68b-4dc3-98fe-2470224d7a1d.png)

# Step 2 - Configure Jenkins to retrieve source codes from GitHub using Webhooks
1. Enable webhooks in your GitHub repository settings

    Ensure to be on the specific repository- go to settings- webhook and configure as higlighted

![Configure Webhook on Github 8](https://user-images.githubusercontent.com/10111342/131695518-4db8dba3-93f0-4a4b-9625-46567e70d265.png)
![9 Webhook config](https://user-images.githubusercontent.com/10111342/131695799-381b5903-4256-4ff0-a5da-bb219bef1560.png)

2. Go to Jenkins web console, click "New Item" and create a "Freestyle project" 
![Freestyle project 9](https://user-images.githubusercontent.com/10111342/131696282-40870dc7-4032-4239-95d8-cdf67277c542.png)

To connect your GitHub repository, you will need to provide its URL, you can copy from the repository itself (Tooling url here). In mour case, I have a repo called **TOOLING**
![Tooling Repo URL 11](https://user-images.githubusercontent.com/10111342/131696537-4abfa329-f1e2-4537-80e4-5f8b9e7d11b9.png)

In configuration of your Jenkins freestyle project choose Git repository, provide there the link to your Tooling GitHub repository and credentials (user/password) so Jenkins could access files in the repository.
![Jenking credential config 11](https://user-images.githubusercontent.com/10111342/131696925-0b203a04-6ed1-44c1-a7e3-91686e8a557d.png)
![Screenshot 2021-09-01 111325](https://user-images.githubusercontent.com/10111342/131697313-00be6c8c-519e-49e5-9f77-15555b801e52.png)

Save the configuration and let us try to run the build. For now we can only do it manually.
Click "Build Now" button, if you have configured everything correctly, the build will be successful and you will see it under #1
![Image 13](https://user-images.githubusercontent.com/10111342/131697509-ac172052-4de7-40ca-ab49-7e23d1fe5700.png)
![Buildsuccesful 14](https://user-images.githubusercontent.com/10111342/131697772-699736d3-126a-434e-bbd9-57f7e1346e09.png)
![Build Succeesful 21](https://user-images.githubusercontent.com/10111342/131699245-e5ee5825-0463-472f-9b93-d1ac3d7f6248.png)


3. Click "Configure" your job/project and add these two configurations

**Configure triggering a job from GitHub webhook**

**Configure "Post-build Actions" to archive all the files – files resulted from a build are called "artifacts".**



![Buid Triggers 16](https://user-images.githubusercontent.com/10111342/131698182-52f2b789-55c6-4df0-89f8-ffba7834a182.png)


We have now configured an automated Jenkins job that receives files from GitHub by webhook trigger (this method is considered as ‘push’ because the changes are being ‘pushed’ and files transfer is initiated by GitHub). There are also other methods: trigger one job (downstreadm) from another (upstream), poll GitHub periodically and others.

By default, the artifacts are stored on Jenkins server locally

ls /var/lib/jenkins/jobs/tooling_github/builds/<build_number>/archive/



# Step 3- Configure Jenkins to copy files to NFS server via SSH
we have our artifacts saved locally on Jenkins server, the next step is to copy them to our NFS server to /mnt/apps directory. Note, from previous project we have we  have an NFS server with a mount point **/mnt/apps**

Jenkins is a highly extendable application and there are 1400+ plugins available. We will need a plugin that is called "Publish Over SSH".

1. Install "Publish Over SSH" plugin.

On main dashboard select "Manage Jenkins" and choose "Manage Plugins" menu item.
On "Available" tab search for "Publish Over SSH" plugin and install it

![Publish over SSh 17](https://user-images.githubusercontent.com/10111342/131700060-927bb494-09b0-42b2-8c05-7a01a82f905a.png)

2. Configure the job/project to copy artifacts over to NFS server.

On main dashboard select "Manage Jenkins" and choose "Configure System" menu item.

Scroll down to Publish over SSH plugin configuration section and configure it to be able to connect to your NFS server:

Provide a private key (content of .pem file that you use to connect to NFS server via SSH/Putty)

Arbitrary name

Hostname – can be private IP address of your NFS server

Username – ec2-user (since NFS server is based on EC2 with RHEL 8)

Remote directory – /mnt/apps since our Web Servers use it as a mounting point to retrieve files from the NFS server

Test the configuration and make sure the connection returns Success. Remember, that TCP port 22 on NFS server must be open to receive SSH connections.
![Jenkins config Success 18](https://user-images.githubusercontent.com/10111342/131700602-8de974b3-06f0-4268-a8be-0a5708957b60.png)
![Image 19](https://user-images.githubusercontent.com/10111342/131700908-b28f9f8a-d8a1-4f25-ba08-456f9bfa9ae9.png)
Save this configuration and go ahead, change something in README.MD  file in your GitHub Tooling repository.

Webhook will trigger a new job and in the "Console Output" of the job you will find something like this:
SSH: Transferred 25 file(s)

![Test Jenkins build final 20](https://user-images.githubusercontent.com/10111342/131701312-c56f5870-83f5-4330-8177-34fb90593bf8.png)

*Note: I had a blocker on Build#3 Permission issues on NFS Server.*

*Solution: sudo chown -R $USER:$USER /mnt/apps on the NFS server to give ec2-user the right permission.*


 ![Screenshot 2021-09-01 114209](https://user-images.githubusercontent.com/10111342/131701883-0a05b83c-4a50-4461-85e9-08aa0d66861d.png)

 To make sure that the files in /mnt/apps have been updated – connect via SSH/Putty to your NFS server and check README.MD file

cat /mnt/apps/README.md

![Readme  md file test](https://user-images.githubusercontent.com/10111342/131709351-33561e1c-5e50-4f2c-89da-30d9e7b56810.png)

Note the **GREEN** highlight " # 1 last testing" change I made on the READMe.MD file from the Tooling repo 
Revision-Mr KOle
