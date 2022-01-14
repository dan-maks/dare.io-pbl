
# Project 3

## Step 1 - Backend configuration

Updates ubuntu

    $ sudo apt update
   
Upgraded ubuntu using the below command

    $ sudo apt upgrade
   
Use the below command to get the location of Node.js software from Ubuntu repositories

    $ curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -
   
Install Node.js with the command below

    $ sudo apt-get install -y nodejs
   
Verify the node installation with the command below

    $ node -v 
   
Verify the node installation with the command below

    $ npm -v
   
#### Application Code Setup   
   
Create a new directory for your To-Do project

    $ mkdir Todo
   
Ran the command below to verify that the Todo directory is created with ls command  

    $ ls
   
Changed the current directory to the newly created one using the command below

    $ cd Todo
   
Use the command npm init to initialise project, so that a new file named package.json will be created. Enter several times to accept default values, then accept to write out the package.json file by typing yes.

    $ npm init
   
Ran the command ls to confirm that you have package.json file created   

    $ ls
   
#### Install ExpressJS

To use express, install it using npm

    $ npm install express
   
Create a file *index.js* with the command below 

    $ touch index.js
   
Ran *ls* to confirm that the index.js file is successfully created

    $ ls
   
Installs the *dotenv* module using the command below 

    $ npm install dotenv
   
Open the index.js file with the command below

    $ vim index.js
    
Pasted the code below into the file and save it

    
    const express = require('express');
    require('dotenv').config();

    const app = express();

    const port = process.env.PORT || 5000;

    app.use((req, res, next) => {
    res.header("Access-Control-Allow-Origin", "\*");
    res.header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept");
    next();
    });

    app.use((req, res, next) => {
    res.send('Welcome to Express');
    });

    app.listen(port, () => {
    console.log(`Server running on port ${port}`)
    });
    
        
 Open the terminal in the same directory as index.js file and type the below command to test if our server works
 
    $ node index.js
     
The output, server running on port 5000 in the terminal shows that everything goes well

Created an inbound rule to open TCP port 5000 in the EC2 security group 

Open up browser and try to access the server’s Public IP or Public DNS name followed by port 5000

    http://<PublicIP-or-PublicDNS>:5000
    
#### Routes

There are three actions that our To-Do application needs to be able to do:

1. Create a new task
2. Display list of all tasks
3. Delete a completed task

Each task will be associated with some particular endpoint and will use different standard HTTP request methods: POST, GET, DELETE.

Created a folder routes using the command below that will define various endpoints that the To-do app will depend on

    $ mkdir routes
    
Changed directory to routes folder using the command below

    $ cd routes 
    
Created a file api.js with the command below

    $ touch api.js
    
Open the file with the command below

    $ vim api.js
    
Copy below code in the file


    const express = require ('express');
    const router = express.Router();

    router.get('/todos', (req, res, next) => {

    });

    router.post('/todos', (req, res, next) => {

    });

    router.delete('/todos/:id', (req, res, next) => {

    })

    module.exports = router;
    
        
### MODELS     
       
To create a Schema and a model, install mongoose which is a Node.js package that makes working with mongodb easier.   


   
   
