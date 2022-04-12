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
      
![wd 5](https://user-images.githubusercontent.com/96151001/162749412-a4bc8ccf-5c18-45bb-ae1e-dfecab4a372      

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

check the contents of /var/log/, so as not do delete files impprtant for the running of our local machine 

     $ sudo ls -l /var/log/

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



    
