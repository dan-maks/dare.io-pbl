# Project 9 - Continous Integration Pipeline For Tooling Website

## Task

Enhance the architecture prepared in Project 8 by adding a Jenkins server, configure a job to automatically deploy source codes changes from Git to NFS server.

## Step 1 - Install Jenkins server

Create an AWS EC2 server based on Ubuntu Server 20.04 LTS and name it "Jenkins". Connect to the server via SSH

Install JDK (since Jenkins is a Java-based application). Update the Ubuntu Server first.

     $ sudo apt update
     $ sudo apt install default-jdk-headless

![p9 1](https://user-images.githubusercontent.com/96151001/166061339-6daf7ac4-d9a9-4fa0-b7dc-f50eea3c34dd.png)

Install Jenkins

     $ wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
       sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > \
           /etc/apt/sources.list.d/jenkins.list'
       sudo apt update
       sudo apt-get install jenkins


![p9 2](https://user-images.githubusercontent.com/96151001/166061365-7d5f0158-4aef-44c2-8563-54df663eee60.png)

Make sure Jenkins is up and running

     $ sudo systemctl status jenkins

![p9 3](https://user-images.githubusercontent.com/96151001/166061384-4ca5230c-5b76-412d-a63f-195f745bb97d.png)

By default Jenkins server uses TCP port 8080 – open it by creating a new Inbound Rule in your EC2 Security Group

Perform initial Jenkins setup.

From your browser access http://<Jenkins-Server-Public-IP-Address-or-Public-DNS-Name>:8080

![p9 4](https://user-images.githubusercontent.com/96151001/166061405-9f7ce806-4635-48a6-b24e-c058a540b676.png)  
  
You will be prompted to provide a default admin password

Retrieve it from your server.
  
     $ sudo cat /var/lib/jenkins/secrets/initialAdminPassword

![p9 6](https://user-images.githubusercontent.com/96151001/166061450-83c9327c-1f66-4e8f-bb42-045c90243b98.png)   
  
Then you will be asked which plugings to install – choose suggested plugins.
  
![p9 5](https://user-images.githubusercontent.com/96151001/166061425-ac1f1972-8987-4f2a-a867-d57fc638b24c.png)  
  
Once plugins installation is done – create an admin user and you will get your Jenkins server address.

![p9 7](https://user-images.githubusercontent.com/96151001/166061497-8e274a08-6b89-42c6-af99-b0d5b8572494.png)  
  
The installation is completed!

![p9 8](https://user-images.githubusercontent.com/96151001/166061526-a93656ae-b4f6-4c30-8904-442397fcc99a.png) 
  
Step 2 – Configure Jenkins to retrieve source codes from GitHub using Webhooks
  
In this part, you will learn how to configure a simple Jenkins job/project (these two terms can be used interchangeably). This job will will be triggered by GitHub webhooks and will execute a ‘build’ task to retrieve codes from GitHub and store it locally on Jenkins server.
  
Enable webhooks in your GitHub repository settings

![p9 9](https://user-images.githubusercontent.com/96151001/166061542-2056f66a-5857-40cc-92f9-5993093a65ba.png)
  
Go to Jenkins web console, click "New Item" and create a "Freestyle project"
  
To connect your GitHub repository, you will need to provide its URL, you can copy from the repository itself

In configuration of your Jenkins freestyle project choose Git repository, provide there the link to your Tooling GitHub repository and credentials (user/password) so Jenkins could access files in the repository.

![p9 13](https://user-images.githubusercontent.com/96151001/166061712-29363541-78f7-4d70-b70b-09b4a15638fb.png)
  
Save the configuration and let us try to run the build. For now we can only do it manually.
  
Click "Build Now" button, if you have configured everything correctly, the build will be successfull and you will see it under #1

You can open the build and check in "Console Output" if it has run successfully.

![p9 14](https://user-images.githubusercontent.com/96151001/166061745-e8a574f2-52e8-4b2e-818a-3007cc4668fb.png)  
  
If so – congratulations! You have just made your very first Jenkins build!

But this build does not produce anything and it runs only when we trigger it manually. Let us fix it.
  
Click "Configure" your job/project and add these two configurations
  
Configure triggering the job from GitHub webhook:

Configure "Post-build Actions" to archive all the files – files resulted from a build are called "artifacts".
  
Now, go ahead and make some change in any file in your GitHub repository (e.g. README.MD file) and push the changes to the master branch.

![p9 18](https://user-images.githubusercontent.com/96151001/166227753-8b531624-cd0d-4e6e-bce1-68413988cd47.png)
     
You will see that a new build has been launched automatically (by webhook) and you can see its results – artifacts, saved on Jenkins server.

![p9 17](https://user-images.githubusercontent.com/96151001/166227735-741a7839-d9f3-45c4-86eb-9866781e8125.png)
    
You have now configured an automated Jenkins job that receives files from GitHub by webhook trigger (this method is considered as ‘push’ because the changes are being ‘pushed’ and files transfer is initiated by GitHub). There are also other methods: trigger one job (downstreadm) from another (upstream), poll GitHub periodically and others.

![p9 19](https://user-images.githubusercontent.com/96151001/166227771-8733da18-5dd5-43a0-95a4-26f2c6c24d83.png)
     
By default, the artifacts are stored on Jenkins server locally

     $ ls /var/lib/jenkins/jobs/tooling_github/builds/<build_number>/archive/  

![p9 20](https://user-images.githubusercontent.com/96151001/166227804-ab3ebdb5-bde8-47e3-9ae8-b2f7408ff7a9.png)
     
Step 3 – Configure Jenkins to copy files to NFS server via SSH
  
Now we have our artifacts saved locally on Jenkins server, the next step is to copy them to our NFS server to /mnt/apps directory.
  
Jenkins is a highly extendable application and there are 1400+ plugins available. We will need a plugin that is called "Publish Over SSH".
  
Install "Publish Over SSH" plugin.
     
On main dashboard select "Manage Jenkins" and choose "Manage Plugins" menu item.

On "Available" tab search for "Publish Over SSH" plugin and install it  
  
![p9 21](https://user-images.githubusercontent.com/96151001/166227821-12a72e15-2588-42ca-88bc-5c62c1f63c9a.png)
     
Configure the job/project to copy artifacts over to NFS server.
  
On main dashboard select "Manage Jenkins" and choose "Configure System" menu item.

Scroll down to Publish over SSH plugin configuration section and configure it to be able to connect to your NFS server:
  
1. Provide a private key (content of .pem file that you use to connect to NFS server via SSH/Putty)
2. Arbitrary name
3. Hostname – can be private IP address of your NFS server
4. Username – ec2-user (since NFS server is based on EC2 with RHEL 8)
5. Remote directory – /mnt/apps since our Web Servers use it as a mointing point to retrieve files from the NFS server
    
Test the configuration and make sure the connection returns Success. Remember, that TCP port 22 on NFS server must be open to receive SSH connections.

![p9 23](https://user-images.githubusercontent.com/96151001/166227878-73a3ba15-e778-4fe3-80bf-ab5d978478cf.png)
     
Save the configuration, open your Jenkins job/project configuration page and add another one "Post-build Action"

Configure it to send all files probuced by the build into our previouslys define remote directory. In our case we want to copy all files and directories – so we use **.

![p9 24](https://user-images.githubusercontent.com/96151001/166227915-7d14c162-872e-4cdd-bc07-846996de5018.png)
     
Save this configuration and go ahead, change something in README.MD file in your GitHub Tooling repository.

We have to change ownership and mode of our /mnt/apps so that our congiguration can work
     
     $ sudo chmod 777 /mnt/apps
     
     $ sudo chown nobody:nobody /mnt/apps

![p9 26](https://user-images.githubusercontent.com/96151001/166227953-dcc61094-6fdb-482f-a973-f21a9847a8ca.png)     
     
Webhook will trigger a new job and in the "Console Output" of the job you will find something like this:

     SSH: Transferred 25 file(s)
     Finished: SUCCESS  

![p9 27](https://user-images.githubusercontent.com/96151001/166227968-b5c7b0eb-f517-47ec-9d3b-a22eb2c7efd7.png)
     
To make sure that the files in /mnt/apps have been updated – connect via SSH/Putty to your NFS server and check README.MD file

     $ cat /mnt/apps/README.md  

![p9 29](https://user-images.githubusercontent.com/96151001/166228010-3b18446b-a298-4296-8977-2b197de3406a.png)     
     
![p9 30](https://user-images.githubusercontent.com/96151001/166228031-c9654fb0-2755-4241-adda-ccd96f266769.png)
     
If you see the changes you had previously made in your GitHub – the job works as expected. 
     
![p9 31](https://user-images.githubusercontent.com/96151001/166228046-fe60761e-938e-41ef-8b16-352d154846fc.png)      
  
  
  
