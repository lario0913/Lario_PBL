# MERN STACK IMPLEMENTATION

## Step 0: Prequisites
- Open an AWS account and Launch an EC2 instance

## Step 1: Backend Configuration
- Update Ubuntu
  `sudo apt update`
- Upgrade Ubuntu
  `sudo apt upgrade`
- Get the location of Node.js software from Ubuntu repositories.
  `curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -`
- Install Node js
  `sudo apt-get install -y nodejs`
- Verify node installation `node -v`
- It's now time for code set up. Create a new directory for the project and change the to the directory
  `mkdir Todo`
  `cd Todo`
 - Initialize the new project with npm init
  `npm init`
  
![npminit](https://user-images.githubusercontent.com/26335055/195695336-1132339e-7ac1-44b5-8404-7e1c7028fae9.png)

## Install Express
- Install Express `npm install express`
- Create an Index file `mkdir index.js`
- Install dotenv module. `npm install dotenv`
- Open the index file with vim and paste the code that follows. `vim index.js`
  ```
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
    
  ```
- Use :w to save in vim and use :qa to exit vim
- Open your terminal in the same directory as your index.js and open with node. It should display SERVER RUNNING ON PORT 5000. 
    `node index.js`
    
  ![noderunnig](https://user-images.githubusercontent.com/26335055/195701922-47bb7b14-a661-4669-a7a4-9a00d7ab8110.png)
  
- Create an inbound rule on EC2 to open TCP port 5000
 
![inbound](https://user-images.githubusercontent.com/26335055/195703185-b8f520b3-0a94-4624-8a4c-3dc11d31585c.png)

- Open up the browser and try to access the server’s Public IP or Public DNS name followed by port 5000:
 `http://<PublicIP-or-PublicDNS>:5000`
 
![welcomeToExpress](https://user-images.githubusercontent.com/26335055/195703595-3f489f65-4dc0-4c7d-8f15-d694f5740028.png)

- There are three actions that our To-Do application needs to be able to do:
    - Create a new task
    - Display list of all tasks
    - Delete a completed task

Each task will be associated with some particular endpoint and will use different standard HTTP request methods: POST, GET, DELETE. For each task, there is need to create routes that will define various endpoints that the To-do app will depend on. So create a folder routes and change to the directory
  `mkdir routes`
  `cd routes`
- Create api.js file and open with vim editor

  `touch api.js`
  `vim api.js`
  
- Enter Insert mode, copy the code below and paste in the vim editor after which you save the file and exit the editor
  ```
    const express = require ('express');
    const router = express.Router();

    router.get('/todos', (req, res, next) => {

    });

    router.post('/todos', (req, res, next) => {

    });

    router.delete('/todos/:id', (req, res, next) => {

    })

    module.exports = router;
  ```
  
  ## Step 3: Setting up the Models
A model is at the heart of JavaScript based applications, and it is what makes it interactive. We will also use models to define the database schema . This is important so that we will be able to define the fields stored in each Mongodb document. In essence, the Schema is a blueprint of how the database will be constructed, including other data fields that may not be required to be stored in the database. These are known as virtual properties
To create a Schema and a model, install mongoose which is a Node.js package that makes working with mongodb easier: 

`npm install mongoose`
- Create a new folder named models, change directory into the newly created ‘models’ folder with and create a file and name it todo.js.

`mkdir models && cd models && touch todo.js` 
- Open the file created with vim todo.js then paste the code below in the file, save and exit vim editor

```
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
```
- Now we need to update our routes from the file api.js in ‘routes’ directory to make use of the new model. In Routes directory, open api.js with vim api.js, delete the code inside with :%d command and paste there code below into it then save and exit.

```
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
```
