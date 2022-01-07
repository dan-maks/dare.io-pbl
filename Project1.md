
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

![test 5](https://user-images.githubusercontent.com/96151001/148568320-f63d1354-4234-43ce-a4b9-7336e8a9339c.PNG)

Test how Apache HTTP server can respond to requests from the Internet, open a web browser and try access the url

http://<Public-IP-Address>:80
  
![apache browser test](https://user-images.githubusercontent.com/96151001/148567708-6ef6e9c9-1920-4aa9-bdf5-27812ff1a79f.PNG)
  
The below command runs a security script that removes some insecure default settings and lockdowm acccess to the database system
  
$ sudo mysql_secure_installation  

## Step 2 - Installing MySQL

The below command installs the MySQL in the server 
  
$ sudo apt install mysql-server
  
![test 6](https://user-images.githubusercontent.com/96151001/148570545-49a4dc5c-14be-4d2b-991a-aa100f1f94c2.PNG)  

