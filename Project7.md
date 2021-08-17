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

1.Spin up a new EC2 instance with RHEL Linux 8 Operating System.

Using Mobaxterm- login to the instance

![Step1](https://user-images.githubusercontent.com/10111342/129738324-accd762b-3ced-4e13-a0af-f0970ab60329.png)

2.Configure LVM on the Server and format them as xfs

   Created a volume and attached to the NFS instance
   
   ![NFS Volume Attached](https://user-images.githubusercontent.com/10111342/129741228-2295fd00-0088-4e8f-bc21-68c7c9b6288a.png)
   
   
   
   



