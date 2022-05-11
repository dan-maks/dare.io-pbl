# Project 12 - ANSIBLE REFACTORING AND STATIC ASSIGNMENTS (IMPORTS AND ROLES)

## Step 1 – Jenkins job enhancement

Go to your Jenkins-Ansible server and create a new directory called ansible-config-artifact – we will store there all artifacts after each build.

    $ sudo mkdir /home/ubuntu/ansible-config-artifact

Change permissions to this directory, so Jenkins could save files there – 

    $ sudo chmod -R 0777 /home/ubuntu/ansible-config-artifact

Go to Jenkins web console -> Manage Jenkins -> Manage Plugins -> on Available tab search for Copy Artifact and install this plugin without restarting Jenkins

Create a new Freestyle project (you have done it in Project 9) and name it save_artifacts.

This project will be triggered by completion of your existing ansible project. Configure it accordingly

The main idea of save_artifacts project is to save artifacts into /home/ubuntu/ansible-config-artifact directory. To achieve this, create a Build step and choose Copy artifacts from other project, specify ansible as a source project and /home/ubuntu/ansible-config-artifact as a target directory.

Test your set up by making some change in README.MD file inside your ansible-config-mgt repository (right inside master branch).

If both Jenkins jobs have completed one after another – you shall see your files inside /home/ubuntu/ansible-config-artifact directory and it will be updated with every commit to your main branch.

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

Run ansible-playbook command against the dev environment

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


update site.yml with - import_playbook: ../static-assignments/common-del.yml instead of common.yml and run it against dev servers:

    $ cd /home/ubuntu/ansible-config-mgt/

    $ ansible-playbook -i inventory/dev.yml playbooks/site.yaml
    
Make sure that wireshark is deleted on all the servers by running wireshark --version


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
     
    
In /etc/ansible/ansible.cfg file uncomment roles_path string and provide a full path to your roles directory roles_path    = /home/ubuntu/ansible-config-artifact/roles, so Ansible could know where to find configured roles.    
    
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

  
  
  






    
