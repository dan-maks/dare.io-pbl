# AUX PROJECT 1: SHELL SCRIPTING

After learning about Linux for some time, and it is time to start getting a feel of how to automate some work using Shell Scripts.

In this project, we need to learn how to onboard 20 new Linux users onto a server. 

Create a shell script that reads a csv file that contains the first name of the users to be onboarded.

lauch an EC2 instance on AWS using ubuntu

Before connecting to the instance use scp command to copy over the script from the local  machine over to the instance

Using git bash, move into the downloads folder on the local machine where the pem key is located

     $ cd Downloads
     
Create onboard.sh file, copy and paste the content from the shell script file

     $ vi onboard.sh
     
![aux 2](https://user-images.githubusercontent.com/96151001/162594193-7b1f9a64-067c-4022-9275-bff03ff51af8.png)     


use scp command

     $ scp -i mysecuritygrp.pem onboard.sh ubuntu@184.72.124.20:Pcr;
     
![aux 7](https://user-images.githubusercontent.com/96151001/162588064-e3b96e70-e16e-4746-bc83-5bcca882ea6d.png)     
      
Connect to the instance to confirm if the script has been copied over to the remote server

![aux 10](https://user-images.githubusercontent.com/96151001/162588087-fe6367fd-6da7-4ccb-a28b-f2d5987db0be.png)
confirm using ls


     $ ls -l


![aux 12](https://user-images.githubusercontent.com/96151001/162588099-f27f86d3-2141-4404-a38d-8bf12972a5aa.png)

Create the project folder called Shell

     $ mkdir Shell
   
Move into the shell folder

     $ cd Shell
     
![aux 13](https://user-images.githubusercontent.com/96151001/162588108-86c243c1-0942-4935-8225-6db4cf3f74b7.png)     

Move back to the home directory

     $ cd ..
     
Using mv rename the Pcr to onboard.sh

     $ mv Pcr onboard.sh
     
Move the onboard.sh file to the shell directory

     $ mv onboard.sh /home/ubuntu/Shell/
     
![aux 15](https://user-images.githubusercontent.com/96151001/162591982-2c67f93b-b3d9-435a-aae2-65ac64cc8cb7.png)
          
cd into the Shell directory

     $ cd Shell/

Confirm if the onboard.sh is already in the Shell directory

     $ ls

![aux 11](https://user-images.githubusercontent.com/96151001/162588096-1d7ff3ce-0a97-4288-856d-78dcd48f5e1a.png)

Create files id_rsa, id_rsa.pub and names.csv, for the private key, public key and csv respectively

     $ touch id_rsa id_rsa.pub names.csv
     
![aux 16](https://user-images.githubusercontent.com/96151001/162592039-6500aa5b-6195-4778-82b0-5be2c780919a.png)     

Open the names.csv file

     $ vim names.csv
     
Insert some random names into it. (One name per line)   

![aux 19](https://user-images.githubusercontent.com/96151001/162592212-d1c4df64-0f28-4db0-bbd6-094c8756afa8.png)


Open the file using your favorite editor and paste in the public key

     $ vi id_rsa.pub     

Populate file with the public key below

     ssh-rsa 
     AAAAB3NzaC1yc2EAAAADAQABAAABgQCzKZyicHxIkklSrNlxsJyyTrcIdBIt84Z0cQb3R4k0jH53kxkaT5hP8tfWTe62LXi7vV86fY+SX7TBNM76XGCbw/6vrMGegm6J1x2i1AiLNwq5nqTjOGn0AIwku4IlCCLAB7tdfRyVuCarmBlwny3lzRyybIUAWXR/D6vpN09MsDILbKdhay+Q/p9OUBMSLPqXdY/QIh/Oe3rVv1lwY3AohNfq7V3tO88zKswfA5iiexNiSYX1myT0OrX8cBE771j9quoNZhQgaLI1mIMtAvnHQChrn9k2nUaO/BMBCQGol5XzGv1ado7hgoVPoluIUD+FGNo/pH4zcmDLICH6drXY/C9MESnkMUPLFxBXKO/OitApY71vRao9nAhAwpVMsy6FqiOb5uawhvhoHYIHTV/f4EtagVagRMP2PxYMYR6jykIV4MPJTkCm+lGhTyMlRu+qRQjdLn8AAtHf4aEV8dIkoGh088DI7eA/4o0wz4OV4upH5ewSFS+5IHmRECEW5Nc=     


![aux 17](https://user-images.githubusercontent.com/96151001/162592131-0f9ef55c-c1f1-4284-b0f8-41e96fdb501c.png)


Open the file using your favorite editor and paste in the private key.

     $ vi id_rsa
     
Populate file with the private key below


     -----BEGIN OPENSSH PRIVATE KEY-----
     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
     kmaSGj362OnCCNAAAACWRhcmVARGFyZQE=
     -----END OPENSSH PRIVATE KEY-----
     
     
     
![aux 18](https://user-images.githubusercontent.com/96151001/162592145-b6f648e8-a149-42df-aecd-20eceeb38c9b.png)
     
     
Open the onboard.sh file and upddate the id_rsa.pub path with /home/ubuntu/Shell    
     
     $ vi onboard.sh
     
![aux 20](https://user-images.githubusercontent.com/96151001/162593453-4c1f81ab-bcdb-4663-97f1-a6c313c54a33.png)


Update the developers group in the script

     $ sudo groupaddd developers
     
![aux 21](https://user-images.githubusercontent.com/96151001/162593462-ddafbe3c-9707-4a06-8f85-a69d0ea36611.png)     
     
Run the code below before running the script

     $ sudo chmod +x onboard.sh
     
![aux 22](https://user-images.githubusercontent.com/96151001/162593468-2e4f6162-d64e-4dc7-94a8-427ca7d04e0c.png)

Run the script without the sudo privilege

     $ ./onboard.sh

![aux 23](https://user-images.githubusercontent.com/96151001/162593472-c4d60a3b-466f-48db-aa2a-8512b9601f05.png)

Move to the root user

     $ sudo su

Run the script as a super user

     $ ./onboard.sh
     
![aux 24](https://user-images.githubusercontent.com/96151001/162593575-6f3d1b09-bd1f-44f8-9ec8-78fb75f90af5.png)


Checkout the users created

     $ ls -l /home/
     
![aux 25](https://user-images.githubusercontent.com/96151001/162593625-502d7ded-61b7-4ba8-8936-e745fb22369d.png)


run the below code to see the group id

     $ getent group developers
     
![aux 26](https://user-images.githubusercontent.com/96151001/162593656-55acaa50-df9a-4c92-b46e-ffbcab4c52e4.png)

run the below code

     $ cat /etc/passwd
          
![aux 27](https://user-images.githubusercontent.com/96151001/162593684-b579456b-b8d8-4b8d-bd47-c8e42b955997.png)


Run the below command to see a more filtered output

      $ cat /etc/passwd | awk -F':' '{ print $1}' | xargs n1 groups
      
![aux 28](https://user-images.githubusercontent.com/96151001/162593722-edfbf179-8ba9-4f16-966c-645548284d38.png)      
      
Open a new terminal, Test a few of the users randomly, and ensure that you are able to connect to the server using the private key and the public key. 

Open a new file auxproj.pem and pupulate with the the same private key given.

     $ vi auxproj.pem
     
Connect random user(Lampard) using the public ip address to the server   
     
     $ ssh -i auxproj.pem Lampard@44.204.9.26

![aux 29](https://user-images.githubusercontent.com/96151001/162593744-1737e9fb-4504-4079-998a-e8ffe75516d5.png)


Test if the random user (Lampard) has sudo privileges   

     $ sudo apt update     

![aux 30](https://user-images.githubusercontent.com/96151001/162593777-85afc92f-3400-4b1a-8ee6-e26615d2dcfc.png)

To cheeck the random user limit, run the below code 

     $ ls -la

Check the .ssh folder seen

     $ ls -la .ssh/

![aux 31](https://user-images.githubusercontent.com/96151001/162593815-ae07edda-aba1-4fbe-be14-fef154d9c513.png)


Output shows rhe authourized_keys is there just as specified in the script.

Exit and test some other random users too. 
     
![aux 32](https://user-images.githubusercontent.com/96151001/162593845-fa8e8084-be5f-4b1b-b23c-de3691c2fcad.png)     
     

![aux 33](https://user-images.githubusercontent.com/96151001/162593863-2443aeca-0bc5-42a8-a6e5-98f003f6d716.png)
     
     
     
     
          
