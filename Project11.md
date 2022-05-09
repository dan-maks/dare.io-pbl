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

# Begin Ansible Development.

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

Save below inventory structure in the inventory/dev file to start configuring your development servers. Ensure to replace the IP addresses according to your own setup.


    [nfs]
    <NFS-Server-Private-IP-Address> ansible_ssh_user='ec2-user'

    [webservers]
    <Web-Server1-Private-IP-Address> ansible_ssh_user='ec2-user'
    <Web-Server2-Private-IP-Address> ansible_ssh_user='ec2-user'

    [db]
    <Database-Private-IP-Address> ansible_ssh_user='ec2-user' 

    [lb]
    <Load-Balancer-Private-IP-Address> ansible_ssh_user='ubuntu'
    
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



Examine the code above and try to make sense out of it. This playbook is divided into two parts, each of them is intended to perform the same task: install wireshark utility (or make sure it is updated to the latest version) on your RHEL 8 and Ubuntu servers. It uses root user to perform this task and respective package manager: yum for RHEL 8 and apt for Ubuntu.

Feel free to update this playbook with following tasks:

- Create a directory and a file inside it
- Change timezone on all servers
- Run some shell script
- …



    
            

## Step 6 – Update GIT with the latest code    
    
Now all of your directories and files live on your machine and you need to push changes made locally to GitHub.

In the real world, you will be working within a team of other DevOps engineers and developers. It is important to learn how to collaborate with help of GIT. In many organisations there is a development rule that do not allow to deploy any code before it has been reviewed by an extra pair of eyes – it is also called "Four eyes principle".
    
Now you have a separate branch, you will need to know how to raise a Pull Request (PR), get your branch peer reviewed and merged to the master branch.

Commit your code into GitHub:

    $ git status

    $ git add <selected files>

    $ git commit -m "commit message"

Create a Pull request (PR)

Wear a hat of another developer for a second, and act as a reviewer.

If the reviewer is happy with your new feature development, merge the code to the main branch.

Head back on your terminal, checkout from the feature branch into the master, and pull down the latest changes.

Once your code changes appear in main branch – Jenkins will do its job and save all the files (build artifacts) to /var/lib/jenkins/jobs/ansible/builds/<build_number>/archive/ directory on Jenkins-Ansible server.
    
## Step 7 – Run first Ansible test  

Now, it is time to execute ansible-playbook command and verify if your playbook actually works:
    
    
    
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

    
    
    
    
    
    
