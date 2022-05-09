# Project 11 - ANSIBLE CONFIGURATION MANAGEMENT – AUTOMATE PROJECT 7 TO 10

## Task
- Install and configure Ansible client to act as a Jump Server/Bastion Host
- Create a simple Ansible playbook to automate servers configuration

## Step 1 - Install and configure Ansible client to act as a Jump Server/Bastion Host

Update Name tag on your Jenkins EC2 Instance to Jenkins-Ansible. We will use this server to run playbooks.

In your GitHub account create a new repository and name it ansible-config-mgt.

Instal Ansible

    $ sudo apt update

    $ sudo apt install ansible

![ansible 1](https://user-images.githubusercontent.com/96151001/167403047-8690686a-5bdb-4ba6-b42d-02cee21d6c82.png)

Check your Ansible version by running

    $ ansible --version

![ansible 2](https://user-images.githubusercontent.com/96151001/167403092-590ce3a5-c4e0-4cf6-a092-9f4c6658ad59.png)

Configure Jenkins build job to save your repository content every time you change it – this will solidify your Jenkins configuration skills acquired in Project 9.

- Create a new Freestyle project ansible in Jenkins and point it to your ‘ansible-config-mgt’ repository.
- Configure Webhook in GitHub and set webhook to trigger ansible build.
- Configure a Post-build job to save all (**) files, like you did it in Project 9.

![ansible 3](https://user-images.githubusercontent.com/96151001/167403124-d9965ea9-d2f0-43d3-8754-44d354c498e7.png)

![ansible 4](https://user-images.githubusercontent.com/96151001/167406123-23ef6dee-1b58-4f49-8677-2d401c7bf11b.png)

Test your setup by making some change in README.MD file in main branch and make sure that builds starts automatically and Jenkins saves the files (build artifacts) in following folder

![ansible 6](https://user-images.githubusercontent.com/96151001/167406184-39bc664d-ee44-457c-8912-1ba22b326626.png)

![ansible 5](https://user-images.githubusercontent.com/96151001/167406158-046898dd-4e33-4880-9906-15e7f8be01bf.png)

    $ ls /var/lib/jenkins/jobs/ansible/builds/<build_number>/archive/
    
![ansible 7](https://user-images.githubusercontent.com/96151001/167406210-939a6e6a-3055-46c0-b924-68c88888f1d5.png)

Note: Trigger Jenkins project execution only for /main branch.


## Step 2 – Prepare your development environment using Visual Studio Code

First part of ‘DevOps’ is ‘Dev’, which means you will require to write some codes and you shall have proper tools that will make your coding and debugging comfortable – you need an Integrated development environment (IDE) or Source-code Editor. There is a plethora of different IDEs and Source-code Editors for different languages with their own advantages and drawbacks, you can choose whichever you are comfortable with, but we recommend one free and universal editor that will fully satisfy your needs – Visual Studio Code (VSC)
    
After you have successfully installed VSC, configure it to connect to your newly created GitHub repository.

Clone down your ansible-config-mgt repo to your Jenkins-Ansible instance

    git clone <ansible-config-mgt repo link>

![ansible 8](https://user-images.githubusercontent.com/96151001/167408304-5c9fc9d5-073c-456c-ac0e-f56c4d5d40f8.png)

# Step 3 - Begin Ansible Development.

In your ansible-config-mgt GitHub repository, create a new branch (prj-11) that will be used for development of a new feature.

![ansible 9](https://user-images.githubusercontent.com/96151001/167408327-a2ff6a6f-debd-4b1d-b907-1690305f0f7a.png)

Checkout the newly created feature branch to your local machine and start building your code and directory structure

![ansible 10](https://user-images.githubusercontent.com/96151001/167408348-eef58aff-45a6-4975-81e6-d565e3f13e35.png)

![ansible 11](https://user-images.githubusercontent.com/96151001/167408387-4ae07d33-e878-4594-9c3b-1ce03ab47225.png)

Create a directory and name it playbooks – it will be used to store all your playbook files.

![ansible 12](https://user-images.githubusercontent.com/96151001/167408453-681b50f5-ad4b-4445-991e-8616eb2567f3.png)

Create a directory and name it inventory – it will be used to keep your hosts organised.

Within the playbooks folder, create your first playbook, and name it common.yml

Within the inventory folder, create an inventory file (.yml) for each environment (Development, Staging Testing and Production) dev, staging, uat, and prod respectively.

![ansible 13](https://user-images.githubusercontent.com/96151001/167408483-b4c645d5-a4ff-4fd8-98ef-2e259d25e9ab.png)

## Step 4 – Set up an Ansible Inventory    

An Ansible inventory file defines the hosts and groups of hosts upon which commands, modules, and tasks in a playbook operate. Since our intention is to execute Linux commands on remote hosts, and ensure that it is the intended configuration on a particular server that occurs. It is important to have a way to organize our hosts in such an Inventory.

Create 4 RHEL based instances and one ubuntu instance.

Note: Ansible uses TCP port 22 by default, which means it needs to ssh into target servers from Jenkins-Ansible host – for this you can implement the concept of ssh-agent. Now you need to import your key into ssh-agent:

    $ eval `ssh-agent -s`
    
    $ ssh-add <path-to-private-key>
    
Now, ssh into your Jenkins-Ansible server using ssh-agent

    $ ssh -A ubuntu@public-ip

![ansible 14](https://user-images.githubusercontent.com/96151001/167408520-189100e5-7939-49e0-bfac-7c41388fb0f5.png)

Also notice, that your Load Balancer user is ubuntu and user for RHEL-based servers is ec2-user.
    
Update your inventory/dev.yml file with this snippet of code:   
    
    [nfs]
    <NFS-Server-Private-IP-Address> ansible_ssh_user='ec2-user'

    [webservers]
    <Web-Server1-Private-IP-Address> ansible_ssh_user='ec2-user'
    <Web-Server2-Private-IP-Address> ansible_ssh_user='ec2-user'

    [db]
    <Database-Private-IP-Address> ansible_ssh_user='ec2-user' 

    [lb]
    <Load-Balancer-Private-IP-Address> ansible_ssh_user='ubuntu


![ansible 16](https://user-images.githubusercontent.com/96151001/167408598-d9e06ab9-5b8e-4bee-9090-84efb9f5e76b.png)


## Step 5 – Create a Common Playbook

It is time to start giving Ansible the instructions on what you needs to be performed on all servers listed in inventory/dev.

In common.yml playbook you will write configuration for repeatable, re-usable, and multi-machine tasks that is common to systems within the infrastructure.

Update your playbooks/common.yml file with following code:

    ---
    - name: update web, nfs and db servers
      hosts: webservers, nfs, db
      remote_user: ec2-user
      become: yes
      become_user: root
      tasks:
        - name: ensure wireshark is at the latest version
          yum:
            name: wireshark
            state: latest

    - name: update LB server
      hosts: lb
      remote_user: ubuntu
      become: yes
      become_user: root
      tasks:
        - name: Update apt repo
          apt: 
          update_cache: yes

        - name: ensure wireshark is at the latest version
          apt:
            name: wireshark
            state: latest


![ansible 17](https://user-images.githubusercontent.com/96151001/167408633-2fa051de-079d-4203-ae70-8d612f0db754.png)

Examine the code above and try to make sense out of it. This playbook is divided into two parts, each of them is intended to perform the same task: install wireshark utility (or make sure it is updated to the latest version) on your RHEL 8 and Ubuntu servers. It uses root user to perform this task and respective package manager: yum for RHEL 8 and apt for Ubuntu.
            

## Step 6 – Update GIT with the latest code    
    
Now all of your directories and files live on your machine and you need to push changes made locally to GitHub.

In the real world, you will be working within a team of other DevOps engineers and developers. It is important to learn how to collaborate with help of GIT. In many organisations there is a development rule that do not allow to deploy any code before it has been reviewed by an extra pair of eyes – it is also called "Four eyes principle".
    
Now you have a separate branch, you will need to know how to raise a Pull Request (PR), get your branch peer reviewed and merged to the master branch.

Commit your code into GitHub:

    $ git status

    $ git add .

    $ git commit -m "commit message"

![ansible 18](https://user-images.githubusercontent.com/96151001/167408653-7023301a-c31a-4f22-af86-b74a05ef5a86.png)
    
Create a Pull request (PR)

    $ git push origin prj-11

![ansible 19](https://user-images.githubusercontent.com/96151001/167408697-e36b6e06-9bba-4e6a-bc52-fc6be7ed17b2.png)

Wear a hat of another developer for a second, and act as a reviewer.

If the reviewer is happy with your new feature development, merge the code to the main branch.

![ansible 20](https://user-images.githubusercontent.com/96151001/167408724-3937559b-12ce-44f3-8e84-e31da8a161db.png)

![ansible 21](https://user-images.githubusercontent.com/96151001/167408767-5b96bf00-2409-499d-bd2d-9f4eac00b4a8.png)

![ansible 22](https://user-images.githubusercontent.com/96151001/167408807-6b5c7ca0-5bff-43cb-9c22-9aec7dc6b680.png)

![ansible 23](https://user-images.githubusercontent.com/96151001/167408864-b479fcba-8863-4ac3-9160-405affd52e3c.png)

![ansible 24](https://user-images.githubusercontent.com/96151001/167437296-cbd44975-695d-4617-9033-a70314f94b5c.png)


Head back on your terminal, checkout from the feature branch into the main, and pull down the latest changes.

![ansible 28](https://user-images.githubusercontent.com/96151001/167437405-dbd1dd56-05a6-459d-8cf5-738cbb00b034.png)

![ansible 29](https://user-images.githubusercontent.com/96151001/167437434-b0da917e-0adf-4b03-a1b4-acb6f04ba7fe.png)


Once your code changes appear in main branch – Jenkins will do its job and save all the files (build artifacts) to /var/lib/jenkins/jobs/ansible/builds/<build_number>/archive/ directory on Jenkins-Ansible server.

![ansible 25](https://user-images.githubusercontent.com/96151001/167437329-71637696-08cf-435b-b020-6b5885a6f240.png)

![ansible 26](https://user-images.githubusercontent.com/96151001/167437357-3daf4387-5784-492d-a90f-79ef4f0034dc.png)

![ansible 27](https://user-images.githubusercontent.com/96151001/167437377-f790c66b-e4b6-4855-9b1b-1d3c3f4dde9f.png)


## Step 7 – Run first Ansible test  

Now, it is time to execute ansible-playbook command and verify if your playbook actually works:

Connect to the host using remote-ssh on the VSC, but first update the ssh config file.

![Screenshot 2022-05-09 153251](https://user-images.githubusercontent.com/96151001/167433103-0785feca-6658-4149-9876-950af6b43a8f.png)

![ansible 30](https://user-images.githubusercontent.com/96151001/167437455-606ee5a8-c16a-4dc9-bead-07797959dc80.png)


Test if all the instances will connect on the host, and exit after checking all the instances.
      
    $ ssh ubuntu@<Private-IP-Address>
    $ ssh ec2-useer@<Private-IP-Address>
    
Run the ansible test on the lastest build on Jenkins

    $ /var/lib/jenkins/jobs/ansible/builds/<build_number>/archive/inventory/dev.yml /var/lib/jenkins/jobs/ansible/builds/<build_number>/archive/playbooks/common.yml
 
![ansible 32](https://user-images.githubusercontent.com/96151001/167437527-56eba9b3-498b-4786-ba8c-566eee207a92.png)


ssh into one of the webserver and check the wireshark version

    $ wireshark --version

![ansible 33](https://user-images.githubusercontent.com/96151001/167437560-5e175de5-5769-4191-a3e8-64f57c6bd325.png)
    
Also check for the ubuntu instance

![ansible 34](https://user-images.githubusercontent.com/96151001/167437583-c932e922-aa79-4ab0-bfb8-67a87b7b5eb2.png)  


## Optional step – Repeat once again
 
Update your ansible playbook with some new Ansible tasks and go through the full checkout -> change codes -> commit -> PR -> merge -> build -> ansible-playbook cycle again to see how easily you can manage a servers fleet of any size with just one command!   
        
    $ git status
    
    $ git checkout -b prj-11
    
Update the common.yml file. Create a directory and a file inside it, Change timezone on all servers, Run some shell script.


    ---
    - name: update web, nfs and db servers
      hosts: webservers, nfs, db
      remote_user: ec2-user
      become: yes
      become_user: root
      tasks:
        - name: ensure wireshark is at the latest version
          yum:
            name: wireshark
            state: latest

    # ---------------------------------------------------------        

    - name: update LB server
      hosts: lb
      remote_user: ubuntu
      become: yes
      become_user: root
      tasks:
        - name: Update apt repo
          apt: 
          update_cache: yes

    - name: ensure wireshark is at the latest version
      apt:
        name: wireshark
        state: latest


    - name: create directory, file and set timezone on all servers
      hosts: webservers, nfs, db, lb
      become: yes
      tasks:
  
        - name: create a directory
          file:
            path: /home/sample
            state: directory

        - name: create a file
          file: 
            path: /home/sample/ansible.txt 
            state: touch

        - name: set timezone
          timezone:
            name: Africa/Lagos


On the terminal, run the follwing commands:

    $ git add playbooks/common.yml

    $ git commit -m "added more tasks"


Push the changes made

Move to github and create a pull request, make review and merge the code to the main branch.
    
![ans 1](https://user-images.githubusercontent.com/96151001/167462468-4db128f5-80ab-454e-b789-90dbc058b514.png)

![ans 2](https://user-images.githubusercontent.com/96151001/167462516-35058d24-756b-43fa-95a4-05f4e3381e25.png)

![ans 3](https://user-images.githubusercontent.com/96151001/167462528-28125db2-e9d5-4b87-b166-a4adf3def533.png)


Head back on your terminal, checkout from the feature branch into the main, and pull down the latest changes.

![ans 4](https://user-images.githubusercontent.com/96151001/167462550-4272567f-7938-420a-89fd-d428302a5925.png)

Once your code changes appear in main branch – Jenkins will do its job and save all the files (build artifacts) to /var/lib/jenkins/jobs/ansible/builds/<build_number>/archive/ directory on Jenkins-Ansible server.

![ans 6](https://user-images.githubusercontent.com/96151001/167462593-2dbeddfd-4859-4b27-8a86-4a8d60ea7822.png)

![ans 5](https://user-images.githubusercontent.com/96151001/167462573-5c70d6d1-54ee-40ab-843b-3e78c34c3cc8.png)


First try to connect to all the instances on the host using ssh, and exit after checking all the instances.
      
    $ ssh ubuntu@<Private-IP-Address>
    $ ssh ec2-useer@<Private-IP-Address>

![ans 7](https://user-images.githubusercontent.com/96151001/167462613-42e213d7-a8c5-4d94-99f5-b6197311b833.png)
    
Run the ansible test on the lastest build on Jenkins

    $ /var/lib/jenkins/jobs/ansible/builds/<build_number>/archive/inventory/dev.yml /var/lib/jenkins/jobs/ansible/builds/<build_number>/archive/playbooks/common.yml

![ans 9](https://user-images.githubusercontent.com/96151001/167462672-f7f3e510-d536-4b09-85d3-fda9eb710517.png)

![ans 8](https://user-images.githubusercontent.com/96151001/167462632-c811e1d7-71dc-4db6-a3a5-0c6609c4149d.png)


Connect to the instances, and checkout ansible playbook actions

![ans 10](https://user-images.githubusercontent.com/96151001/167462706-d99c84a8-a107-416b-94f7-d790ff4b1afb.png)

![ans 11](https://user-images.githubusercontent.com/96151001/167462735-e8e11cb8-0998-4162-a48f-6bc1f384f213.png)

 


