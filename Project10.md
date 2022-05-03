# Project 10 - Load Balancer Solution With Nginx and SSL/TLS

## Task

This project consists of two parts:
1. Configure Nginx as a Load Balancer
2. Register a new domain name and configure secured connection using SSL/TLS certificates

## Step 1 - Configure Nginx as a Load Balancer

Create an EC2 VM based on Ubuntu Server 20.04 LTS and name it Nginx LB (do not forget to open TCP port 80 for HTTP connections, also open TCP port 443 – this port is used for secured HTTPS connections)

Update /etc/hosts file for local DNS with Web Servers’ names (e.g. Web1 and Web2) and their local IP addresses

![etc](https://user-images.githubusercontent.com/96151001/166488923-fbfc89a8-4a4d-4d59-8ba0-63cffd8eeaff.png)

Install and configure Nginx as a load balancer to point traffic to the resolvable DNS names of the webservers

Update the instance and Install Nginx

     $ sudo apt update
     $ sudo apt install nginx

![p10 5](https://user-images.githubusercontent.com/96151001/166480808-5c38cfec-5b71-411f-89eb-bb323b0904e0.png)

![p10 6](https://user-images.githubusercontent.com/96151001/166480835-f62797a9-f0da-4b8a-962c-28f0ea237d98.png)

Configure Nginx LB using Web Servers’ names defined in /etc/hosts

Open the default nginx configuration file

     $ sudo vi /etc/nginx/nginx.conf

insert following configuration into http section


     upstream myproject {
        server Web1 weight=5;
        server Web2 weight=5;
      }

    server {
        listen 80;
        server_name www.domain.com;
        location / {
          proxy_pass http://myproject;
        }
      }

    #comment out this line
    #       include /etc/nginx/sites-enabled/*;


![p10 8](https://user-images.githubusercontent.com/96151001/166480898-09e5ec95-fcc1-4998-8f40-8e85bf033ce4.png)

Restart Nginx and make sure the service is up and running

     $ sudo systemctl restart nginx
     $ sudo systemctl status nginx

![p10 10](https://user-images.githubusercontent.com/96151001/166480979-edffaead-ffd0-4257-9087-7200faa83a24.png)

## Step 2 -  Register a new domain name and configure secured connection using SSL/TLS certificates

Let us make necessary configurations to make connections to our Tooling Web Solution secured!

In order to get a valid SSL certificate – you need to register a new domain name, you can do it using any Domain name registrar – a company that manages reservation of domain names. The most popular ones are: Godaddy.com, Domain.com, Bluehost.com.

![p10 4](https://user-images.githubusercontent.com/96151001/166480770-019356d7-7f79-4a8a-898e-b6bf0abc7a30.png)

Register a new domain name with any registrar of your choice in any domain zone (e.g. .com, .net, .org, .edu, .info, .xyz or any other)

![p10 3](https://user-images.githubusercontent.com/96151001/166480735-f9eb3a17-4e19-4dcd-967e-f8298cbd669d.png)

Assign an Elastic IP to your Nginx LB server and associate your domain name with this Elastic IP (public IP was used because of free tier limit)

![p10 2](https://user-images.githubusercontent.com/96151001/166480695-3661bf7b-ae1c-441d-a284-d42baf9b51a0.png)

Update A record in your register to point to Nginx LB using public IP address

Configure Nginx to recognize your new domain name

Update your nginx.conf with server_name www.<your-domain-name.com> instead of server_name www.domain.com. Test the domain on a browser.

![p10 15](https://user-images.githubusercontent.com/96151001/166481201-d78efc13-27fe-4d91-a552-f83d214b044d.png)

Install certbot and request for an SSL/TLS certificate

Make sure snapd service is active and running

     $ sudo systemctl status snapd

![p10 16](https://user-images.githubusercontent.com/96151001/166481247-35c37825-1346-4f0e-bf4a-7c3d4322e9b2.png)

Install certbot

     $ sudo snap install --classic certbot

![p10 21](https://user-images.githubusercontent.com/96151001/166486038-c558ff85-77ab-4ce1-989f-74986711dba7.png)

Request your certificate (just follow the certbot instructions – you will need to choose which domain you want your certificate to be issued for, domain name will be looked up from nginx.conf file so make sure you have updated it on step 4).

     $ sudo ln -s /snap/bin/certbot /usr/bin/certbot

     $ sudo certbot --nginx

![p10 22](https://user-images.githubusercontent.com/96151001/166486220-04f9d70e-ff03-4a03-a599-a2df1f9570e0.png)

Test secured access to your Web Solution by trying to reach https://<your-domain-name.com>

![p10 24](https://user-images.githubusercontent.com/96151001/166487404-edcde515-594c-44ab-9852-9d6b9eb784ab.png)

You shall be able to access your website by using HTTPS protocol (that uses TCP port 443) and see a padlock pictogram in your browser’s search string.
Click on the padlock icon and you can see the details of the certificate issued for your website.

![p10 26](https://user-images.githubusercontent.com/96151001/166487444-53b562e0-0870-4a63-a347-ede08197f8e2.png)

Set up periodical renewal of your SSL/TLS certificate

By default, LetsEncrypt certificate is valid for 90 days, so it is recommended to renew it at least every 60 days or more frequently.

You can test renewal command in dry-run mode

     $ sudo certbot renew --dry-run

![p10 27](https://user-images.githubusercontent.com/96151001/166487483-e0a3725b-53fd-419f-a750-23bcdef650df.png)

Best pracice is to have a scheduled job that to run renew command periodically. Let us configure a cronjob to run the command twice a day.

To do so, lets edit the crontab file with the following command:

     $ crontab -e

![p10 28](https://user-images.githubusercontent.com/96151001/166487499-b57a3eb0-4d48-4b72-845e-712a6da4e8f5.png)

Add following line:

     * */12 * * *   root /usr/bin/certbot renew > /dev/null 2>&1

![p10 29](https://user-images.githubusercontent.com/96151001/166487541-4e3c255d-1420-4908-a2c1-3e694e5764f6.png)

You can always change the interval of this cronjob if twice a day is too often by adjusting schedule expression.


![p10 30](https://user-images.githubusercontent.com/96151001/166487578-c95d9cac-1efb-40bc-b54e-12897951a948.png)



