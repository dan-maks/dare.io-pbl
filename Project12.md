# Project 12 - ANSIBLE REFACTORING AND STATIC ASSIGNMENTS (IMPORTS AND ROLES)

## Step 1 – Jenkins job enhancement

Go to your Jenkins-Ansible server and create a new directory called ansible-config-artifact – we will store there all artifacts after each build.

    $ sudo mkdir /home/ubuntu/ansible-config-artifact

Change permissions to this directory, so Jenkins could save files there – 

    $ sudo chmod -R 0777 /home/ubuntu/ansible-config-artifact

![prj 5](https://user-images.githubusercontent.com/96151001/168141496-b89c292f-c4cc-4873-90fb-ab9a91ed0c52.png)

Go to Jenkins web console -> Manage Jenkins -> Manage Plugins -> on Available tab search for Copy Artifact and install this plugin without restarting Jenkins

![prj 6](https://user-images.githubusercontent.com/96151001/168141567-29376977-8898-475c-b77a-5b71d0a26341.png)

Create a new Freestyle project and name it save_artifacts.

This project will be triggered by completion of your existing ansible project. Configure it accordingly

The main idea of save_artifacts project is to save artifacts into /home/ubuntu/ansible-config-artifact directory. To achieve this, create a Build step and choose Copy artifacts from other project, specify ansible as a source project and /home/ubuntu/ansible-config-artifact as a target directory.

Test your set up by making some change in README.MD file inside your ansible-config-mgt repository (right inside master branch).

![prj 13](https://user-images.githubusercontent.com/96151001/168137559-87e697ee-c745-45e2-9a39-0693923748bf.png)

If both Jenkins jobs have completed one after another – you shall see your files inside /home/ubuntu/ansible-config-artifact directory and it will be updated with every commit to your main branch.

![prj 14](https://user-images.githubusercontent.com/96151001/168137583-4c40d8e1-1c60-498a-b898-905475e67162.png)

Now your Jenkins pipeline is more neat and clean.


## Step 2 – Refactor Ansible code by importing other playbooks into site.yml

Let see code re-use in action by importing other playbooks.

Within playbooks folder, create a new file and name it site.yml – This file will now be considered as an entry point into the entire infrastructure configuration. Other playbooks will be included here as a reference. In other words, site.yml will become a parent to all other playbooks that will be developed. Including common.yml that you created previously.

Create a new folder in root of the repository and name it static-assignments. The static-assignments folder is where all other children playbooks will be stored. This is merely for easy organization of your work. It is not an Ansible specific concept, therefore you can choose how you want to organize your work. You will see why the folder name has a prefix of static very soon. For now, just follow along.

Move common.yml file into the newly created static-assignments folder.

Inside site.yml file, import common.yml playbook.

    ---
    - hosts: all
    - import_playbook: ../static-assignments/common.yml

![prj 16](https://user-images.githubusercontent.com/96151001/168137698-4ba13c5c-f148-4dbf-9e99-b7dc53950cb2.png)

The code above uses built in import_playbook Ansible module.

Your folder structure should look like this;

    ├── static-assignments
    │   └── common.yml
    ├── inventory
        └── dev
        └── stage
        └── uat
        └── prod
    └── playbooks
        └── site.yml


Since you need to apply some tasks to your dev servers and wireshark is already installed – you can go ahead and create another playbook under static-assignments and name it common-del.yml. In this playbook, configure deletion of wireshark utility.

    ---
    - name: update web, nfs and db servers
      hosts: webservers, nfs, db
      remote_user: ec2-user
      become: yes
      become_user: root
      tasks:
      - name: delete wireshark
        yum:
          name: wireshark
          state: removed

    - name: update LB server
      hosts: lb
      remote_user: ubuntu
      become: yes
      become_user: root
      tasks:
      - name: delete wireshark
        apt:
          name: wireshark-qt
          state: absent
          autoremove: yes
          purge: yes
          autoclean: yes


![prj 17](https://user-images.githubusercontent.com/96151001/168137725-64202dea-1c13-45e5-ab93-015045a1ac37.png)

Update site.yml with - import_playbook: ../static-assignments/common-del.yml instead of common.yml. run it against dev servers:

![prj 18](https://user-images.githubusercontent.com/96151001/168137830-9eabafea-e71f-4ff0-a375-a0a79efb0c97.png)

Check if all the webservers are reachable through our host 
    
    $ ansible all -m ping

![prj 20](https://user-images.githubusercontent.com/96151001/168137887-f2b1dd23-d102-4c5b-b681-a655606d848d.png)

Troubleshoot the error gotten, by opening the ansible config file, uncomment and update the inventory part.

    $ cd inventory/ 
    
    $ sudo vi /etc/ansible/ansible-config

![prj 21](https://user-images.githubusercontent.com/96151001/168137909-7c77529c-c87b-4089-a763-779dbd504793.png)

Retry the command

     $ ansible all -m ping

![prj 22](https://user-images.githubusercontent.com/96151001/168137954-e77163b7-86e2-4af3-80be-5ba7a8552876.png)

Run ansible-playbook command against the dev environment

    $ cd ..

    $ ansible-playbook -i /home/ubuntu/ansible-config-artifact/inventory/dev.yml /home/ubuntu/ansible-config-artifact/playbooks/site.yml

![prj 23](https://user-images.githubusercontent.com/96151001/168137974-adc66c7e-ed93-4d25-ad7e-ddf0b71e815a.png)

![prj 24](https://user-images.githubusercontent.com/96151001/168137993-0ddd925f-0551-4f95-9d5e-489fecc4312e.png)

Make sure that wireshark is deleted on all the servers by running wireshark --version

    $ which wireshark
    $ wireshark --version

![prj 25](https://user-images.githubusercontent.com/96151001/168138022-4f10a62f-c6fc-44c2-b0a3-ac7da2577470.png)

![prj 26](https://user-images.githubusercontent.com/96151001/168138077-0274433c-7da1-41fa-92b5-1804f2552489.png)


## Step 3 – Configure UAT Webservers with a role ‘Webserver’
    
Launch 2 fresh EC2 instances using RHEL 8 image, we will use them as our uat servers, so give them names accordingly – Web1-UAT and Web2-UAT.

To create a role, you must create a directory called roles/, relative to the playbook file or in /etc/ansible/ directory.

There are two ways how you can create this folder structure:

- Use an Ansible utility called ansible-galaxy inside ansible-config-mgt/roles directory (you need to create roles directory upfront)

      $ mkdir roles
    
      $ cd roles
    
      $ ansible-galaxy init webserver

- Create the directory/files structure manually

Note: You can choose either way, but since you store all your codes in GitHub, it is recommended to create folders and files there rather than locally on Jenkins-Ansible server.

Create a new branch 'refactor'

     $ git status
     
     $ git checkout -b refactor

After removing unnecessary directories and files, the roles structure should look like this
 
    └── webserver
        ├── README.md
        ├── defaults
        │   └── main.yml
        ├── handlers
        │   └── main.yml
        ├── meta
        │   └── main.yml
        ├── tasks
        │   └── main.yml
        └── templates
    

Update your inventory ansible-config-mgt/inventory/uat.yml file with IP addresses of your 2 UAT Web servers

    [uat-webservers]
    <Web1-UAT-Server-Private-IP-Address> ansible_ssh_user='ec2-user' 

    <Web2-UAT-Server-Private-IP-Address> ansible_ssh_user='ec2-user' 
     
![prj 30](https://user-images.githubusercontent.com/96151001/168138197-4c76f4f4-0b03-4c8d-b137-af3dc7a9b4e5.png)
    
In /etc/ansible/ansible.cfg file uncomment roles_path string and provide a full path to your roles directory roles_path    = /home/ubuntu/ansible-config-artifact/roles, so Ansible could know where to find configured roles.    

![prj 28](https://user-images.githubusercontent.com/96151001/168138124-07c72712-ce73-48ae-9dfb-e7f3b51cca0b.png)

It is time to start adding some logic to the webserver role. Go into tasks directory, and within the main.yml file, start writing configuration tasks to do the following:

- Install and configure Apache (httpd service)
- Clone Tooling website from GitHub https://github.com/<your-name>/tooling.git.
- Ensure the tooling website code is deployed to /var/www/html on each of 2 UAT Web servers.
- Make sure httpd service is started
  
Your main.yml may consist of following tasks:

    ---
    - name: install apache
      become: true
      ansible.builtin.yum:
        name: "httpd"
        state: present

    - name: install git
      become: true
      ansible.builtin.yum:
        name: "git"
        state: present

    - name: clone a repo
      become: true
      ansible.builtin.git:
        repo: https://github.com/<your-name>/tooling.git
        dest: /var/www/html
        force: yes

    - name: copy html content to one level up
      become: true
      command: cp -r /var/www/html/html/ /var/www/

    - name: Start service httpd, if not started
      become: true
      ansible.builtin.service:
        name: httpd
        state: started

    - name: recursively remove /var/www/html/html/ directory
      become: true
      ansible.builtin.file:
        path: /var/www/html/html
        state: absent

  
## Step 4 – Reference ‘Webserver’ role
    
Within the static-assignments folder, create a new assignment for uat-webservers uat-webservers.yml. This is where you will reference the role.  
    
    ---
    - hosts: uat-webservers
      roles:
         - webserver    

    
![prj 31](https://user-images.githubusercontent.com/96151001/168138218-cb916403-c586-4b5b-900d-f00de4b2429d.png)
    
Remember that the entry point to our ansible configuration is the site.yml file. Therefore, you need to refer your uat-webservers.yml role inside site.yml.

So, we should have this in site.yml

    - hosts: uat-webservers
    - import_playbook: ../static-assignments/uat-webservers.yml

![prj 29](https://user-images.githubusercontent.com/96151001/168138166-4d54bc3c-73cf-42a1-9951-792101229abb.png)
    
## Step 5 – Commit & Test
    
Commit your changes, create a Pull Request and merge them to master branch, make sure webhook triggered two consequent Jenkins jobs, they ran successfully and copied all the files to your Jenkins-Ansible server into /home/ubuntu/ansible-config-artifact/ directory.

    
![prj 32](https://user-images.githubusercontent.com/96151001/168138238-36e01abc-d7a9-418a-b4f3-52ea85b7e918.png)
    
![prj 33](https://user-images.githubusercontent.com/96151001/168138280-7fcf47a8-932c-443c-bd8f-129546a26195.png)
    
![prj 34](https://user-images.githubusercontent.com/96151001/168138308-acc0af5e-9545-4857-b36f-9118fe163b09.png) 

![prj 35](https://user-images.githubusercontent.com/96151001/168138348-fe2ffe12-895b-47c3-8125-bb5d54333ecb.png)

    
Run the playbook against the uat inventory and see what happens:
    
    $ ansible-playbook -i /home/ubuntu/ansible-config-artifact/inventory/uat.yml /home/ubuntu/ansible-config-artifact/playbooks/site.yml

![prj 37](https://user-images.githubusercontent.com/96151001/168138494-f1269506-a91f-49ef-90f6-8ba6c44888ed.png)
    
![prj 36](https://user-images.githubusercontent.com/96151001/168138424-dd846af8-b9cd-4cfa-8278-3cf5ee4e79ce.png)
    
You should be able to see both of your UAT Web servers configured and you can try to reach them from your browser:

    http://<Web1-UAT-Server-Public-IP-or-Public-DNS-Name>/index.php

or

    http://<Web1-UAT-Server-Public-IP-or-Public-DNS-Name>/index.php

    
![prj 38](https://user-images.githubusercontent.com/96151001/168138521-8b63ceaf-b348-4b2d-bc65-440ffb7ebc3e.png)
    
![prj 39](https://user-images.githubusercontent.com/96151001/168138544-549c9c3f-569a-45c6-8b03-a6364a1370b9.png)


    

    
    
