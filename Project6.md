# Project 6 - WEB SOLUTION WITH WORDPRESS

In this project you will be tasked to prepare storage infrastructure on two Linux servers and implement a basic web solution using WordPress. WordPress is a free and open-source content management system written in PHP and paired with MySQL or MariaDB as its backend Relational Database Management System (RDBMS).

Project 6 consists of two parts:

1. Configure storage subsystem for Web and Database servers based on Linux OS. The focus of this part is to give you practical experience of working with disks, partitions and volumes in Linux.
2. Install WordPress and connect it to a remote MySQL database server. This part of the project will solidify your skills of deploying Web and DB tiers of Web solution.

Our 3-Tier Setup
1. A Laptop or PC to serve as a client
2. An EC2 Linux Server as a web server (This is where you will install WordPress)
3. An EC2 Linux server as a database (DB) server

We shall use RedHat OS for this project on our AWS console

# Step 1 — Prepare a Web Server

Launch an EC2 instance that will serve as "Web Server". Create 3 volumes in the same AZ as your Web Server EC2, each of 10 GiB.

Attach all three volumes one by one to your Web Server EC2 instance

Using Gitbash terminal, change directory to Downloads where my pem key is stored

       $ cd Downloads
     
Connect the web server instance using the SSH code 
 
![wd 1](https://user-images.githubusercontent.com/96151001/162747746-1e4ee68c-38cb-4945-977c-78e79c91f16e.png)

Use lsblk command to inspect what block devices are attached to the server. Notice names of your newly created devices. All devices in Linux reside in /dev/ directory. 
 
       $ lsblk
     
![wd 2](https://user-images.githubusercontent.com/96151001/162749262-56102693-2229-4c3d-a82b-6a4883cb0a37.png)

Inspect it with ls /dev/ and make sure you see all 3 newly created block devices there – their names will likely be xvdf, xvdh, xvdg.
     
       $ ls /dev/
      
![wd 3](https://user-images.githubusercontent.com/96151001/162749325-4dcc04b5-28c3-4951-bd76-5bce8d1afbce.png)
      
Use df -h command to see all mounts and free space on your server

      $ df -h
      
![md 4](https://user-images.githubusercontent.com/96151001/162840012-8bbfe233-6729-4cd2-b3b7-67ae752599b9.png)
      
Use gdisk utility to create a single partition on each of the 3 disks

      $ sudo gdisk /dev/xvdf
            
![wd 5](https://user-images.githubusercontent.com/96151001/162948180-49db298b-cc39-48af-bba2-3eb7fc504b4d.png)

Repeat the same process for the second disk

      $ sudo gdisk /dev/xvdg
      
![wd 7](https://user-images.githubusercontent.com/96151001/162841293-fe8b9c99-9731-4a70-8f8c-82b9d1c6ff4a.png)      
      
And lastly third disk

      $ sudo gdisk /dev/xvdh
      
![md 11](https://user-images.githubusercontent.com/96151001/162842170-73d56bc6-3701-4de6-a0d1-8a2928e66d3c.png)

Use lsblk utility to view the newly configured partition on each of the 3 disks.

Install lvm2 package

      $ sudo yum install lvm2 -y
           
![md 12](https://user-images.githubusercontent.com/96151001/162842938-eb289378-e9b4-4e8e-9288-403b1562f115.png)
      
Confirm if lvm has been installed

      $ which lvm
     
 ![md 14](https://user-images.githubusercontent.com/96151001/162843066-065a95c7-b5b7-431d-9e7d-94e9b57d75a2.png)

Run sudo lvmdiskscan command to check for available partitions.

      $ sudo lvmdiskscan

![md 15](https://user-images.githubusercontent.com/96151001/162843523-925b7a32-893a-4bc0-94da-d7692e5c0994.png)

Use pvcreate utility to mark each of 3 disks as physical volumes (PVs) to be used by LVM

      $ sudo pvcreate /dev/xvdf1 /dev/xvdg1 /dev/xvdh1

![md 16](https://user-images.githubusercontent.com/96151001/162843849-5ceac3e5-25a8-4d3b-be52-403874161d0b.png)

Verify that the Physical volume has been created successfully

      $ sudo pvs
      
![md 17](https://user-images.githubusercontent.com/96151001/162844768-2b25f0b4-b793-4fc8-8825-63fe1e823a01.png)

Use vgcreate utility to add all 3 PVs to a volume group (VG). Name the VG webdata-vg

      $ sudo vgcreate webdata-vg /dev/xvdf1 /dev/xvdg1 /dev/xvdh1

![md 18](https://user-images.githubusercontent.com/96151001/162844993-4e334cf8-a7ca-4cdb-a99e-0f9e5468518f.png)

Verify that VG has been created successfully

      $ sudo vgs
      
![md 19](https://user-images.githubusercontent.com/96151001/162845539-704a8c3c-8e99-41be-9414-940e80f9b9fd.png)

Use lvcreate utility to create 2 logical volumes. apps-lv (Use half of the PV size), and logs-lv Use the remaining space of the PV size. NOTE: apps-lv will be used to store data for the Website while, logs-lv will be used to store data for logs.

For apps-lv, run

      $ sudo lvcreate -n apps-lv -L 14G webdata-vg
      
for logs-lv, run

      $ sudo lvcreate -n logs-lv -L 14G webdata-vg

![md 20](https://user-images.githubusercontent.com/96151001/162845569-3f4ed82c-adda-4843-a9df-02d05048a206.png)

Verify that your Logical Volume has been created successfully

      $ sudo lvs

![md 21](https://user-images.githubusercontent.com/96151001/162845786-4bb024cf-739c-49c5-83ee-842abe46bdc5.png)

Verify the entire setup

      $ sudo lsblk
      
![md 23](https://user-images.githubusercontent.com/96151001/162845916-8021ded0-9f20-44a4-9eff-c9e0a8a887f1.png)

Use mkfs.ext4 to format the logical volumes with ext4 filesystem

for apps-lv, run

      $ sudo mkfs -t ext4 /dev/webdata-vg/apps-lv

for logs-lv.run

      $ sudo mkfs -t ext4 /dev/webdata-vg/logs-lv
      
![md 25](https://user-images.githubusercontent.com/96151001/162846934-cd64e049-93d7-41f9-b988-ef296e810578.png)

Cheeck if the var directory is existing

      $ sudo ls -l /var

![md 26](https://user-images.githubusercontent.com/96151001/162847234-6ed5dbca-ab59-408a-9b46-61a3af13fbde.png)

Create /var/www/html directory to store website files

      $ sudo mkdir -p /var/www/html

Create /home/recovery/logs to store backup of log data

      $ sudo mkdir -p /home/recovery/logs

Mount /var/www/html on apps-lv logical volume

      $ sudo mount /dev/webdata-vg/apps-lv /var/www/html/
      
![md 28](https://user-images.githubusercontent.com/96151001/162848699-57ee079a-ccf9-4777-9dc9-3f04649b52c3.png)

check the contents of /var/log/, so as not do delete files important for the running of our local machine 

      $ sudo ls -l /var/log/

![wd 29](https://user-images.githubusercontent.com/96151001/162979178-5574a945-d7fe-4fdd-a609-3799f29beec4.png)

Use rsync utility to backup all the files in the log directory /var/log into /home/recovery/logs (This is required before mounting the file system)

      $ sudo rsync -av /var/log /home/recovery/logs/

![md 29](https://user-images.githubusercontent.com/96151001/162848824-184a8a71-c76c-488d-8599-8759f385d4df.png)

check if /home/recovery/logs/ is existing

      $ sudo ls -l /home/recovery/logs/log
     
![md 30](https://user-images.githubusercontent.com/96151001/162848856-de3d7110-053b-4eda-8949-d75385cf9c03.png)

![md 31](https://user-images.githubusercontent.com/96151001/162851932-03c5c329-e7c7-4e08-9f2f-33eb4ed34e90.png)

Mount /var/log on logs-lv logical volume

      $ sudo mount /dev/webdata-vg/logs-lv /var/log
      
Restore log files back into /var/log directory

      $ sudo rsync -av /home/recovery/logs/log /var/log

![md 32](https://user-images.githubusercontent.com/96151001/162853180-1923e1e3-8641-4746-a60d-a8ab4d2c2457.png)

check the /var/log

      $ sudo ls -l /var/log
     
![md 33](https://user-images.githubusercontent.com/96151001/162850749-fc0ac180-eeae-48be-a2e6-690dc83fd65f.png)
     
check out the all that has been mounted

      $ df -h

Update /etc/fstab file so that the mount configuration will persist after restart of the server.

The UUID of the device will be used to update the /etc/fstab file

      $ sudo blkid
    
![md 34](https://user-images.githubusercontent.com/96151001/162853113-111129aa-4ba4-4f21-a605-ff503211ea63.png)

Open the /etc/fstab file

      $ sudo vi /etc/fstab

Update /etc/fstab in this format using your own UUID and rememeber to remove the leading and ending quotes.

![md 38](https://user-images.githubusercontent.com/96151001/162950282-a21ab7d1-b18d-408a-b112-1e1ec7eeb032.png)
    
Test the configuration

      $ sudo mount -a

![md 35](https://user-images.githubusercontent.com/96151001/162949305-07e13087-3ad6-4318-842e-54792004c1e3.png)

Reload the daemon

      $ sudo systemctl daemon-reload

Verify your setup, output must look like this:

      $ df -h
      
![md 36](https://user-images.githubusercontent.com/96151001/162949344-f6a39d8d-556c-4c64-9342-097f3023e2f8.png)


# Step 2 — Prepare the Database Server

Launch a second RedHat EC2 instance that will have a role – ‘database server’

Repeat the same steps as for the Web Server, but instead of apps-lv create db-lv and mount it to /db directory instead of /var/www/html/.

Connect the web server instance using the SSH code

![md 40](https://user-images.githubusercontent.com/96151001/162953256-c4690b84-215d-45cc-af79-c3cddcbdca38.png)

Use lsblk command to inspect what block devices are attached to the server    
   
      $ lsblk  

Use gdisk utility to create a single partition on each of the 3 disks, run the below code for the first disk.

      $ sudo gdisk /dev/xvdf

![md 39](https://user-images.githubusercontent.com/96151001/162953301-45a740d1-9d56-440a-98f8-724721044e04.png)

for second disk, run:

      $ sudo gdisk /dev/xvdg

![md 41](https://user-images.githubusercontent.com/96151001/162957398-e59265f3-4587-46cf-9159-98a4ac93873f.png)

for third disk, run:

      $ sudo gdisk /dev/xvdh
      
![md 42](https://user-images.githubusercontent.com/96151001/162957442-4a3bc2a1-8de6-43b1-ba24-179c9c14f9d8.png)

Use lsblk utility to view the newly configured partition on each of the 3 disks.

      $ lsblk
      
![md 43](https://user-images.githubusercontent.com/96151001/162957973-a233e4b4-4fa0-456a-b7b2-ca5edce498d9.png)

Install lvm2 package

      $ sudo yum install lvm2 -y
      
![md 44](https://user-images.githubusercontent.com/96151001/162958204-3df79183-c07f-4045-9826-51a97aced266.png)

Use pvcreate utility to mark each of 3 disks as physical volumes (PVs) to be used by LVM

      $ sudo pvcreate /dev/xvdf1 /dev/xvdg1 /dev/xvdh1

![md 45](https://user-images.githubusercontent.com/96151001/162958242-c4382dc4-2ccf-4438-98fc-e6994213233d.png)

Use vgcreate utility to add all 3 PVs to a volume group (VG). Name it vg-database

      $ sudo vgcreate vg-database /dev/xvdf1 /dev/xvdg1 /dev/xvdh1   

Verify that VG has been created successfully

      $ sudo vgs
      
![md 46](https://user-images.githubusercontent.com/96151001/162958436-9b92e2e2-8b55-4111-8cc0-862c444f81b0.png)

Use lvcreate utility to create 1 logical volume. db-lv

      $ sudo lvcreate -n db-lv -L 20G vg-database

![md 47](https://user-images.githubusercontent.com/96151001/162959019-19b8fb7b-70b0-484f-befd-a18a417e8b14.png)

Create mount points

      $ sudo mkdir /db
      
Add the filesystem  

      $ sudo mkfs.ext4 /dev/vg-database/db-lv
      
![md 48](https://user-images.githubusercontent.com/96151001/162959116-7062293e-a778-49d3-ab67-4fa48a5fc5a2.png)

confirm that /db is empty

      $ sudo ls -l /db
     
![md 49](https://user-images.githubusercontent.com/96151001/162962241-91de1f4a-3efe-4c1c-ab36-22d0660e50f3.png)

Mount /db on db-lv logical volume

      $ sudo mount /dev/vg-database/db-lv /db

confirm the configuration

      $ df -h
      
![md 50](https://user-images.githubusercontent.com/96151001/162963809-6f696a9f-d27c-45b2-9726-a25d4ec4751a.png)

Update /etc/fstab file so that the mount configuration will persist after restart of the server.

The UUID of the device will be used to update the /etc/fstab file

      $ sudo blkid

![md 51](https://user-images.githubusercontent.com/96151001/162964125-ef607ea9-614f-460f-88d1-40e96e635ab6.png)

Open the /etc/fstab file

      $ sudo vi /etc/fstab

Update /etc/fstab in this format using your own UUID and rememeber to remove the leading and ending quotes.

![md 52](https://user-images.githubusercontent.com/96151001/162964170-f4bc0038-01fb-4df7-ae78-c2c8acd77642.png)

Test the configuration

      $ sudo mount -a

![md 53](https://user-images.githubusercontent.com/96151001/162964204-6ac36c1f-5970-436d-b40f-260a5cee912d.png)

Reload the daemon

      $ sudo systemctl daemon-reload

Verify your setup, output must look like this:

      $ df -h
      
![md 54](https://user-images.githubusercontent.com/96151001/162964229-b8dbff33-1bef-4566-bbe6-e8a25cca7e4d.png)


# Step 3 — Install WordPress on your Web Server EC2

Update the repository (both instances)

      $ sudo yum -y update

![md 55](https://user-images.githubusercontent.com/96151001/162964269-b819b85e-ecd1-4e77-b780-565f2886af00.png)

starting with the installation of PHP 8.0 0n RHEL 8, follow the documentation below,

Right off the bat, you need to enable the EPEL repository on your system. EPEL, short for Extra Packages for Enterprise Linux, is an effort from the Fedora team that provides a set of additional packages that are not present by default on RHEL & CentOS.

      $ sudo dnf install -y https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
     
![md 56](https://user-images.githubusercontent.com/96151001/162964303-f97d463f-b9e3-4a20-8bba-0d06e8a2d2cf.png)

Remi repository is a third-party repository that provides a wide range of PHP versions for RedHat Enterprise Linux. To install the Remi repository, run the command:

      $ sudo dnf install -y https://rpms.remirepo.net/enterprise/remi-release-8.rpm
     
![md 57](https://user-images.githubusercontent.com/96151001/162964324-1858325d-bc49-4b0f-902c-9ae680b33536.png)     

Once the installation is complete, proceed and list the available php module streams as shown:

      $ sudo dnf module list php
      
Right at the bottom, be sure to notice the remi-8.0 php module.

![md 58](https://user-images.githubusercontent.com/96151001/162964352-29188852-971f-41f7-b66d-ce460bbab7fd.png)

Check the version of the PHP

      $ php -v

Start PHP-FPM

      $ sudo systemctl start php-fpm
      
Enable PHP-FPM

      $ sudo systemctl enable php-fpm

![md 59](https://user-images.githubusercontent.com/96151001/162964379-40c639f6-9778-45a0-a449-97ff4abfbef3.png)

Check the status

      $ sudo systemctl status php-fpm
      
![md 60](https://user-images.githubusercontent.com/96151001/162964408-a86ddcc4-ba4a-4dc0-9f0c-c13f25c8d82a.png)      
      
To instruct SElinux to allow Apache to execute the PHP codevia PHP-FPM run;

      $ setsebool -P httpd_execmem 1
      
check if httpd is installed

      $ which httpd
      
check the status, if its running

      $  sudo systemctl status httpd
      
Bring httpd to live

      $ sudo systemctl start httpd

check the status again

      $ sudo systemctl status httpd

![md 61](https://user-images.githubusercontent.com/96151001/162964456-1734e6d2-6e2b-42d3-ab2c-8c085945aa1c.png)

Check the content of /var/www/html to confirm httpd

      $ sudo ls -l /var/www/html
     
![md 62](https://user-images.githubusercontent.com/96151001/162964492-9181bc05-bf7e-47fa-86fb-9fb8594ea7ef.png)     

Test on the web brower to check if apache is already working, using the web server public IP

![md 63](https://user-images.githubusercontent.com/96151001/162964518-50197777-db1a-4847-bc90-c2ce09216207.png)

Download wordpress and copy wordpress to var/www/html, following the below process

      $ mkdir wordpress
      
change directory

      $ cd   wordpress

Install wget

      $ sudo yum install wget -y

![wd 60](https://user-images.githubusercontent.com/96151001/162983465-112506e7-3b2a-44bc-a009-fb664ad4833f.png)

Download wordpress

      $ sudo wget http://wordpress.org/latest.tar.gz

![wd 61](https://user-images.githubusercontent.com/96151001/162983498-63f62a0d-a92a-4310-b44a-aa21f065767b.png)

Check out the downloaded file
    
      $ ls -l
    
Extarct the downloaded file 

      $ sudo tar xzvf latest.tar.gz
      
![wd 62](https://user-images.githubusercontent.com/96151001/162983537-9db545e2-4898-4ce7-848d-9973844c19c9.png)

Confirm the extraction

      $ ls -l 
      
Move back to the directory wordpress/ that was extracted

      $ cd wordpress

Check the content of the directory now

      $ ls -l
     
![wd 63](https://user-images.githubusercontent.com/96151001/162983575-4b09c281-2b61-47bc-880d-64526580cf27.png)    

Copy the contents of the wp-config-sample.php over to wp-config.php, ctreated by the code below

      $ sudo cp -R wp-config-sample.php wp-config.php     

Check the content of the directory aagain

      $ ls -l

![wd 64](https://user-images.githubusercontent.com/96151001/162983624-1c8a2034-b1a3-4326-94c4-2b77856bda82.png)

Check the current directory

      $ pwd
      
Move bacck to the first wordpress directory created

      $ cd ..

Check the content of the present directory

      $ ls
       
Copy the contents of wordpress/ over to  /var/www/html/ ctreated by the code below
       
      $ sudo cp -R wordpress/ /var/www/html/
       
Move to /var/www/html/ directory

      $ cd /var/www/html/
       
![wd 65](https://user-images.githubusercontent.com/96151001/162983746-bb0419a3-2fea-4c41-96b2-341ebbd81641.png)

Move to /var directory

      $ cd ../..

Move back to the home directory

      $ cd
      
Check comtent of the directory

      $ ls
      
Change to the wordpress directory

      $ cd wprdpress

Check out contents of the wordpress directory

      $ ls -l wordpress

![wd 66](https://user-images.githubusercontent.com/96151001/162983789-f73b2f38-e73a-41dc-868e-aba1189918e7.png)

Copy the contents of the present directory to /var/www/html

      $ sudo cp -R wordpress/. /var/www/html
      
Check the contents of /var/www/html after

      $ sudo ls -l /var/www/html
      
![wd 67](https://user-images.githubusercontent.com/96151001/162983834-75bbee24-2750-464f-9ebb-1b77dc64d4a5.png)

Move to /var/www/html directory and check the contents using ls

      $ cd /var/www/html

![wd 68](https://user-images.githubusercontent.com/96151001/162983869-a677dc5a-43f5-4b7f-9fbc-d25daed02291.png)

Install mysql on the web server because it will be running as our client

      $ sudo yum install mysql server
      
![wd 69](https://user-images.githubusercontent.com/96151001/162983915-4621a4cc-7367-4fba-a760-45967e788b59.png)      

start mysqld

      $ sudo systemctl start mysqld

enable it

      $ sudo systemctl enable mysqld

check the status

      $ sudo systemctl status mysqld
       
 
# Step 4 — Install MySQL on your DB Server EC2 
       
On the database server, install mysql

      $ sudo yum install mysql server -y
       
![db sermysql ](https://user-images.githubusercontent.com/96151001/163004288-a2dfd3ec-4d42-4c8b-ac6f-e3508a2a077a.png)
       
Start mysqld    

      $ sudo systemctl start mysqld

Enable it

      $ sudo systemctl enable mysqld

Check the status

      $ sudo systemctl status mysqld
        
![db sermysql2 ](https://user-images.githubusercontent.com/96151001/163004333-3f9d3225-c99b-4bd0-8fb7-8cca9c3634a3.png)


# Step 5 — Configure DB to work with WordPress

      $ sudo mysql_secure_installation
        
select new password 'password' and enter all the options required

![wd 70](https://user-images.githubusercontent.com/96151001/162983947-aa4c86ea-9ee7-464d-b9db-fc13a3729c67.png)
        
Start mysql database configuration:

      $ sudo mysql -u root -P
       
Enter the pasword 'password'

Create database,

      mysql> create database wordpress;

show databases

      myysql> show databases;
     
![wd 71](https://user-images.githubusercontent.com/96151001/162983979-35be420d-635b-4df1-9232-942eb0cbf3d8.png)

Create user,

      mysql> CREATE USER 'wordpress'@'%' IDENTIFIED WITH mysql_native_password BY 'password';
      
Grant all privileges,      
   
      mysql> GRANT ALL PRIVILEGES ON *.* TO 'wordpress'@'%' WITH GRANT OPTION;
      
Flush privileges,      
      
      mysql> FLUSH PRIVILEGES;
      
Select user

      mysql> select user, host from mysql.user;
      
      
![wd 72](https://user-images.githubusercontent.com/96151001/162984014-161b4fec-9751-4b3b-8812-aee7a9e06445.png)      
      
      mysql> exit;
            
bind address configuration

      $ sudo vi /etc/my.cnf

Set the [mysqld] bind-address to 0.0.0.0

![wd 73](https://user-images.githubusercontent.com/96151001/162984137-f7f014a3-70c6-4c89-ad37-86d9f77838f9.png)

Restart mysqld:

      $ sudo systemctl restart mysqld

on the web server, update the wp-config.php

      $ sudo vi wp-config.php
      
![wd 74](https://user-images.githubusercontent.com/96151001/162984212-ce003f05-5936-48fe-b87c-d8cea3e82cca.png)
      
Restart mysqld on the web server

      $ sudo systemctl restart mysqld


      
      



      
      
      
      
      
      
      
      
      
      
      
      
      
