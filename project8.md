# Project 8 - Load Balancer Solution With Apache

Each URL contains a domain name part, which is translated (resolved) to IP address of a target server that will serve requests when open a website in the Internet.

Translation (resolution) of domain names is perormed by DNS servers, the most commonly used one has a public IP address 8.8.8.8 and belongs to Google.

You can try to query it with nslookup command:

      $ nslookup 8.8.8.8

![p8 c1](https://user-images.githubusercontent.com/96151001/164704330-d816db82-4fc9-4a00-88b4-67f050886d6d.png)


In our set up in Project-7 we had 3 Web Servers and each of them had its own public IP address and public DNS name. A client has to access them by using different URLs, which is not a nice user experience to remember addresses/names of even 3 server, let alone millions of Google servers.      

In order to hide all this complexity and to have a single point of access with a single public IP address/name, a Load Balancer can be used. A Load Balancer (LB) distributes clients’ requests among underlying Web Servers and makes sure that the load is distributed in an optimal way.

Let us take a look at the updated solution architecture with an LB added on top of Web Servers (for simplicity let us assume it is a software L7 Application LB, for example – Apache, NGINX or HAProxy)

In this project we will enhance our Tooling Website solution by adding a Load Balancer to distribute traffic between Web Servers and allow users to access our website using a single URL


## Task

Deploy and configure an Apache Load Balancer for Tooling Website solution on a separate Ubuntu EC2 intance. Make sure that users can be served by Web servers through the Load Balancer.

To simplify, let us implement this solution with 2 Web Servers, the approach will be the same for 3 and more Web Servers.

### Prerequisites

Make sure that you have following servers installed and configured within Project-7:

1. Two RHEL8 Web Servers
2. One MySQL DB Server (based on Ubuntu 20.04)
3. One RHEL8 NFS server


## Configure Apache As A Load Balancer

Create an Ubuntu Server 20.04 EC2 instance and name it Project-8-apache-lb.

Open TCP port 80 on Project-8-apache-lb by creating an Inbound Rule in Security Group.

Install Apache Load Balancer on Project-8-apache-lb server and configure it to point traffic coming to LB to both Web Servers:

Install apache2

      $ sudo apt update
      $ sudo apt install apache2 -y
      
![p8 1](https://user-images.githubusercontent.com/96151001/164714081-9405d484-6e53-416b-a2f0-aa6b264eda0f.png)

      $ sudo apt-get install libxml2-dev

![p8 2](https://user-images.githubusercontent.com/96151001/164713923-15e57b2c-67e8-4d8e-b985-7713930e694e.png)

Enable following modules:
      
      $ sudo a2enmod rewrite
      $ sudo a2enmod proxy
      $ sudo a2enmod proxy_balancer
      $ sudo a2enmod proxy_http
      $ sudo a2enmod headers
      $ sudo a2enmod lbmethod_bytraffic


![p8 3](https://user-images.githubusercontent.com/96151001/164713792-7df8e1bc-4ae9-45ba-ac06-a0503fdb29d4.png)

Restart apache2 service

      $ sudo systemctl restart apache2

Make sure apache2 is up and running

      $ sudo systemctl status apache2

![p8 4](https://user-images.githubusercontent.com/96151001/164713675-de503a0f-ce23-4fc7-8f11-a3f5192a4eef.png)

Configure load balancing      
      
      $ sudo vi /etc/apache2/sites-available/000-default.conf

Add this configuration into this section <VirtualHost *:80>  </VirtualHost>

       $ <Proxy "balancer://mycluster">
                        BalancerMember http://<WebServer1-Private-IP-Address>:80 loadfactor=5 timeout=1
                        BalancerMember http://<WebServer2-Private-IP-Address>:80 loadfactor=5 timeout=1
                        ProxySet lbmethod=bytraffic
                        # ProxySet lbmethod=byrequests
                 </Proxy>

                 ProxyPreserveHost On
                 ProxyPass / balancer://mycluster/
                 ProxyPassReverse / balancer://mycluster/


![p8 5](https://user-images.githubusercontent.com/96151001/164713439-d3088b08-b10c-4694-834e-5c5b941d9cbb.png)

Restart apache server

      $ sudo systemctl restart apache2

![p8 c2](https://user-images.githubusercontent.com/96151001/164711405-e43b9e75-ca3d-4083-a6e1-0938ba3b28b8.png)

Verify that our configuration works – try to access your LB’s public IP address or Public DNS name from your browser:

      http://<Load-Balancer-Public-IP-Address-or-Public-DNS-Name>/index.php

![p8 c3](https://user-images.githubusercontent.com/96151001/164711445-7b851415-d330-49ae-8944-ab1d8241ea4e.png)

Note: If in the Project-7 you mounted /var/log/httpd/ from your Web Servers to the NFS server – unmount them and make sure that each Web Server has its own log directory.

Open two ssh/Putty consoles for both Web Servers and run following command:

      $ sudo tail -f /var/log/httpd/access_log

![p8 c4](https://user-images.githubusercontent.com/96151001/164711481-35e903e6-ee3b-4207-b737-76c660eaa8c7.png)

![p8 c5](https://user-images.githubusercontent.com/96151001/164711512-775517ab-cda2-4619-a04d-242db216070c.png)

Try to refresh your browser page http://<Load-Balancer-Public-IP-Address-or-Public-DNS-Name>/index.php several times and make sure that both servers receive HTTP GET requests from your LB – new records must appear in each server’s log file.
      
The number of requests to each server will be approximately the same since we set loadfactor to the same value for both servers – it means that traffic will be disctributed evenly between them.
      
      
![p8 c6](https://user-images.githubusercontent.com/96151001/164711555-cbd1326c-23b5-40f1-a59b-7f8c743fb49c.png)
      
![p8 c7](https://user-images.githubusercontent.com/96151001/164711579-5763e5ce-b185-46de-9fa2-bbd54d29d971.png)     
      
      
If you have configured everything correctly – your users will not even notice that their requests are served by more than one server.
      

### Optional Step – Configure Local DNS Names Resolution
      
Sometimes it is tedious to remember and switch between IP addresses, especially if you have a lot of servers under your management.
      
What we can do, is to configure local domain name resolution. The easiest way is to use /etc/hosts file, although this approach is not very scalable, but it is very easy to configure and shows the concept well
      
So let us configure IP address to domain name mapping for our LB.
      
Open this file on your LB server
      
      $ sudo vi /etc/hosts

Add 2 records into this file with Local IP address and arbitrary name for both of your Web Servers      
      
      <WebServer1-Private-IP-Address> Web1
      <WebServer2-Private-IP-Address> Web2

![p8 c8](https://user-images.githubusercontent.com/96151001/164711599-f6c0daf6-ce2f-44c1-97fb-0a3bba32b404.png)
            
Now you can update your LB config file with those names instead of IP addresses.

      BalancerMember http://Web1:80 loadfactor=5 timeout=1
      BalancerMember http://Web2:80 loadfactor=5 timeout=1   
            
![p8 c9](https://user-images.githubusercontent.com/96151001/164711628-a1f45855-4b88-43dc-b784-0923561f8f10.png)
            
You can try to curl your Web Servers from LB locally
            
      $ curl http://Web1
            
![p8 c10](https://user-images.githubusercontent.com/96151001/164711662-b3be6700-85e1-4b1c-9b7b-473373309a08.png)
            
Or,
            
      $ curl http://Web2      
            
![p8 c11](https://user-images.githubusercontent.com/96151001/164711699-3c970d46-3edf-4bef-9663-0856d879e64c.png)
            
Remember, this is only internal configuration and it is also local to your LB server, these names will neither be ‘resolvable’ from other servers internally nor from the Internet.            
      
      
      

