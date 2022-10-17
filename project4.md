# MEAN STACK DEPLOYMENT TO UBUNTU IN AWS
Mean Stack is a combination of Mongodb (Document Database), Express (Backend Application Framework), Angular (Frontend Application Framework) and Nodejs (Javascript Runtime Environment).
In this project, I am going to implement a simple Book Register web form using MEAN stack.
## Prerequiste
- Launch an Ec2 instance on AWS

## Step 1: Installing Nodejs
- Update and Upgrade Ubuntu

  `sudo apt update`
  `sudo apt upgrade`
- Add certificates

  `sudo apt -y install curl dirmngr apt-transport-https lsb-release ca-certificates`
  
  `curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -`
- Install Nodejs

  `sudo apt install -y nodejs`

## Step 2: Installing Mongodb
MongoDB stores data in flexible, JSON-like documents. In this project, I will adding book records to MongoDB that contain book name, isbn number, author, and number of pages.
- Import public key

`sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6`
- Create a List
 
`echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list`
- Install Mongodb `sudo apt install -y mongodb`
- Start the server `sudo service mongodb start`
- Confirm the server is running `sudo systemctl status mongodb`
- Install NPM package

```
sudo apt-get install aptitude
sudo aptitude install npm
```
- Install body parser `sudo npm install body-parser`
- Create a book directory and move into the folder `mkdir Books && cd Books`
- Initialize npm project in the Book directory. `npm init`
- Add server.js file and paste the code below into it.

  ```
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
  ```
## Step 3: Installing Express and Setting up Routes to the server
Express will be used to pass book information to and from our MongoDB database and Mongoose package well be used to establish a schema for the database to store data of our book register.
- Start by installing Express and Mongoose

`sudo npm install express mongoose`
- In Books folder, create a folder names apps and move into the folder

`mkdir apps && cd apps`
- Create routes.js file and pase the code below into it

`vi routes.js`

```
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
```
- In the apps folder, create another folder named models and moved into it

`mkdir models && cd models`
- create a file named book.js and paste the code below into it

`vi book.js`

```
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
```

## Step 4: Accessing the routes with AngularJS
AngularJS provides a web framework for creating dynamic views in your web applications. In this project, AngularJS is sed to connect our web page with Express and perform actions on our book register.
- In the Books folder, create a new folder named public and move into it `mkdir public && cd public`
- Add a file name script.js and paste the code below into it. `vim script.js

```
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
```
- Create an index.html file inside the public folder and paste the code below into it 

`vim index.html`
- Change the directory back up to Books `cd ..`
- Start the server by running `node server.js`
- The server is now up and running, can connect it via port 3300 and can also launch a separate Putty or SSH console to test what curl command returns locally.

`curl -s http://localhost:3300`
- This can also be accessed from the internet but one need to open TCP port 3300 in your AWS Web Console for your EC2 Instance.

![inbound](https://user-images.githubusercontent.com/26335055/196117793-fd8d9eb4-0feb-406e-a211-6e40bfaf28d5.png)

- The Web Book Register Application will look like in browser

![book](https://user-images.githubusercontent.com/26335055/196117856-5e6a01f8-7226-46e4-b766-96ac3411afd6.png)


