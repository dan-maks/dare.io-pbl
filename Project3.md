
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
    
        
#### MODELS     

A model is at the heart of JavaScript based applications, and it is what makes it interactive. We will also use models to define the database schema. Schema is a blueprint of how the database will be constructed, including other data fields that may not be required to be stored in the database. These are known as virtual properties
       
To create a Schema and a model, install mongoose which is a Node.js package that makes working with mongodb easier.

Change directory back Todo folder with *cd ..* and install Mongoose using the command below

    $ npm install mongoose
    
Create a new folder models using the command below
    $ mkdir models
    
Changed directory into the newly created ‘models’ folder with the below command

    $ cd models
    
Inside the models folder, create a file and name it todo.js

    $ touch todo.js
    
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


#### MongoDB Database

Created a MongoDB database and collection inside mLab

In the *index.js* file, we specified *process.env* to access environment variables, but we have not yet created this file. So we need to do that now.

Created a file in your *Todo* directory and name it *.env.*

    $ touch .env
    $ vi .env
     

Add the connection string to access the database in it, just as below:

    DB = 'mongodb+srv://<username>:<password>@<network-address>/<dbname>?retryWrites=true&w=majority'

Ensure to update <username>, <password>, <network-address> and <database> according to the setup
    
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
    
    
    
Start your server using the command
    
    $ node index.js
    
    

