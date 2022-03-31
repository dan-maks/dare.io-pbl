
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

Create a MongoDB database and collection inside mLab. mLab provides MongoDB database as a service solution (DBaaS), so to make life easy, you will need to sign up for a shared clusters free account, which is ideal for our use case. Follow the sign up process, select AWS as the cloud provider, and choose a region near you. Add network access to the database cluster,by allowing access from anywhere.

![mongo db 1](https://user-images.githubusercontent.com/96151001/161025656-62afc7b3-7d07-40ce-82cd-4c3b1ee9a65a.png)

![mongo db 2](https://user-images.githubusercontent.com/96151001/161025741-0547da81-5bad-46dc-866c-ef562b1a0647.png)

![mongo db 3](https://user-images.githubusercontent.com/96151001/161025804-a6a0f4ee-6271-4262-976c-38d12c8d2fa7.png)


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
    
    
#### Testing Backend Code without Frontend using RESTful API
    
So far we have written backend part of our To-Do application, and configured a database, but we do not have a frontend UI yet. We need ReactJS code to achieve that. But during development, we will need a way to test our code using RESTfulL API. Therefore, we will need to make use of some API development client to test our code.

In this project, we will use Postman to test our API. Install Postman app on the local machine from the internet.
    
You should test all the API endpoints and make sure they are working. For the endpoints that require body, you should send JSON back with the necessary fields since it’s what we setup in our code.
    
Now open your Postman, create a POST request to the API http://<PublicIP-or-PublicDNS>:5000/api/todos. This request sends a new task to our To-Do list so the application could store it in the database.

Note: make sure your set header key Content-Type as application/json.
    
![post 1](https://user-images.githubusercontent.com/96151001/161028921-d0ef896e-5a3d-4309-9b32-973e2ed787de.png)
    
    
Create a GET request to your API on http://<PublicIP-or-PublicDNS>:5000/api/todos. This request retrieves all existing records from out To-do application (backend requests these records from the database and sends it us back as a response to GET request).

![post 2](https://user-images.githubusercontent.com/96151001/161028967-485fd1d2-25a6-4dff-a370-ecea17dc431a.png)

    
 We have successfully created our Backend, now let go create the Frontend.
    
## Step 2 – Frontend creation
    
Since we are done with the functionality we want from our backend and API, it is time to create a user interface for a Web client (browser) to interact with the application via API. To start out with the frontend of the To-do app, we will use the create-react-app command to scaffold our app.
    
In the same root directory as your backend code, which is the Todo directory, run:

    $ npx create-react-app client
    
![mern c1](https://user-images.githubusercontent.com/96151001/161030761-fc0430b6-40b2-4fa9-b8a9-b0c563a29421.png)    
    
![mern c2](https://user-images.githubusercontent.com/96151001/161030827-bbf1e671-8c60-4e97-855a-6dd5bda772ce.png)    
    
This will create a new folder in your Todo directory called client, where you will add all the react code. 
    
#### Running a React App
    
Before testing the react app, there are some dependencies that need to be installed.
    
1. Install concurrently. It is used to run more than one command simultaneously from the same terminal window.
       
    $ npm install concurrently --save-dev
    
![mern c3](https://user-images.githubusercontent.com/96151001/161030860-57fe2dff-bc80-48d8-bf1c-ba9551eb3d63.png)

2. Install nodemon. It is used to run and monitor the server. If there is any change in the server code, nodemon will restart it automatically and load the new changes.
    
    $ npm install nodemon --save-dev     

![mern c4](https://user-images.githubusercontent.com/96151001/161030889-a8fbf718-45c0-4a93-9061-a72b05196ec0.png)
    
3. In Todo folder open the package.json file. Change the highlighted part of the below screenshot and replace with the code below.
    
![MERN EDIT](https://user-images.githubusercontent.com/96151001/161038691-e0cecf0e-e775-4865-9d30-fd9c0781372f.PNG)    

    "scripts": {
    "start": "node index.js",
    "start-watch": "nodemon index.js",
    "dev": "concurrently \"npm run start-watch\" \"cd client && npm start\""
    },
    
![mern 32](https://user-images.githubusercontent.com/96151001/161037883-acc28c43-5ad6-4a89-9def-7ca251ed41dd.PNG)
    
#### Configure Proxy in *package.json*
    
1. Change directory to ‘client’
    
    $ cd client
    
![mern c6](https://user-images.githubusercontent.com/96151001/161030922-dba95511-c93f-4be5-a36d-fc41dc8a67b5.png)    
    
2. Open the package.json file
    
    $ vi package.json
    
3. Add the key value pair in the package.json file "proxy": "http://localhost:5000".    
    
![mern c7](https://user-images.githubusercontent.com/96151001/161031050-c512853b-c3a6-4a7c-abc0-e2d53fe5c697.png)    
    
    
The whole purpose of adding the proxy configuration in number 3 above is to make it possible to access the application directly from the browser by simply calling the server url like http://localhost:5000 rather than always including the entire path like http://localhost:5000/api/todos

Now, ensure you are inside the Todo directory, and simply do:
    
    $ npm run start
    
![mern c8](https://user-images.githubusercontent.com/96151001/161031089-f2735f22-7920-44c2-9e41-169d5478bb12.png)    
    
Your app should open and start running on localhost:3000    
    
![mern c9](https://user-images.githubusercontent.com/96151001/161031117-830beffa-5b51-47d6-8fef-c862acc5099c.png)    
    
    
Important note: In order to be able to access the application from the Internet you have to open TCP port 3000 on EC2 by adding a new Security Group rule. You already know how to do it.
    
##### Creating your React Components
    
One of the advantages of react is that it makes use of components, which are reusable and also makes code modular. For our Todo app, there will be two stateful components and one stateless component.
    
From your Todo directory run    
    
    $ cd client   
    
move to the src directory

    $ cd src
    
Inside your src folder create another folder called components

    $ mkdir components
    
![mern c11](https://user-images.githubusercontent.com/96151001/161031326-31fba623-f722-414e-9809-b61796632ea8.png)    
    
Move into the components directory with

    $ cd components
    
Inside ‘components’ directory create three files Input.js, ListTodo.js and Todo.js.

    $ touch Input.js ListTodo.js Todo.js    
    
![mern c12](https://user-images.githubusercontent.com/96151001/161031366-2a83ef3a-1d5d-4478-a246-4f77551ae093.png)
    
Open Input.js file
    
    $ vi Input.js
    
Copy and paste the following
    
    import React, { Component } from 'react';
    import axios from 'axios';

    class Input extends Component {

    state = {
    action: ""
    }

    addTodo = () => {
    const task = {action: this.state.action}

        if(task.action && task.action.length > 0){
          axios.post('/api/todos', task)
            .then(res => {
              if(res.data){
                this.props.getTodos();
                this.setState({action: ""})
              }
            })
            .catch(err => console.log(err))
        }else {
          console.log('input field required')
        }

    }

    handleChange = (e) => {
    this.setState({
    action: e.target.value
    })
    }

    render() {
    let { action } = this.state;
    return (
    <div>
    <input type="text" onChange={this.handleChange} value={action} />
    <button onClick={this.addTodo}>add todo</button>
    </div>
    )
    }
    }

    export default Input
  
    
![mern c13](https://user-images.githubusercontent.com/96151001/161031427-181e3d9f-7e1f-4c5e-b3fc-7b6d2849b21c.png)
       
To make use of Axios, which is a Promise based HTTP client for the browser and node.js, you need to cd into your client from your terminal and run yarn add axios or npm install axios.  

Move to the src folder
    
    $ cd ..
    
Move to clients folder
    
    $ cd ..
    
Install Axios
    
    $ npm install axios
    
![mern c14](https://user-images.githubusercontent.com/96151001/161031472-a7cdb6cb-4a14-4097-8be1-04cfc3038d7d.png)    
    
Go to ‘components’ directory

    $ cd src/components 
    
After that open your ListTodo.js

    $ vi ListTodo.js
    
![mern c17](https://user-images.githubusercontent.com/96151001/161031554-35860785-4e4e-48c3-8e23-cf506229771d.png)    
    
    import React from 'react';

    const ListTodo = ({ todos, deleteTodo }) => {

    return (
    <ul>
    {
    todos &&
    todos.length > 0 ?
    (
    todos.map(todo => {
    return (
    <li key={todo._id} onClick={() => deleteTodo(todo._id)}>{todo.action}</li>
    )
    })
    )
    :
    (
    <li>No todo(s) left</li>
    )
    }
    </ul>
    )
    }

    export default ListTodo   
  
    
![mern c16](https://user-images.githubusercontent.com/96151001/161031518-3bfc5d01-57c8-45d1-a850-93f8bf5e85ec.png)    
    
open the Todo.js file 
    
    $ vi Todo.js
    
write the following code in the Todo,js file
    
    
    import React, {Component} from 'react';
    import axios from 'axios';

    import Input from './Input';
    import ListTodo from './ListTodo';

    class Todo extends Component {

    state = {
    todos: []
    }

    componentDidMount(){
    this.getTodos();
    }

    getTodos = () => {
    axios.get('/api/todos')
    .then(res => {
    if(res.data){
    this.setState({
    todos: res.data
    })
    }
    })
    .catch(err => console.log(err))
    }

    deleteTodo = (id) => {

        axios.delete(`/api/todos/${id}`)
          .then(res => {
            if(res.data){
              this.getTodos()
            }
          })
          .catch(err => console.log(err))

    }

    render() {
    let { todos } = this.state;

        return(
          <div>
            <h1>My Todo(s)</h1>
            <Input getTodos={this.getTodos}/>
            <ListTodo todos={todos} deleteTodo={this.deleteTodo}/>
          </div>
        )

    }
    }

    export default Todo;

    

![mern c18](https://user-images.githubusercontent.com/96151001/161031604-70d9e51e-e7fa-458d-bf22-672b14091f84.png)
    
    
We need to make little adjustment to our react code. Delete the logo and adjust our App.js to look like this.

Move to the src folder
    
    $ cd ..
        
Make sure that you are in the src folder and run
    
    $ vi App.js
    
Copy and paste the code below into it
    
    
    import React from 'react';

    import Todo from './components/Todo';
    import './App.css';

    const App = () => {
    return (
    <div className="App">
    <Todo />
    </div>
    );
    }

    export default App;
    
    
![mern c19](https://user-images.githubusercontent.com/96151001/161058054-fdb6c70d-749a-43bd-9f01-c56710d66e98.png)
    
    
After pasting, exit the editor.

In the src directory open the App.css
    
    $ vi App.css
    
![mern c21](https://user-images.githubusercontent.com/96151001/161058448-052050de-3714-41e5-b384-7538e4c2a027.png)
        
Then paste the following code into App.css:
    
    
    .App {
    text-align: center;
    font-size: calc(10px + 2vmin);
    width: 60%;
    margin-left: auto;
    margin-right: auto;
    }

    input {
    height: 40px;
    width: 50%;
    border: none;
    border-bottom: 2px #101113 solid;
    background: none;
    font-size: 1.5rem;
    color: #787a80;
    }

    input:focus {
    outline: none;
    }

    button {
    width: 25%;
    height: 45px;
    border: none;
    margin-left: 10px;
    font-size: 25px;
    background: #101113;
    border-radius: 5px;
    color: #787a80;
    cursor: pointer;
    }

    button:focus {
    outline: none;
    }

    ul {
    list-style: none;
    text-align: left;
    padding: 15px;
    background: #171a1f;
    border-radius: 5px;
    }

    li {
    padding: 15px;
    font-size: 1.5rem;
    margin-bottom: 15px;
    background: #282c34;
    border-radius: 5px;
    overflow-wrap: break-word;
    cursor: pointer;
    }

    @media only screen and (min-width: 300px) {
    .App {
    width: 80%;
    }

    input {
    width: 100%
    }

    button {
    width: 100%;
    margin-top: 15px;
    margin-left: 0;
    }
    }

    @media only screen and (min-width: 640px) {
    .App {
    width: 60%;
    }

    input {
    width: 50%;
    }
  
    button {
    width: 30%;
    margin-left: 10px;
    margin-top: 0;
    }
    }
    
    

![mern c20](https://user-images.githubusercontent.com/96151001/161057646-b3e3f0eb-8469-4c8e-9a6d-3dc28c9d402b.png)
    
    
Exit
    
In the src directory open the index.css
    
    $ vim index.css
    
Copy and paste the code below:
    
    
    body {
    margin: 0;
    padding: 0;
    font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", "Roboto", "Oxygen",
    "Ubuntu", "Cantarell", "Fira Sans", "Droid Sans", "Helvetica Neue",
    sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    box-sizing: border-box;
    background-color: #282c34;
    color: #787a80;
    }

    code {
    font-family: source-code-pro, Menlo, Monaco, Consolas, "Courier New",
    monospace;
    }
 
    
![mern c22](https://user-images.githubusercontent.com/96151001/161056983-f2ad007a-2b2d-4337-8f16-5b5b56196a4d.png)
    
    
Go to the *Todo* directory
    
    $ cd ../..
    
![mern c23](https://user-images.githubusercontent.com/96151001/161056798-675bec9c-a885-4e02-934a-4a19aa57c7bb.png)    
    
    $ npm run dev
    
![mern c25](https://user-images.githubusercontent.com/96151001/161056289-b7af48a4-2a30-41b2-8535-2afeae53c79d.png) 
    
    
To-Do app ready and fully functional with the functionalities discussed earlier: creating a task, deleting a task and viewing all your tasks.
    
![mern c24](https://user-images.githubusercontent.com/96151001/161056565-58764f33-1a2b-4f53-9713-2a5a671c8183.png)
    

![mern compt](https://user-images.githubusercontent.com/96151001/161054743-ccd780a1-5483-4a57-999f-7a531842f274.png)
    
    
### Conclusion
    
Created a simple To-Do and deployed it to MERN stack. Wrote a frontend application using React.js that communicates with a backend application written using Expressjs. Also created a Mongodb backend for storing tasks in a database.

    
    
