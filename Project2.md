
# Project 2

## Step 1 - Installing the Nginx Web Server

The below command updates the server's package index since it's a new session

    $ sudo apt update
  
The below command installs the Nginx

    $ sudo apt install nginx
    
![lemp 1](https://user-images.githubusercontent.com/96151001/148652970-5ed37191-e77a-4720-9546-bbf07b15466f.PNG)    
    
To verify that nginx was successfully installed and is running as a service in Ubuntu, the below command was run

    $ sudo systemctl status nginx
    
![lemp 2](https://user-images.githubusercontent.com/96151001/148652979-9e732a05-74b3-48a9-9f47-d87fec4a6e3f.PNG) 

    
Opened TCP port 80 in the EC2 instance, I allowed all traffic from all port range, which is still okay for this practice 

![lemp 3](https://user-images.githubusercontent.com/96151001/148653004-aff6649f-275e-4e1a-8631-7e97cdc15d39.PNG)

Checked if we can access nginx locally in our Ubuntu shell, run

    $ curl http://localhost:80
    
![lemp 4](https://user-images.githubusercontent.com/96151001/148653025-b69a4b75-273c-4c12-91de-bdfa79f2c37e.PNG)    
    
Test if the Nginx server can respond to requests from the Internet, opened a web browser and accessed the following url

    http://<Public-IP-Address>:80
    
![lemp 5](https://user-images.githubusercontent.com/96151001/148653033-133fad9e-d206-4209-9c9d-e750dbe0b023.PNG)    

output on browser shows web server is correctly installed and accessible through firewall

## Step 2 - Installing MySQL

The apt command acquires and installs mysql

    $ sudo apt install mysql-server
    
![lemp 6](https://user-images.githubusercontent.com/96151001/148653048-366f3a6c-dd1a-4d0b-84c6-096cf3701f2c.PNG)    
    
The below command runs a security script that comes pre-installed with MySQL which remove some insecure default settings and lock down access to your database system

    $ sudo mysql_secure_installation
    
![lemp 7](https://user-images.githubusercontent.com/96151001/148653061-da6c2e64-5861-47f1-8467-43483aba2a35.PNG)    
    
After the completion of the validate password plugin, the code below verifies that we can log in to the MySQL console   
    
    $ sudo mysql 
    
![lemp 9](https://user-images.githubusercontent.com/96151001/148653656-a2a15d75-a821-4676-923a-5e12c2551bd0.PNG)    
    
To exit the MySQL console, type

    mysql> exit
    
## Step 3 - Installing PHP

The below command installs php-fpm and php-mysql at once

    $ sudo apt install php-fpm php-mysql
    
![lemp 10](https://user-images.githubusercontent.com/96151001/148653734-b06821f8-21e6-4904-b83f-e79cf5f47bd3.PNG)    
    
Type Y and press ENTER to confirm installation. PHP components has been installed successfully  

## Step 4 - Configuring Nginx to Use PHP Processor
 
Created a root web directory for projectLEMP using the below command
 
    $ sudo mkdir /var/www/projectLEMP
    
![lemp 11](https://user-images.githubusercontent.com/96151001/148653740-6be60d88-13bd-40ea-8d3c-1814b8187fe0.PNG)    
    
Assigned ownership to the directory with the $USER environment variable, which still referenced the current system user
 
    $ sudo chown -R $USER:$USER /var/www/projectLEMP
    
![lemp 12](https://user-images.githubusercontent.com/96151001/148653745-9c419b5e-3b0d-4aec-88a2-ba2e70e41ca8.PNG)    
    
Opened a new configuration file in Nginx’s sites-available directory using 'nano' command-line editor  

    $ sudo nano /etc/nginx/sites-available/projectLEMP
 
Pasted the below bare-bones configuration in the new blank file created

   
    #/etc/nginx/sites-available/projectLEMP

    server {
        listen 80;
        server_name projectLEMP www.projectLEMP;
        root /var/www/projectLEMP;

        index index.html index.htm index.php;

        location / {
            try_files $uri $uri/ =404;
        }

        location ~ \.php$ {
            include snippets/fastcgi-php.conf;
            fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
         }

        location ~ /\.ht {
            deny all;
        }

    }
    
    
![lemp 13](https://user-images.githubusercontent.com/96151001/148653751-7d54df53-f535-4bf2-aceb-5bd6e16f9104.PNG)

After editing, save and close the file on nano by typing CTRL+X and then y and ENTER to confirm 
 
The command below activate the configuration by linking to the config file from Nginx’s sites-enabled directory
 
    $ sudo ln -s /etc/nginx/sites-available/projectLEMP /etc/nginx/sites-enabled/
    
![lemp 14](https://user-images.githubusercontent.com/96151001/148653752-11197714-7ed2-4495-a33a-4610d0f82dfb.PNG)    
    
 Test the configuration for syntax errors by running
 
    $ sudo nginx -t
    
![lemp 15](https://user-images.githubusercontent.com/96151001/148654125-9ac2d10f-51a9-4047-9d44-c8618891c15b.PNG)    
    
Disabled the default Nginx host that is currently configured to listen on port 80 with the below command

    $ sudo unlink /etc/nginx/sites-enabled/default
    
Use the below command to reload Nginx to apply the changes  

    $ sudo systemctl reload nginx
    
![lemp 16](https://user-images.githubusercontent.com/96151001/148653771-782bf37a-e7ce-4bec-8dd2-4fbe59737232.PNG)    
    
Created an index.html file in the /var/www/projectLEMP to test if the new server block works as expected

    $ sudo echo 'Hello LEMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-    b       data/public-ipv4) > /var/www/projectLEMP/index.html
    
![lemp 17](https://user-images.githubusercontent.com/96151001/148653791-0592bd89-fff6-412a-a6a1-1657cca9d33c.PNG)    
    
On the browser, Open the website URL using IP address

    http://<Public-IP-Address>:80
    
The below screen shot shows that LEMP stack is fully configured

![lemp 18](https://user-images.githubusercontent.com/96151001/148653795-27feee1d-38c6-4163-a531-f656d01920eb.PNG)

## Step 5 - Testing PHP with Nginx

Used below command to open a new file called info.php within the document root in nano text editor

    $ sudo nano /var/www/projectLEMP/info.php

Pasted the following lines into the new file

    <?php
    phpinfo();
    
![lemp 19](https://user-images.githubusercontent.com/96151001/148654403-8396465c-9143-41f9-86fb-a4efe0e9e746.PNG)    
 
Accessed this page on web browser by visiting the domain name or public IP address set up in the Nginx configuration file, followed by /info.php: 

    http://`server_domain_or_IP`/info.php
    
![lemp 21](https://user-images.githubusercontent.com/96151001/148654410-d8c62192-21a1-42ce-9615-5000314d410b.PNG)    
    
Remove the file created as it contains sensitive information about the PHP environment and Ubuntu server using the following command  

    $ sudo rm /var/www/your_domain/info.php
    
![lemp 22](https://user-images.githubusercontent.com/96151001/148654414-d8c0bf35-e4fd-4852-b265-3c6d17c30702.PNG)    

## Step 6 - Retrieving data from MySQL database with PHP

Connected to the MySQL console using the root account

    $ sudo mysql
    
![lemp 23](https://user-images.githubusercontent.com/96151001/148654419-8a41da5c-1b2e-41f8-bb6a-6d9693191d44.PNG)    
    
Created a new database, by running the below command from MySQL console

    mysql> CREATE DATABASE `example_database`;
    
![lemp 24](https://user-images.githubusercontent.com/96151001/148654441-3cdf3bce-0309-413f-8b42-d91e3ab7dfc8.PNG)    
    
The below command creates a new user named example_user, using mysql_native_password as default authentication method. I defined this user’s password as password  

    mysql>  CREATE USER 'example_user'@'%' IDENTIFIED WITH mysql_native_password BY 'password';
    
![lemp 25](https://user-images.githubusercontent.com/96151001/148654444-08e2a8e1-9ccd-47d8-b84d-e134bfb4ac5b.PNG)    
    
Gave this user permission over the example_database database by running this command
 
    mysql> GRANT ALL ON example_database.* TO 'example_user'@'%';
    
![lemp 26](https://user-images.githubusercontent.com/96151001/148654460-81e40af7-bb91-4137-9d6c-62fa8196f1a2.PNG)    
    
Exit the MySQL shell with:

    mysql> exit 
    
Test if the new user has the proper permissions by logging in to the MySQL console again, this time using the custom user credentials  

    mysql -u example_user -p
    
![lemp 27](https://user-images.githubusercontent.com/96151001/148654842-e9cebf51-40b6-4a95-90f8-309655b767c6.PNG)    
 
Confirmed access to the example_database database, After logging in to the MySQL console
 
    mysql> SHOW DATABASES;
    
![lemp 28](https://user-images.githubusercontent.com/96151001/148654847-081e5bbe-e38e-4a46-aa41-d86367e8dea3.PNG)    
    
created a test table named todo_list. From the MySQL console, run the below statement

    mysql> CREATE TABLE example_database.todo_list (item_id INT AUTO_INCREMENT, content VARCHAR(255), PRIMARY KEY(item_id));
    
![lemp 29](https://user-images.githubusercontent.com/96151001/148654851-4da68fb1-5d68-4683-a5b5-1208b5b60470.PNG)    
    
Repeated the next command a once, using different VALUES

    mysql> INSERT INTO example_database.todo_list (content) VALUES ("My first important item");
    
![lemp 30](https://user-images.githubusercontent.com/96151001/148654854-09fc21e5-921a-411e-9ca2-6282caf3bfe0.PNG)    
    
Confirmed the data was successfully saved to the table by running the below command

    mysql>  SELECT * FROM example_database.todo_list;

After confirmation of valid data in the test table, exit the MySQL console

    mysql> exit    
    
Created a new PHP file in the custom web root directory using nano editor by running the below ommand

    $ nano /var/www/projectLEMP/todo_list.php
    
Copied the below content into the todo_list.php script


    <?php
    $user = "example_user";
    $password = "password";
    $database = "example_database";
    $table = "todo_list";

    try {
      $db = new PDO("mysql:host=localhost;dbname=$database", $user, $password);
      echo "<h2>TODO</h2><ol>";
      foreach($db->query("SELECT content FROM $table") as $row) {
        echo "<li>" . $row['content'] . "</li>";
      }
      echo "</ol>";
    } catch (PDOException $e) {
      print "Error!: " . $e->getMessage() . "<br/>";
      die();
    }
    
 
![lemp 31](https://user-images.githubusercontent.com/96151001/148654858-91b59b29-690e-4763-b03e-c55f56e9e7f1.PNG)

Save and close the file after editing.   

Accessed this page in the web browser by visiting the domain name or public IP address configured for the website, followed by /todo_list.php

    http://<Public_domain_or_IP>/todo_list.php
    
![lemp 32](https://user-images.githubusercontent.com/96151001/148654865-b5ca02d3-3cba-46fd-b071-4229a83a1b66.PNG)    
