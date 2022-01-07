
# Project 1 

## Step 1 - Installing Apache and Updating the Firewall

The below command update a list of packages in package manager

$ sudo apt update

The below command runs apache2 package installation 

$ sudo apt install apache2

This code verifies that apache2 is running as a service in my server 

$ sudo systemctl status apache2

![test 4](https://user-images.githubusercontent.com/96151001/148561654-24566200-3aa5-4103-a08e-e04db33671b9.PNG)

Opened TCP port 80 in the EC2 instance which is the default port that web browsers use to access web pages on the internet

![test 2](https://user-images.githubusercontent.com/96151001/148563143-11ac8464-3f22-4af6-b42f-abb6ccd280aa.PNG)

Output to check if we can access apache2 locally it the ubuntu shell, run the command

$ curl http://localhost:80





