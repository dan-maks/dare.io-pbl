# PROJECT 4 - MEAN STACK DEPLOYMENT TO UBUNTU IN AWS

# Step 0 - Preparing prerequisites

In order to carry out the project, we set up a virtual server with Ubuntu Server OS using the already created AWS account console we've been using in the previous projects.

# Task 

In this project, we want to implement a simple Book Register web form using MEAN stack.

# Step 1 - Install NodeJs

Node.js is a JavaScript runtime built on Chrome’s V8 JavaScript engine. Node.js is used in this tutorial to set up the Express routes and AngularJS controllers.

Update Ubuntu 

    $ sudo apt update

Upgrade ubuntu 
   
    $ sudo apt upgrade
    
![mean 1](https://user-images.githubusercontent.com/96151001/161763907-072c5693-63a4-4101-8846-a393b6967600.PNG)

Add certificate 
 
    $ sudo apt -y install curl dirmngr apt-transport-https lsb-release ca-certificates

    $ curl -sL https://deb.nodesource.com/setup_16.x | sudo -E bash -
    
![node ins](https://user-images.githubusercontent.com/96151001/161765774-5cd0b15b-fea6-41f5-9f52-8138e3e351b2.PNG)
       
Install NodeJS

    $ sudo apt install -y nodejs
    
![mern n1](https://user-images.githubusercontent.com/96151001/161765277-1cec49f2-486c-409b-b921-6d4c4698f6f0.PNG)


# Step 2: Install MongoDB

MongoDB stores data in flexible, JSON-like documents. Fields in a database can vary from document to document and data structure can be changed over time. For our example application, we are adding book records to MongoDB that contain book name, isbn number, author, and number of pages.

    $ sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6

    $ echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list
    
![mean 4](https://user-images.githubusercontent.com/96151001/161763511-59a77d94-7b29-4e52-ab53-25c4558889d7.PNG)    

Install MongoDB

    $ sudo apt install -y mongodb
    
Start The server

    $ sudo service mongodb start
    
Verify that the server is up and running
 
    $ sudo systemctl status mongodb
    
![mean 6](https://user-images.githubusercontent.com/96151001/161763103-7f36115f-14e6-4942-a260-a7ba8e0c8e1e.PNG)    
    
Install npm – Node package manager.

    $ sudo apt install -y npm
    
Install body-parser package

We need ‘body-parser’ package to help us process JSON files passed in requests to the server.

    $ sudo npm install body-parser
    
![mean 9](https://user-images.githubusercontent.com/96151001/161762553-02849361-2df5-48bf-b7e4-9db36d6f2684.PNG)

Create a folder named ‘Books’

    $ mkdir Books && cd Books
    
![mean 10](https://user-images.githubusercontent.com/96151001/161762105-04b3ebaa-d8da-4a5b-8e13-88ec9ad54bb5.PNG)    
    
In the Books directory, Initialize npm project

    $ npm init   

![mean 11](https://user-images.githubusercontent.com/96151001/161761692-ae80ca49-5889-4633-97ee-bcda777f2967.PNG)

Add a file to it named server.js

    $ vi server.js  
    
Copy and paste the web server code below into the server.js file

    var express = require('express');
    var bodyParser = require('body-parser');
    var app = express();
    app.use(express.static(__dirname + '/public'));
    app.use(bodyParser.json());
    require('./apps/routes')(app);
    app.set('port', 3300);
    app.listen(app.get('port'), function() {
    console.log('Server up: http://localhost:' + app.get('port'));
    });
    
    
![mean 12](https://user-images.githubusercontent.com/96151001/161761391-7f5e9708-b589-4389-b74f-93698671d8ab.PNG)


# Step 3: Install Express and set up routes to the server

Express is a minimal and flexible Node.js web application framework that provides features for web and mobile applications. We will use Express in to pass book information to and from our MongoDB database.

We also will use Mongoose package which provides a straight-forward, schema-based solution to model your application data. We will use Mongoose to establish a schema for the database to store data of our book register.

    $ sudo npm install express mongoose
    
![mean 13](https://user-images.githubusercontent.com/96151001/161761089-dbb45638-25dc-4422-8f72-e15c910ac4cd.PNG)    
    
In ‘Books’ folder, create a folder named apps

    $ mkdir apps && cd apps
    
![mean 14](https://user-images.githubusercontent.com/96151001/161760867-8ac82c95-1a06-4b12-bf6c-e4ed7b69f256.PNG)    
    
Create a file named routes.js

![mean 15](https://user-images.githubusercontent.com/96151001/161760548-778cfd19-ba80-4256-8994-cc85d72f3c42.PNG)

    $ vi routes.js
    
Copy and paste the code below into routes.js
    
    var Book = require('./models/book');
    module.exports = function(app) {
      app.get('/book', function(req, res) {
        Book.find({}, function(err, result) {
          if ( err ) throw err;
          res.json(result);
        });
      }); 
      app.post('/book', function(req, res) {
        var book = new Book( {
          name:req.body.name,
          isbn:req.body.isbn,
          author:req.body.author,
          pages:req.body.pages
        });
        book.save(function(err, result) {
          if ( err ) throw err;
          res.json( {
            message:"Successfully added book",
            book:result
          });
        });
      });
      app.delete("/book/:isbn", function(req, res) {
        Book.findOneAndRemove(req.query, function(err, result) {
          if ( err ) throw err;
          res.json( {
            message: "Successfully deleted the book",
            book: result
          });
        });
      });
      var path = require('path');
      app.get('*', function(req, res) {
        res.sendfile(path.join(__dirname + '/public', 'index.html'));
      });
    };



![mean 16](https://user-images.githubusercontent.com/96151001/161760354-007462d1-0b32-4150-88d5-a676bedf83f9.PNG)

In the ‘apps’ folder, create a folder named models

    $ mkdir models && cd models
    
![mean 17](https://user-images.githubusercontent.com/96151001/161760064-863103c2-aa0e-4bae-846f-f03b48e64e27.PNG)    
    
Create a file named book.js

    $ vi book.js
    
Copy and paste the code below into ‘book.js’
    
    var mongoose = require('mongoose');
    var dbHost = 'mongodb://localhost:27017/test';
    mongoose.connect(dbHost);
    mongoose.connection;
    mongoose.set('debug', true);
    var bookSchema = mongoose.Schema( {
      name: String,
      isbn: {type: String, index: true},
      author: String,
      pages: Number
    });
    var Book = mongoose.model('Book', bookSchema);
    module.exports = mongoose.model('Book', bookSchema);


![mean 18](https://user-images.githubusercontent.com/96151001/161759833-36c87be7-0e0a-4acb-9d38-aa8980981e0b.PNG)


# Step 4 – Access the routes with AngularJS

AngularJS provides a web framework for creating dynamic views in your web applications. In this tutorial, we use AngularJS to connect our web page with Express and perform actions on our book register.

Change the directory back to ‘Books’

    $ cd ../..
    
Create a folder named public

    $ mkdir public && cd public
    
![mean 19](https://user-images.githubusercontent.com/96151001/161758588-08bb16eb-6574-4dec-8864-134f22550fa9.PNG)    

Add a file named script.js

    $ vi script.js
        
Copy and paste the Code below (controller configuration defined) into the script.js file. 

    var app = angular.module('myApp', []);
    app.controller('myCtrl', function($scope, $http) {
      $http( {
        method: 'GET',
        url: '/book'
      }).then(function successCallback(response) {
        $scope.books = response.data;
      }, function errorCallback(response) {
    console.log('Error: ' + response);
      });
      $scope.del_book = function(book) {
        $http( {
          method: 'DELETE',
          url: '/book/:isbn',
          params: {'isbn': book.isbn}
        }).then(function successCallback(response) {
          console.log(response);
        }, function errorCallback(response) {
          console.log('Error: ' + response);
        });
      };
      $scope.add_book = function() {
        var body = '{ "name": "' + $scope.Name + 
        '", "isbn": "' + $scope.Isbn +
        '", "author": "' + $scope.Author + 
        '", "pages": "' + $scope.Pages + '" }';
        $http({
          method: 'POST',
          url: '/book',
          data: body
        }).then(function successCallback(response) {
          console.log(response);
        }, function errorCallback(response) {
          console.log('Error: ' + response);
        });
      };
    });
    


![mean 20](https://user-images.githubusercontent.com/96151001/161759250-5e9a5251-d659-4ba3-b882-62df881914e3.PNG)

In public folder, create a file named index.html;

    $ vi index.html
    
![mean 21](https://user-images.githubusercontent.com/96151001/161759528-b700b10a-3807-41d4-899e-600c478742bb.PNG)
       
Copy and paste the code below into index.html file.

    <!doctype html>
    <html ng-app="myApp" ng-controller="myCtrl">
      <head>
        <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.4/angular.min.js"></script>
        <script src="script.js"></script>
      </head>
      <body>
        <div>
          <table>
            <tr>
              <td>Name:</td>
              <td><input type="text" ng-model="Name"></td>
            </tr>
            <tr>
              <td>Isbn:</td>
              <td><input type="text" ng-model="Isbn"></td>
            </tr>
            <tr>
              <td>Author:</td>
              <td><input type="text" ng-model="Author"></td>
            </tr>
            <tr>
              <td>Pages:</td>
              <td><input type="number" ng-model="Pages"></td>
            </tr>
          </table>
          <button ng-click="add_book()">Add</button>
        </div>
        <hr>
        <div>
          <table>
            <tr>
              <th>Name</th>
              <th>Isbn</th>
              <th>Author</th>
              <th>Pages</th>

            </tr>
            <tr ng-repeat="book in books">
              <td>{{book.name}}</td>
              <td>{{book.isbn}}</td>
              <td>{{book.author}}</td>
              <td>{{book.pages}}</td>

              <td><input type="button" value="Delete" data-ng-click="del_book(book)"></td>
            </tr>
          </table>
        </div>
      </body>
    </html>
    
    
    
![mean 22](https://user-images.githubusercontent.com/96151001/161757133-e16ce51f-8eed-40c5-b215-a1182878c28f.PNG)

Change the directory back up to Books

    $ cd ..

Start the server by running this command:

    $ node server.js
    
![mean 23](https://user-images.githubusercontent.com/96151001/161756705-fd3399d1-cec4-4cec-af61-adfd24577c59.PNG)
    
The server is now up and running, we can connect it via port 3300. We can launch a separate Putty or SSH console to test what curl command returns locally.

    $ curl -s http://localhost:3300       
    
It shall return an HTML page, it is hardly readable in the CLI, but we can also try and access it from the Internet.

![mean 24](https://user-images.githubusercontent.com/96151001/161756222-79111ac0-2ba4-4e57-b579-5c2fc7a36574.PNG)

For this – you need to open TCP port 3300 in your AWS Web Console for your EC2 Instance.   

Now we can access our Book Register web application from the Internet with a browser using Public IP address or Public DNS name.

Quick reminder how to get your server’s Public IP and public DNS name:

1. You can find it in your AWS web console in EC2 details
2. Run curl -s http://169.254.169.254/latest/meta-data/public-ipv4 for Public IP address or curl -s http://169.254.169.254/latest/meta-data/public-hostname for Public DNS name.

![mean 25](https://user-images.githubusercontent.com/96151001/161755994-65a08c0a-7c5e-4535-8eaf-14e837a7e5df.PNG)

This is how our Web Book Register Application will look like in browser:

![mean 26](https://user-images.githubusercontent.com/96151001/161755793-9997dbad-11bb-42bd-bc9a-2d341e6cea4c.PNG)





