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

## Installing Mongodb
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

