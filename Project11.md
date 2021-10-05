   **Ansible Configuration Management**   
   *** 
    
Task:
***

* Install and configure Ansible client to act as a Bastion host/Jump  server
* Create a simple Ansible playbook to automate server configuration

**Install and configure Ansible on EC2 instance**
***
* The same EC2 instance used for jenkins in project 9 will be used as Ansible server in this project.
* Create Github repository named `ansble-config-mgt`
* Install Ansible

`sudo apt update`

`sudo apt install ansible`

-Ansible version installed :

![Ansible-Version Running](https://user-images.githubusercontent.com/10111342/135882981-88338717-80a3-4679-a071-afd0d4eb17c2.png)

* Configure Jenkins build job to save the ansible-config-mgt repository content every time the repo changes
  
  * Create a freestyle project in Jenkins
  * configure github webhook and set the webhook to trigger a build in 
  Jenkins
  
  a. Created a freestyle project in Jenkins and added the repo url
  
  ![Add Reposto Jenkins 3](https://user-images.githubusercontent.com/10111342/135884544-5f38f43f-7d11-41fd-9640-38b5d0c1cae9.png)

  b. Configured Webhook on Git repo




  
  
  ![Webhook Config2](https://user-images.githubusercontent.com/10111342/135884145-70fcac84-302b-4f64-bdbe-14e87b550d16.png)

   * Configure a post-build action to save all ** files i.e archive the build
   *    Test setup by making changes to the readMe file in the ansible-config-mgt repo and commit the changes. This will trigger a build and archive the build files in the default location /var/lib/jenkins/jobs/ansible/builds/<build_number>/archive/

![Build Triggered location 7](https://user-images.githubusercontent.com/10111342/135885506-5e95e861-c0b2-499a-a4a7-1b6e4a0c1620.png)

Jenkins build 
![jekins build 1st test](https://user-images.githubusercontent.com/10111342/135887080-cd4d8400-3642-447c-b8db-de7b832057be.png)


**The architecture after setting up jenkins and Ansible on a bastion server is shown below:*

![Architecture Diagram4](https://user-images.githubusercontent.com/10111342/135886170-016dde5c-c09a-4400-8bc9-f446911aa1f1.png)

-Assign an Elastic Ip to the Jenkins server to avoid having to reconfigure github webhook everytime you restart the Jenkins server.


![Elastic IP 6](https://user-images.githubusercontent.com/10111342/135886402-80752265-1094-45d9-aa4b-dd674e47b519.png)
***

**Prepare using VS Code and begin Ansible development**
***
*  setup VSCode to connect to github 
![VSCode Connect to Repo 8](https://user-images.githubusercontent.com/10111342/135887793-14ecc053-ee99-4e20-b341-dfce02779eff.png)
* Create a new branch to be used to create new features named feature/prj11
![Feature Branch 9](https://user-images.githubusercontent.com/10111342/135888111-73f1decb-8cae-4f77-80db-8fdf23c00476.png)
**Note: `git checkout -b feature/prj-11` creates and checkout the branch*

* Checkout the `feature/prj11` branch and create new directories `inventory` and `playbooks` to be used to store the inventory and playbook files respectively.
![Create Playbook And Inventory folders 10](https://user-images.githubusercontent.com/10111342/135888408-0bdc9982-60ae-4646-b6e4-177aeb95be69.png)


* Create a new file named common.yml in the playbook directory.

* In the inventory directory create new files `dev.yml`, `staging.yml`, `uat.yml` and `prod.yml` for different environments (development, staging, testing and production environments)

## Setup Ansible Inventory
***
* The infastructure for the development environment is added to the `inventory/dev.yml` file.

![dev yml file 11](https://user-images.githubusercontent.com/10111342/135889621-cb5c29fd-b78b-4ef0-80ce-78fd6429099f.png)

* To allow ansible SSH into the target servers. 

1. Method: Copy the Private key file for the servers to the Ansible server and ensure the permissions on the file is set to chmod 400 key.pem . Also import the key into ssh-agent

 This method is not recommended

`

    eval `ssh-agent -s
    
    ssh-add Darey-Devops.pem

    chmod 400 Darey-Devops.pem

    ssh-add Darey-Devops.pem ``



2.Method: Initiate the eval `ssh-agent -s` `ssh-add -1 <path-to-private-kry> on the host server( in my case Ansible-Jenkins)

![Screenshot 2021-10-04 130822](https://user-images.githubusercontent.com/10111342/135894212-12fbe6df-9c30-4142-b2a6-0d688eeb219e.png)

## Create a common playbook
***
* Update the playbooks/common.yml file with the following code which installs wireshark utility on the web, nfs, db and lb servers


```
---
- name: update web, nfs and db servers
  hosts: webservers, nfs, db
  remote_user: ec2-user
  become: yes
  become_user: root
  tasks:
  - name: ensure wireshark is at the latest version
    yum:
      name: wireshark
      state: latest

- name: update LB server
  hosts: lb
  remote_user: ubuntu
  become: yes
  become_user: root
  tasks:
  - name: ensure wireshark is at the latest version
    apt:
      name: wireshark
      state: latest 
      

```
## Update Git with the latest code 

* Commit the changes to the feature branch and create a pull request (PR) for the branch to be merged into the master branch

Add and commit all files
![Git Add 12](https://user-images.githubusercontent.com/10111342/136029868-4037b653-d3ad-451c-a447-aa0243417f8e.png)



![branch merged to main 15](https://user-images.githubusercontent.com/10111342/136028698-8850a899-c1e3-4b9d-8d16-ce5b3a2cf448.png)

Files pushed to Git repo

![Files pushe to Github 13](https://user-images.githubusercontent.com/10111342/136029425-61a3ba72-3a8c-4ce3-baa3-db074cd74f86.png)

Pull Request
![Pull Request 14](https://user-images.githubusercontent.com/10111342/136028955-0cfecc87-5813-49ea-9e4b-a08c1472fc2c.png)

![Pull Request 15](https://user-images.githubusercontent.com/10111342/136029163-aa8c8ab2-06a7-4b2a-a446-c5f4d12ba182.png)

* Once merged into master branch, the github webhook triggers a build in Jenkins and archives the build files


![Jenkins build 15](https://user-images.githubusercontent.com/10111342/136030051-2dfbfa1d-9cee-4c10-9d34-aef6769310b2.png)

![Jenkins archive 16](https://user-images.githubusercontent.com/10111342/136030221-b1821faf-81a9-48aa-9ea0-49144503a130.png)


## Run first Ansible test
***

* Execute the common.yml playbook
```
ansible-playbook -i inventory/dev.yml playbooks/common.yml

```
![Ansible Playbook Ran 17](https://user-images.githubusercontent.com/10111342/136030814-1fe26679-b85b-4500-8431-ac160309ede4.png)

* Wireshark utility is now installed on the target servers

![WireShark on Web1 17](https://user-images.githubusercontent.com/10111342/136031064-704d20d8-d07d-460c-8fe5-7a13e2fa6f3b.png)

Updated Summary Architecture:
***


![image](https://user-images.githubusercontent.com/10111342/136032117-733a569d-f747-4afd-922f-248bade98b7c.png)

      
