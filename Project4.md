## Web stack implementation using (MEAN Stack)

Project task is implementing a simple Book register web form using MEAN stack.

MEAN Stack is a combination of following components:
MongoDB{:target="_blank"} (Document database) – Stores and allows you to retrieve data.
Express (Back-end application framework) – Makes requests to Database for Reads and Writes.
Angular (Front-end application framework) – Handles Client and Server Requests.
Node.js (JavaScript runtime environment) – Accepts requests and displays results to the end user.

Next, I created an Ec2 instance 0f t2.nano family with Ubuntu server 20.04 LTS (HVM) image.
![Screenshot 1](https://user-images.githubusercontent.com/10111342/126701243-3d9b7425-f7a6-421b-b837-bf6f1a2da71f.png)

And connected to the instance using Mobaxterm as shown:
![Screenshot 2](https://user-images.githubusercontent.com/10111342/126701409-c3d04828-c005-487e-9564-59496c6b9f3c.png)


Step 1

We will install NodeJs - which is a JavaScript runtime built on Chrome’s V8 JavaScript engine. Node.js will be used to set up the Express routes and AngularJS controllers.
sudo apt update
sudo apt upgrade
sudo apt install -y nodejs

node --version to to confirm  
Output:

![Screenshot 3](https://user-images.githubusercontent.com/10111342/126701513-9097d2ef-d4df-4746-88a5-459fa1608a57.png)


Step 2

We will install MongoDB.MongoDB stores data in flexible, JSON-like documents. Fields in a database can vary from document to document and data structure can be changed over time. For our example application, we are adding book records to MongoDB that contain book name, isbn number, author, and number of pages.
Next, we will add the MongoDB key to our server  using the command:
 
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6

Output:


![Screenshot 4](https://user-images.githubusercontent.com/10111342/126701703-523edfb0-47d5-4e21-a6ea-0664a8ac8b46.png)

Next, we will add a repository to Mongodb
echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list

Output:

![Screenshot 5](https://user-images.githubusercontent.com/10111342/126701980-ecc03636-b4c2-41b2-9701-1176c18a88fc.png)

Install MongoDB
sudo apt install -y mongodb
sudo service mongodb start - Start the server
sudo systemctl status mongodb - To verify the service is up and running

Output:

![Screenshot 6](https://user-images.githubusercontent.com/10111342/126702143-9771e044-7b24-49f6-9270-daeedc4f3011.png)

Note: MongoDb is started and running

Install Node package manager.Npm is used to install dependencies for the application.
sudo apt install -y npm

Install ‘body-parser package is the package type we need to install and it will help process json files

sudo npm install body-parser

Output:

![Screenshot 7](https://user-images.githubusercontent.com/10111342/126702591-c040091e-608d-49d6-888f-360680cc0e3f.png)



Next step is creating a folder where we will be building our application

mkdir Books && cd Books

Output:

![Screenshot 8](https://user-images.githubusercontent.com/10111342/126702751-cc412448-66db-46a6-aa0c-e84912b3e833.png)




npm init - to initialize the directory

Output:

![Screenshot 9](https://user-images.githubusercontent.com/10111342/126702964-a881413c-86f1-4330-b58f-ee3f2bd44c3e.png)



Next step is to create a file server.js and paste code as shown below

Output:

![Screenshot 10](https://user-images.githubusercontent.com/10111342/126703073-1a497f66-3232-4280-9678-379e2b668d49.png)


Step 3:

Install Express and set up routes to the server. Express is a minimal and flexible Node.js web application framework that provides features for web and mobile applications. We will use Express in to pass book information to and from our MongoDB database.We also will use the Mongoose package which provides a straight-forward, schema-based solution to model your application data. We will use Mongoose to establish a schema for the database to store data from our book register.

sudo npm install express mongoose

Output:

![Screenshot 11](https://user-images.githubusercontent.com/10111342/126703192-31d75ced-f834-4990-b200-84ec0fe43685.png)


Next, in the Books directory, we created another directory called Apps. We then created a file routes.js in the Apps dir. We posted a code block inside of the file.

Output:

![Screenshot 12](https://user-images.githubusercontent.com/10111342/126703997-b5abd020-ea78-48c1-8e5a-d018293ed2a2.png)



Next, in the apps folder, we created a new folder models. We created a file name book.js and pasted the code block as shown below:

![Screenshot13](https://user-images.githubusercontent.com/10111342/126704004-6a49e9d3-4b74-4823-811a-3e4f0cc6981a.png)

Step 4 

Access the routes with AngularJS
AngularJS provides a web framework for creating dynamic views in your web applications. In this project we will use AngularJS to connect our web page with Express and perform actions on our book register.

Next step, we created a folder named ‘Public’ inside of the ‘Books’ directory. We the added a file named script.js and pasted the code block

Output:

![Screenshot 14](https://user-images.githubusercontent.com/10111342/126704007-5c04f006-0a83-4257-b735-7f7679fb49cf.png)
Note: I always use pwd to ascertain my directory path



We also created a file called index.html in the public folder and pasted the code block
![Screenshot 15](https://user-images.githubusercontent.com/10111342/126704010-2e1c3567-aff4-424e-8ec1-5082761c1ab0.png)


Next, we changed to the Books directory and started the server

node server.js

Output:

![Screenshot 16](https://user-images.githubusercontent.com/10111342/126704018-b1964bd9-1808-4553-8508-547416d7954d.png)


Hurray!! The server is now up and running, we can connect it via port 3300. You can launch a separate Putty or SSH console to test what curl command returns locally.

Launched a separate Putty or SSH console to test what curl command returns locally. I used GitBash and ssh - ssh -i "Darey-Devops.pem" ubuntu@ec2-44-193-77-137.compute-1.amazonaws.com
curl -s http://localhost:3300

Output:

![Screenshot 17](https://user-images.githubusercontent.com/10111342/126705172-18769cf0-b1ea-407a-aaee-6bc7473e1b98.png)

Open TCP port 3300 in my AWS Web Console for your EC2 Instance

Output:

![Screenshot 18](https://user-images.githubusercontent.com/10111342/126705178-939e12cd-3e15-4b94-9f85-4a0e66a6016a.png)

Now we can access our Book Register web application from the Internet with a browser using Public IP address 

Output:

![Screenshot 19](https://user-images.githubusercontent.com/10111342/126705182-fbbb767a-2c1e-4a30-81a1-ea0fb0a3635a.png)














