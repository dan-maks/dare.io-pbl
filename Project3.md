
# Project 3

## Step 1 - Backend configuration

Updates ubuntu

    $ sudo apt update
    
![mern 1](https://user-images.githubusercontent.com/96151001/149522383-6dd7780a-ed66-4011-b4c8-732254eae399.PNG)    
   
Upgraded ubuntu using the below command

    $ sudo apt upgrade
    
![mern 2](https://user-images.githubusercontent.com/96151001/149522411-0872d2fc-6294-4849-8c50-761c3eb9def6.PNG)    
   
Use the below command to get the location of Node.js software from Ubuntu repositories

    $ curl -sL https://deb.nodesource.com/setup_16.x | sudo -E bash -
    
![node ins](https://user-images.githubusercontent.com/96151001/156598430-c93af2b3-fbf8-4984-a301-398baa1373f5.PNG)
              
Install Node.js with the command below

    $ sudo apt-get install -y nodejs
    
![mern n1](https://user-images.githubusercontent.com/96151001/156595843-ee3ccfca-8168-465a-9ad2-ea1c5a6968b3.PNG)
      
Verify the node installation with the command below

    $ node -v 
   
Verify the node installation with the command below

    $ npm -v
     
     
#### Application Code Setup   
   
Create a new directory for your To-Do project

    $ mkdir Todo
     
Ran the command below to verify that the Todo directory is created with ls command  

    $ ls
    
![mern 7](https://user-images.githubusercontent.com/96151001/149522521-bd8ec932-5f73-4148-ab8b-5a4b40b25e53.PNG)    
   
Changed the current directory to the newly created one using the command below

    $ cd Todo
    
![mern 8](https://user-images.githubusercontent.com/96151001/149522544-edb02daa-0a3b-47a8-971b-57e9c01d63ce.PNG)    
   
Use the command npm init to initialise project, so that a new file named package.json will be created. Enter several times to accept default values, then accept to write out the package.json file by typing yes.

    $ npm init
    
![mern n2](https://user-images.githubusercontent.com/96151001/156600050-8838dd3e-67ae-4851-b7e6-3549cbcf3933.PNG)

Ran the command ls to confirm that you have package.json file created   

    $ ls
    
![mern 10](https://user-images.githubusercontent.com/96151001/149524673-82e29a89-6c86-4cd8-b991-28368b4c6605.PNG)    
   
   
#### Install ExpressJS

To use express, install it using npm

    $ npm install express
       
![mern 11](https://user-images.githubusercontent.com/96151001/149524709-bd090d2f-b371-4a2e-934e-b7aa217a749e.PNG)    
   
Create a file *index.js* with the command below 

    $ touch index.js
   
Ran *ls* to confirm that the index.js file is successfully created

    $ ls
   
![mern 12](https://user-images.githubusercontent.com/96151001/149524745-6645e7db-50ce-49b7-a62c-00ad413c2807.PNG) 

Installs the *dotenv* module using the command below 

    $ npm install dotenv
    
![mern 13](https://user-images.githubusercontent.com/96151001/149524765-02a74367-4299-476f-ad89-f39d57ab893f.PNG) 

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
    
 
 
 ![mern n3](https://user-images.githubusercontent.com/96151001/156601122-8b8e011a-c1c8-4c40-aa37-e56e4e48b0ae.PNG)

 
 
 Open the terminal in the same directory as index.js file and type the below command to test if our server works
 
    $ node index.js
     
![mern 15](https://user-images.githubusercontent.com/96151001/149524818-ed09f749-c610-4992-bab2-c3f10417c9d5.PNG)  
  
The output, server running on port 5000 in the terminal shows that everything goes well

Created an inbound rule to open TCP port 5000 in the EC2 security group 

Open up browser and try to access the server’s Public IP or Public DNS name followed by port 5000

    http://<PublicIP-or-PublicDNS>:5000
    
![mern 16](https://user-images.githubusercontent.com/96151001/149524836-d138a785-68ae-4d0d-b8e7-f4d7595ac636.PNG) 

    
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
    
![mern 17](https://user-images.githubusercontent.com/96151001/149524879-e7156c5f-1243-47d0-9a44-3e90c8b655f7.PNG)    
    
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
 
 
 
![mern n4](https://user-images.githubusercontent.com/96151001/156604106-0d8f3c74-91e7-4397-acdb-8505b322475d.PNG)


#### MODELS     

A model is at the heart of JavaScript based applications, and it is what makes it interactive. We will also use models to define the database schema. Schema is a blueprint of how the database will be constructed, including other data fields that may not be required to be stored in the database. These are known as virtual properties
       
To create a Schema and a model, install mongoose which is a Node.js package that makes working with mongodb easier.

Change directory back Todo folder with *cd ..* and install Mongoose using the command below

    $ npm install mongoose
    
![mern 19](https://user-images.githubusercontent.com/96151001/149524920-919968eb-e47f-4837-b712-a8d880b9e8a7.PNG)

Create a new folder models using the command below

    $ mkdir models
    
Changed directory into the newly created ‘models’ folder with the below command

    $ cd models
    
Inside the models folder, create a file and name it todo.js

    $ touch todo.js
    
![mern 20](https://user-images.githubusercontent.com/96151001/149529603-de89a0f0-1b0c-4674-beb1-590d74a5ffef.PNG)    
    
Tip: All three commands above can be defined in one line to be executed consequently with help of && operator, like this:

    $ mkdir models && cd models && touch todo.js
   
Open the file created with *vim todo.js* then paste the code below in the file


    const mongoose = require('mongoose');
    const Schema = mongoose.Schema;

    //create schema for todo
    const TodoSchema = new Schema({
    action: {
    type: String,
    required: [true, 'The todo text field is required']
    }
    })

    //create model for todo
    const Todo = mongoose.model('todo', TodoSchema);

    module.exports = Todo;
    
  

![mern n5](https://user-images.githubusercontent.com/96151001/156606333-f31a39ef-d9c2-4ece-9a75-cd1b7ea76a2b.PNG)


Updated routes from the file *api.js* in ‘routes’ directory to make use of the new model
    
In Routes directory, open api.js with *vim api.js*, delete the code inside with *:%d* command and paste there code below into it then save and exit

    
    const express = require ('express');
    const router = express.Router();
    const Todo = require('../models/todo');

    router.get('/todos', (req, res, next) => {

    //this will return all the data, exposing only the id and action field to the client
    Todo.find({}, 'action')
    .then(data => res.json(data))
    .catch(next)
    });

    router.post('/todos', (req, res, next) => {
    if(req.body.action){
    Todo.create(req.body)
    .then(data => res.json(data))
    .catch(next)
    }else {
    res.json({
    error: "The input field is empty"
    })
    }
    });

    router.delete('/todos/:id', (req, res, next) => {
    Todo.findOneAndDelete({"_id": req.params.id})
    .then(data => res.json(data))
    .catch(next)
    })

    module.exports = router;



![mern n6](https://user-images.githubusercontent.com/96151001/156596071-ab07f4ef-e213-4bfe-865e-52ddecc34946.PNG)


#### MongoDB Database

Created a MongoDB database and collection inside mLab

In the *index.js* file, we specified *process.env* to access environment variables, but we have not yet created this file. So we need to do that now.

Created a file in your *Todo* directory and name it *.env.*

    $ touch .env
    $ vi .env
     

Add the connection string to access the database in it, just as below:

    DB = 'mongodb+srv://<username>:<password>@<network-address>/<dbname>?retryWrites=true&w=majority'

Ensure to update <username>, <password>, <network-address> and <database> according to the setup
    
![mern n7](https://user-images.githubusercontent.com/96151001/156596160-4b34e551-97ce-405c-be72-72441523a671.PNG)    
    
Updated the *index.js* to reflect the use of *.env* so that Node.js can connect to the database  
    
Simply delete existing content in the file, and update it with the entire code below.
    
To do that using vim, follow below steps
   
1. Open the file with vim index.js
2. Press esc
3. Type :
4. Type %d
5. Hit 'Enter' 

The entire content will be deleted, then,
    
6. Press i to enter the insert mode in vim 
7. Now, paste the entire code below in the file.
    
    
    
        const express = require('express');
        const bodyParser = require('body-parser');
        const mongoose = require('mongoose');
        const routes = require('./routes/api');
        const path = require('path');
        require('dotenv').config();

        const app = express();

        const port = process.env.PORT || 5000;

        //connect to the database
        mongoose.connect(process.env.DB, { useNewUrlParser: true, useUnifiedTopology: true })
        .then(() => console.log(`Database connected successfully`))
        .catch(err => console.log(err));

        //since mongoose promise is depreciated, we overide it with node's promise
        mongoose.Promise = global.Promise;

        app.use((req, res, next) => {
        res.header("Access-Control-Allow-Origin", "\*");
        res.header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept");
        next();
        });

        app.use(bodyParser.json());

        app.use('/api', routes);

        app.use((err, req, res, next) => {
        console.log(err);
        next();
        });

        app.listen(port, () => {
        console.log(`Server running on port ${port}`)
        });
    
    
    
![mern n8](https://user-images.githubusercontent.com/96151001/156596200-ec22c5a1-c504-47e7-9c87-a8da5db928b2.PNG)
    
    
Start your server using the command
    
    $ node index.js
    
I corrected the error code EADDRINUSE on server, using the procecedure described on the internet
    
    $ lsof -i tcp:5000
    $ kill -9 PID
    
![mern n9](https://user-images.githubusercontent.com/96151001/156596230-3bd4b8aa-cd16-4bd7-94c0-a3d83cdc22b0.PNG)    
    
    

