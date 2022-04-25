# Project 7: Devops Tooling Website Solution

## Step 1 – Prepare NFS Server

Spin up a new EC2 instance with RHEL Linux 8 Operating System.

Based on our LVM experience from Project 6, Configure LVM on the NFS Server and connect.

3 EBS volumes (10G each) was created - xvdf, xvdg and xvdh. Run the code below to list all the block attached to the nfs server instance.

      $ lsblk

![p7 1](https://user-images.githubusercontent.com/96151001/165110337-c1488124-8b4c-4544-8863-b8354acac53b.png)

Create a single partition on each of the 3 disks using gdisk utility. Starting from the first disk:

      $ sudo gdisk /dev/xvdf

![p7 2](https://user-images.githubusercontent.com/96151001/165110398-a03fb769-bef6-45db-ad16-b3d7c6568d1c.png)

followed by the second disk,

      $ sudo gdisk /dev/xvdg
      
![p7 3](https://user-images.githubusercontent.com/96151001/165111955-e10fbcc9-369d-4b36-bb8c-fd78e6715127.png)

And lastly the third disk,

      $ sudo gdisk /dev/xvdh
      
![p7 4](https://user-images.githubusercontent.com/96151001/165111994-c2d664ad-221a-403c-a314-3c066ea1e25a.png)

Confirm all the partitions created on the disks

      $ lsblk
      
![p7 5](https://user-images.githubusercontent.com/96151001/165112028-2992b6f5-3877-4c58-bb1a-41cc28fd2440.png)     

Install the lvm package

      $ sudo yum install lvm2 -y
       
![p7 6](https://user-images.githubusercontent.com/96151001/165112066-44a3879c-8a31-4bac-badd-71be78cf2f9b.png)

Check for the available partitions for LVM

      $ sudo lvmdiskscan
      
Use pvcreate utility to mark each of 3 disks as physical volumes (PVs) to be used by LVM

      $ sudo pvcreate /dev/xvdf1 /dev/xvdg1 /dev/xvdh1

Verify that the Physical volume has been created successfully

      $ sudo pvs

Use vgcreate utility to add all 3 PVs to a volume group (VG). Name the VG webdata-vg

      $ sudo vgcreate webdata-vg /dev/xvdf1 /dev/xvdg1 /dev/xvdh1

Verify that VG has been created successfully

      $ sudo vgs

![p7 8](https://user-images.githubusercontent.com/96151001/165112116-202b1ed4-2d40-4fb3-94b6-bc88285fc91b.png)

Use lvcreate utility to create 3 logical volumes, lv-apps, lv-logs and lv-opt.

For lv-apps, run:

      $ sudo lvcreate -n lv-apps -L 9G webdata-vg

for lv-logs, run:

      $ sudo lvcreate -n lv-logs -L 9G webdata-vg
      
for lv-opt, run:

      $ sudo lvcreate -n lv-opt -L 9G webdata-vg

Verify that Logical Volume has been created successfully.

      $ sudo lvs

![p7 9 edit](https://user-images.githubusercontent.com/96151001/165112156-2e439369-aca7-4746-80c8-3b069728398d.png)

Use mkfs.xfs to format the logical volumes with xfs filesystem.

For lv-apps, run:

      $ sudo mkfs -t xfs /dev/webdata-vg/lv-apps
     
for lv-logs, run:   

      $ sudo mkfs -t xfs /dev/webdata-vg/lv-logs

for lv-opt, run:

      $ sudo mkfs -t xfs /dev/webdata-vg/lv-opt
     
![p7 11](https://user-images.githubusercontent.com/96151001/165112236-21943a10-199d-4f97-b1a4-c55d3955215f.png)

Make directories /mnt/apps, /mnt/logs, /mnt/opt.

      $ sudo mkdir mnt/apps
     
      $ sudo mkdir mnt/logs
     
      $ sudo mkdir mnt/opt

Create mount points on /mnt directory for the logical volumes as follow:

Mount lv-apps on /mnt/apps – To be used by webservers
     
      $ sudo mount /dev/webdata-vg/lv-apps /mnt/apps

Mount lv-logs on /mnt/logs – To be used by webserver logs

      $ sudo mount /dev/webdata-vg/lv-logs /mnt/logs

Mount lv-opt on /mnt/opt – To be used by Jenkins server in Project 8

      $ sudo mount /dev/webdata-vg/lv-opt /mnt/opt

![p7 12](https://user-images.githubusercontent.com/96151001/165112425-595a5f79-c56c-42cc-bec2-7e9edf022235.png)


Install NFS server, run the below code first

      $ sudo yum -y update

![p7 13](https://user-images.githubusercontent.com/96151001/165112473-15b77687-798a-4215-bf39-bff9744de19b.png)

      $ sudo yum install nfs-utils -y

      $ sudo systemctl start nfs-server.service

      $ sudo systemctl enable nfs-server.service

      $ sudo systemctl status nfs-server.service

![p7 17](https://user-images.githubusercontent.com/96151001/165112742-39bdfdf7-c08e-4b37-9583-ddb42b626acd.png)

Export the mounts for webservers’ subnet cidr to connect as clients. For simplicity, you will install your all three Web Servers inside the same subnet, but in production set up you would probably want to separate each tier inside its own subnet for higher level of security.

To check your subnet cidr – open your EC2 details in AWS web console and locate ‘Networking’ tab and open a Subnet link:

Make sure we set up permission that will allow our Web servers to read, write and execute files on NFS:


      $ sudo chown -R nobody: /mnt/apps
      
      $ sudo chown -R nobody: /mnt/logs
      
      $ sudo chown -R nobody: /mnt/opt

      $ sudo chmod -R 777 /mnt/apps

      $ sudo chmod -R 777 /mnt/logs

      $ sudo chmod -R 777 /mnt/opt

      $ sudo systemctl restart nfs-server.service
      

![p7 18](https://user-images.githubusercontent.com/96151001/165141488-541822c9-8875-40f3-b87a-dd8b403a75e8.png)

Configure access to NFS for clients within the same subnet 

      $ sudo vi /etc/exports
      
Populate the file

      /mnt/apps <Subnet-CIDR>(rw,sync,no_all_squash,no_root_squash)
      /mnt/logs <Subnet-CIDR>(rw,sync,no_all_squash,no_root_squash)
      /mnt/opt <Subnet-CIDR>(rw,sync,no_all_squash,no_root_squash)
      
![p7 19](https://user-images.githubusercontent.com/96151001/165141535-66c78a4e-349b-4126-93a9-305016d72f64.png)

Save and exit the file. Run the code below afterwards

      $ sudo exportfs -arv
       
Check which port is used by NFS and open it using Security Groups (add new Inbound Rule)
     
      $ rpcinfo -p | grep nfs       

Important note: In order for NFS server to be accessible from your client, you must also open following ports: TCP 111, UDP 111, UDP 2049


## Step 2 — Configure the Database Server 

Connect to the server using ssh

Update the ubuntu instance

      $ sudo apt update

![p7 14](https://user-images.githubusercontent.com/96151001/165112542-e4680f4b-bd61-460e-9e7a-c3e8b6ea6b9a.png)

Install MySQL server.
      
      $ sudo apt install mysql-server -y
      
Create a database and name it tooling.

      mysql> create database tooling;

Create a database user and name it webaccess.

      mysql> CREATE USER 'webaccess'@'<subnet cidr>' IDENTIFIED BY 'password';

Grant permission to webaccess user on tooling database to do anything only from the webservers subnet cidr

      mysql> GRANT ALL PRIVILEGES ON tooling.* TO 'webaccess'@'<subnet cidr>' WITH GRANT OPTION;

Flush privileges.
      
      mysql> FLUSH PRIVILEGES;

show databases.

      mysql> SHOW DATABASES;

![P7 15](https://user-images.githubusercontent.com/96151001/165112581-e1cd60eb-0060-4a33-80aa-b904f6f9d38f.png)

Select user.

      mysql> select user, host from mysql.user;
      
![P7 16](https://user-images.githubusercontent.com/96151001/165112652-bcaa1379-c56a-40d1-a64c-3c3a4e7b882b.png)      

Exit

     mysql> exit;
     

## Step 3 — Prepare the Web Servers

We need to make sure that our Web Servers can serve the same content from shared storage solutions, in our case – NFS Server and MySQL database.

You already know that one DB can be accessed for reads and writes by multiple clients.

For storing shared files that our Web Servers will use – we will utilize NFS and mount previously created Logical Volume lv-apps to the folder where Apache stores files to be served to the users (/var/www).

This approach will make our Web Servers stateless, which means we will be able to add new ones or remove them whenever we need, and the integrity of the data (in the database and on NFS) will be preserved.

During the next steps we will do following:

- Configure NFS client (this step must be done on all three servers)
- Deploy a Tooling application to our Web Servers into a shared NFS folder
- Configure the Web Servers to work with a single MySQL database

Launch a new EC2 instance with RHEL 8 Operating System

Install NFS client.

      $ sudo yum install nfs-utils nfs4-acl-tools -y

Mount /var/www/ and target the NFS server’s export for apps.

      $ sudo mkdir /var/www

      $ sudo mount -t nfs -o rw,nosuid <NFS-Server-Private-IP-Address>:/mnt/apps /var/www  

![p7 20](https://user-images.githubusercontent.com/96151001/165141558-b6bb7fba-6033-4f9a-81ed-a9f009b27877.png)

Verify that NFS was mounted successfully by running df -h.
      
      $ df -h
      
Test if the the webservr and nfs server can communicate. 

      $ sudo touch /var/www/test.md

![p7 21](https://user-images.githubusercontent.com/96151001/165141583-8c78f18e-c499-4ede-8ac7-7a17a377479e.png)

After creating test.md, check it out on the nfs server instance.

      $ ls /mnt/apps
      
![p7 22](https://user-images.githubusercontent.com/96151001/165141602-9100a1ff-d34b-4d52-9188-8950a0028a0d.png)      
 
Make sure that the changes will persist on Web Server after reboot:

      $ sudo vi /etc/fstab
      
add following line:    

      $ <NFS-Server-Private-IP-Address>:/mnt/apps /var/www nfs defaults 0 0

![p7 23](https://user-images.githubusercontent.com/96151001/165141634-bba42655-bf49-40a5-91c3-c5597295f7d6.png)

Install Apache.

      $ sudo yum install httpd -y
      
Verify that Apache files and directories are available on the Web Server in /var/www and also on the NFS server in /mnt/apps/      
      
      $ ls /var/www
      
![p7 24](https://user-images.githubusercontent.com/96151001/165141663-5c88f029-82fe-46e0-be50-3d7584228b2a.png)

![p7 25](https://user-images.githubusercontent.com/96151001/165141683-4ad729d7-9306-4c1c-90db-b90212b77c85.png) 

If you see the same files – it means NFS is mounted correctly.

Locate the log folder for Apache on the Web Server and mount it to NFS server’s export for logs

      $ ls /var/log
      
      $ sudo mount -t nfs -o rw,nosuid <NFS-Server-Private-IP-Address>:/mnt/logs /var/log/httpd 
      
![p7 26](https://user-images.githubusercontent.com/96151001/165141713-ea87bab0-5ef7-4df4-aecc-a17136e9d385.png)      

Make sure the mount point will persist after reboot

      $ sudo vi /etc/fstab
      
Add the following line:

      $ <NFS-Server-Private-IP-Address>:/mnt/logs /var/log/httpd nfs defaults 0 0

Fork the tooling source code from Darey.io Github Account to your Github account

      $ git init
      
      $ git clone https://github.com/darey-io/tooling.git
      
Check the content of the repository

       $ ls
       
move to the file
       
       $ cd tooling/
       
check tooling/ content

       $ ls
         
Deploy the tooling website’s code to the Webserver. Ensure that the html folder from the repository is deployed to /var/www/html  

       $ ls /var/www
       
       $ sudo cp -R html/. /var/www/html
       
Confirm the process.

       $ ls /var/www/html
       
       $ ls html
       
![p7 27](https://user-images.githubusercontent.com/96151001/165141735-b408a474-6bfb-4787-9db8-bcee672ae84b.png)      

Note 1: Do not forget to open TCP port 80 on the Web Server.

Note 2: If you encounter 403 Error – check permissions to your /var/www/html folder and also disable SELinux 

       $ sudo setenforce 0  

To make this change permanent – open following config file

       $ sudo vi /etc/sysconfig/selinux 

![p7 28](https://user-images.githubusercontent.com/96151001/165141757-7fd24eed-e28a-4885-a9ba-98212f7b9a5c.png)

Set SELINUX=disabled then restrt httpd.

       $ sudo systemctl start httpd
       
       $ sudo systemctl status httpd
       
![p7 30](https://user-images.githubusercontent.com/96151001/165141847-92160083-aebb-4a16-b3ac-8ea7f241c8c4.png)
       
Using the webserver public IP address, load the apache test page

![p7 29](https://user-images.githubusercontent.com/96151001/165141801-39e520d1-4421-4bc5-bb04-d3793d81cb0a.png)
       
Update the website configuration to connnect to the database (in /var/www/html/functions.php file).    
       
       $ sudo vi /var/www/html/functions.php
       
![p7 31](https://user-images.githubusercontent.com/96151001/165141895-0b7feb23-2510-46e9-aa33-8cad7619e08c.png)       

Install mysql client

       $ sudo yum install mysql -y
       
![p7 32](https://user-images.githubusercontent.com/96151001/165141909-1da63214-acff-4a16-b443-567db8daa983.png)       
       
Open the mysql configuration file and update the bind address to 0.0.0.0

       $ sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf
       
![p7 33](https://user-images.githubusercontent.com/96151001/165141945-5589e20d-a058-414d-ba96-c50c26de2e67.png)

Retsart mysql and check the status

       $ sudo systemctl restart mysql
       
       $ sudo systemctl status mysql
       
Apply tooling-db.sql script to your database.

       $ mysql -h <database-private-ip> -u <db-username> -p tooling < tooling-db.sql
       
check out tooling-db.sql file 

       $ sudo vi tooling-db.sql
       
on the database server, start mysql

       S sudo mysql
       
       mysql> SHOW DATABASES;
       
       mysql> use tooling;
       
       mysql> show tables;
       
       mysql> select * from users;
       
       mysql> exit;
       
Back on the webserver, check out the apache test page

       $ sudo vi /etc/httpd/conf.d/welcome.conf /etc/httpd/conf.d/welcome.backup
       
Rename the default page

       $ sudo mv /etc/httpd/conf.d/welcome.conf /etc/httpd/conf.d/welcome.backup
   
Retsart httpd and check the status

       $ sudo systemctl restart httpd
       
       $ sudo systemctl status httpd
       
Reload the test page on browser       
       
Install PHP dependencies so as to display the html format seen  

Install Remi's repository      

      $ sudo dnf install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm

      $ sudo dnf install dnf-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm

      $ sudo dnf module reset php

      $ sudo dnf module enable php:remi-7.4

      $ sudo dnf install php php-opcache php-gd php-curl php-mysqlnd

      $ sudo systemctl start php-fpm

      $ sudo systemctl enable php-fpm

      $ sudo setsebool -P httpd_execmem 1
      
      $ sudo systemctl restart httpd
      

Reload the html page after completion.

Enter username and password (admin) to login to the tooling website
   
      

    

























 
 





















       
       
       
       
       
       
       
       
       
       
       
