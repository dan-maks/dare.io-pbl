# PROJECT 5 - CLIENT-SERVER ARCHITECTURE WITH MYSQL

# TASK – Implement a Client Server Architecture using MySQL Database Management System (DBMS).

To demonstrate a basic client-server using MySQL Relational Database Management System (RDBMS), follow the below instructions:

Create and configure two Linux-based virtual servers (EC2 instances in AWS)

Server A name - `mysql server`
Server B name - `mysql client`

On mysql server Linux Server install MySQL Server software.

Interesting fact: MySQL is an open-source relational database management system. Its name is a combination of "My", the name of co-founder Michael Widenius’s daughter, and "SQL", the abbreviation for Structured Query Language.

To install MySQL Server software, we first upadate and upgrade our linux-based virtual server, which is ubuntu in this case

    $ sudo apt update
    $ sudo apt upgrade
    
Use the below code to install mysql

    $ sudo apt install mysql-server
    
![p5 2](https://user-images.githubusercontent.com/96151001/161808102-e8b55acd-a954-4ed7-a42e-3933e276e6b1.png)

![p5 3](https://user-images.githubusercontent.com/96151001/161808194-fd696dcb-57c8-4fd3-abac-293bbf170583.png)
    
Enable the server

    $ sudo systemctl enable mysql

![p5 4](https://user-images.githubusercontent.com/96151001/161808271-1ea8438f-96b7-4874-acf5-ade8a3eee30b.png)

On mysql client Linux Server install MySQL Client software by first updating ubuntu

    $ sudo apt update -y
    
![p5 5](https://user-images.githubusercontent.com/96151001/161808342-ba88d4fd-4839-4361-9153-eaf8e077cc80.png)    
    
Then we follow be running the installation code

    $ sudo apt install mysql-client -y
    
![p5 6](https://user-images.githubusercontent.com/96151001/161808395-a40185f3-71c3-4261-9a25-a0b310024a21.png) 


By default, both of our EC2 virtual servers are located in the same local virtual network, so they can communicate to each other using local IP addresses. Use mysql server's local IP address to connect from mysql client. MySQL server uses TCP port 3306 by default, so we will have to open it by creating a new entry in ‘Inbound rules’ in ‘mysql server’ Security Groups. For extra security, do not allow all IP addresses to reach your ‘mysql server’ – allow access only to the specific local IP address of your ‘mysql client’.


We have to create our database on the installed mysql server, by first running the security script

    $ sudo mysql_secure_installation

We choose a new simple password (password) to set up mysql and add all the neccessary inputs requested

![p5 7](https://user-images.githubusercontent.com/96151001/161808438-e3882118-779a-4842-bef2-c5a7afd0aeea.png)

Start mysql

    $ sudo mysql
    
![p5 8](https://user-images.githubusercontent.com/96151001/161808536-0aa07b35-93de-47c4-b97e-c225058b8c60.png)

Create user

    mysql> CREATE USER 'remote_user'@'%' IDENTIFIED WITH mysql_native_password BY 'password';
    
Create database

    mysql> CREATE DATABASE test_db;
    
Grant all privileges on the database

    mysql> GRANT ALL ON test_db.* TO 'remote_user'@'%' WITH GRANT OPTION;
    
Flush privileges

    mysql> FLUSH PRIVILEGES;
   
Exit

    mysql> exit;

![p5 9](https://user-images.githubusercontent.com/96151001/161808607-90ed5e67-d392-4cb3-807f-a722749330ab.png)

We might need to configure MySQL server to allow connections from remote hosts.

    $ sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf
    
Replace ‘127.0.0.1’ which is the bind address with ‘0.0.0.0' by using dw and :wq to save it. 

![p5 10](https://user-images.githubusercontent.com/96151001/161808661-fd64a805-2ae5-4b2b-b520-c171bec442c5.png)

Restart the service

    $ sudo systemctl restart mysql

From mysql client Linux Server connect remotely to mysql server Database Engine without using SSH. You must use the mysql utility to perform this action.

    $ sudo mysql -u remote_user -h 'private ipv4' -p
    
Ensure to edit the codee above by adding the host ip address

Enter the password

![p5 11](https://user-images.githubusercontent.com/96151001/161811972-f297c087-fdda-454d-8b9a-a43dd9003f83.png)

Check that you have successfully connected to a remote MySQL server and can perform SQL queries:

    mysql> Show databases;
    
The output below shows we have successfully completed this project – we have deloyed a fully functional MySQL Client-Server set up.    
    
![p5 12](https://user-images.githubusercontent.com/96151001/161812007-3312f799-cac0-4df4-ad6e-2b4e38a88c9d.png)
    
     
     
    





