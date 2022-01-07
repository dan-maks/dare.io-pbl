
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
    
## Step 2 - Installing MySQL

The below command installs the MySQL in the server 
  
$ sudo apt install mysql-server
  
![test 6](https://user-images.githubusercontent.com/96151001/148570545-49a4dc5c-14be-4d2b-991a-aa100f1f94c2.PNG)  
  
The below command runs a security script that removes some insecure default settings and lockdowm acccess to the database system
  
$ sudo mysql_secure_installation
  
![test 7](https://user-images.githubusercontent.com/96151001/148572631-fd2282a4-ff25-41a6-bcbb-a547b11a61f7.PNG)  

The command below test if I'm able to log in to the MySQL console
  
$ sudo mysql
  
![test 8](https://user-images.githubusercontent.com/96151001/148574390-0029fa56-5101-4704-a579-48b5c1f987ab.PNG)  
  
## Step 3 - Installing PHP
    
The command below installs php, php-mysql and libapache2-mod-php 
  
$ sudo apt install php libapache2-mod-php php-mysql  
  
Once the installation is finished, the following command confirms the PHP version
  
$ php -v  
  
![test 9](https://user-images.githubusercontent.com/96151001/148576767-3bc85d03-1a9d-4e09-ac00-29a7762b4914.PNG) 
  
LAMP stack has been installed completely and it is fully operational  
  
## Step 4 - Creating a Virtual Host for your Website using Apache 
  
Created a directory for projectlammp using 'mkdir' command
  
$ sudo mkdir /var/www/projectlamp  
  
Assigned ownership to the directory with this variable $USER which still referenced the current system user  
  
$ sudo chown -R $USER:$USER /var/www/projectlamp 
  
Created and opened a new configuration file in Apache’s sites-available directory using 'vi' command line editor
  
$ sudo vi /etc/apache2/sites-available/projectlamp.conf 
  
Pasted the below bare-bones configuration by hitting on i on the keyboard to enter the insert mode, and pasted the text
  
  <VirtualHost *:80>
    ServerName projectlamp
    ServerAlias www.projectlamp 
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/projectlamp
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
  </VirtualHost> 
  
  ### To save and close the file, simply follow the steps below:
  
  1. Hit the esc button on the keyboard
  
  2. Type :
  
  3. Type wq. w for write and q for quit
  
  4. Hit ENTER to save the file
  
Used the below command to show the new file we created in the sites-available directory 
  
$ sudo ls /etc/apache2/sites-available
  
![test 10](https://user-images.githubusercontent.com/96151001/148584020-d953078c-157e-4f59-8955-7879ea49eac5.PNG)  
  
Enabled the new virtualhost created with the below 
  
$ sudo a2ensite projectlamp
  
Disabled the default website that comes installed with Apache with the below command
  
$ sudo a2dissite 000-default
  
Ran the below command to make sure your configuration file doesn’t contain syntax errors
  
$ sudo apache2ctl configtest
  
![test 11](https://user-images.githubusercontent.com/96151001/148587089-08d7b032-b454-4b10-8914-34bee0a5be51.PNG)
  
Finally, used the below command to reload Apache so these changes take effect
  
$ sudo systemctl reload apache2  
 
Created an index.html file in the /var/www/projectlamp to test if the virtual host works as expected
  
$ sudo echo 'Hello LAMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectlamp/index.html 
  
The below screen shot is the output which shows the Virtual host is working perfecttly 
  
![HELLO LAMP](https://user-images.githubusercontent.com/96151001/148588756-196eb40e-0ba2-4f52-93f6-15fb05b4cdd6.PNG)  
  
## Step 5 - Enable PHP on the Website   
  
  
  
  
  
  
  
  
  

  
  
  
  
  
   
