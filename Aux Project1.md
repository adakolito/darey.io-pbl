## Aux Project 1 (Shell Scripting)

In this project, we will  need to onboard 20 new Linux users onto a server. Create a shell script that reads a csv file that contains the first name of the users to be onboarded.

Create the project folder called Shell

/home/ubuntu/projects/Shell

Create a csv file name - names.csv

Touch names.csv

Output:


![2](https://user-images.githubusercontent.com/10111342/127885740-bc284471-7448-4cb0-9351-3b11d2c9925d.png)

Next step:Open the names.csv file and insert some random names
 
Testing with 2 users first:

![step2](https://user-images.githubusercontent.com/10111342/127887175-65923936-9956-4ea1-96c1-c60052f61904.png)

Next, I created the onboarding_users script file and gave it the appropriate permissions:

Touch onboarding_users

Chmod +x onboarding_users 

![script file created](https://user-images.githubusercontent.com/10111342/127887772-222189e2-c741-4048-8f2d-3131fc242292.png)



Below is the script content:

#!/bin/bash

# Automating the creation of new users on linux server

#setting of csv_file

CSV_FILE=names.csv

GROUP_NAME=developers

#setting name of ssh directory for skel

SSH_SKEL=/etc/skel/.ssh

# variable for public key
AUTH=authorized_keys


# password variable
PASSWORD=password

# check if group exists if not create group

if [ $(getent group developers) ];
then
    echo group already exists
else
    sudo groupadd $GROUP_NAME
    echo group successfuly created
 fi

 # add ssh folder to skel directory
 
 if [ -d "$SSH_SKEL" ];
 then
     echo "$SSH_SKEL already exists"
 else
     sudo mkdir -p $SSH_SKEL
     sudo bash -c "cat $AUTH >> $SSH_SKEL/authorized_keys"
fi


 # create each user on the server
 
 while IFS=  read USERNAME
    do
    
         # check if theusername already exists
         
         if [ $(getent passwd $USERNAME) ];
         
         then
             echo $USERNAME already exists
        else
            sudo useradd -m -G $GROUP_NAME -s /bin/bash $USERNAME
            
            sudo echo -e "$PASSWORD\n$PASSWORD" | sudo passwd "${USERNAME}"
            
            sudo passwd -x 5 ${USERNAME}
            
            sudo chmod 700 /home/$USERNAME/.ssh
            
            sudo chmod 644 /home/$USERNAME/.ssh/authorized_keys
            
            echo "$USERNAME successfully created"
        fi
    done < $CSV_FILE

exit 0

![script](https://user-images.githubusercontent.com/10111342/127888927-3b42e0f9-77ea-46eb-8306-ce590004ea78.png)

Next Step- I ran the the script and it was successful. However, I forgot to take a screen shot. But below is the re-confirmation when I ran the script again:

![TodayUserScript](https://user-images.githubusercontent.com/10111342/127893050-811c26c5-5761-4126-9dd7-338a5cd7e341.png)

Checked all users are in the home directory:

![user in Home Directory 8](https://user-images.githubusercontent.com/10111342/127893312-24123faa-fb56-42b4-8d3e-45ce6026e1b0.png)

Checked users are in the developers group:

![All users added to Group 7](https://user-images.githubusercontent.com/10111342/127894016-941d9e4c-3ff8-4b05-bf2d-f5d22d6c0703.png)


Tested with user MARYBETH and hurray!! She is able to connect to the server.

Output:

![User MaryBteth logged in](https://user-images.githubusercontent.com/10111342/127894759-97680a4d-2857-4562-a5b4-793782105d09.png)


