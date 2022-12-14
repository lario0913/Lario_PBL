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

## Step 4: Setting up Mongodb Database
A database where we will store our data. For this we will make use of mLab. mLab provides MongoDB database as a service solution (DBaaS), so to make life easy, you will need to sign up for a shared clusters free account, which is ideal for our use case. Sign up here. Follow the sign up process, select AWS as the cloud provider, and choose a region near you.
- Complete a get started checklist after signing up successfully
- Allow access to the MongoDB database from anywhere
- In the index.js file, we specified process.env to access environment variables, but we have not yet created this file. So we need to do that now. Create a file in your Todo directory and name it .env.

 `touch .env`
 `vi .env`
- Add the connection string to access the database in it, just as below but ensure to update <username>, <password>, <network-address> and <database> according to your setup
  
  `DB = 'mongodb+srv://<username>:<password>@<network-address>/<dbname>?retryWrites=true&w=majority'`
- update the index.js to reflect the use of .env so that Node.js can connect to the database. Simply delete existing content in the file, and update it with the entire code below.
    - Open the file with vim index.js
    - Press esc
    - Type :%d and hit ENTER
    - Press i to enter insert mode and paste the code below
     ```
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
     ```
- Start your server and you should get a "Database Connected Successfully" message
  
  `node index.js`
  
  ![dbsuccess](https://user-images.githubusercontent.com/26335055/195783831-aa252b28-8f80-454b-b970-7590a930ba35.png)
  
- Testing Backend Code without Frontend using RESTful API
  - Install Postman 
  - create a POST request to the API http://<PublicIP-or-PublicDNS>:5000/api/todos. This request sends a new task to our To-Do list so the application could store it in the database and make sure your set header key Content-Type as application/json
  
  ![header](https://user-images.githubusercontent.com/26335055/195785844-0d1d2de8-93d4-4fac-9c50-46028d4c6dfa.png)

  - Create a POST request as in the image below
  ![postman1](https://user-images.githubusercontent.com/26335055/195786860-bf939275-461c-4fb5-b38f-13e701313e88.png)

  - Create a GET request as in the image below
  ![postman2](https://user-images.githubusercontent.com/26335055/195786833-ce39b06f-aec1-4389-ace7-02b1b177de8d.png)

  - Create a DELETE request as in the image below. To delete a task – you need to send its ID as a part of DELETE request.
  ![postmandelete](https://user-images.githubusercontent.com/26335055/195786939-1d80103c-76ae-4460-a0a3-36106b1576eb.png)

## Step 5: Frontend Creation
  - Navigate to the Tool directory adnd run the command below which will create a client folder in the Todo directory.
  
  `npx create-react-app client`
  - Install concurrently and nodemon
  
  `npm install concurrently --save-dev`
  `npm install nodemon --save-dev`
  - In Todo folder open the package.json file. Change the highlighted part of the below screenshot and replace with the code below.
  
  ```
  "scripts": {
  "start": "node index.js",
  "start-watch": "nodemon index.js",
  "dev": "concurrently \"npm run start-watch\" \"cd client && npm start\""
  },
  ```
  
  ![shell](https://user-images.githubusercontent.com/26335055/195845824-7b4b2a69-b10f-4ce0-8552-95fa604e3da2.png)
  - Configure Proxy in the client's package.json file
    - Change directory to ‘client’ `cd client`
    - Open the package.json file `vim package.json`
    - Add the key value pair in the package.json file `"proxy": "http://localhost:5000"`.
    - Run your app `npm run dev` and access at `http://<public id>:3000`
  
 - Creating the React Components
    - From Todo move to src directory
  
    `cd client/src`
    - Create a components folder and move into it
  
    `mkdir components`
    `cd components`
    - Inside ‘components’ directory create three files Input.js, ListTodo.js and Todo.js
  
    `touch Input.js ListTodo.js Todo.js`
    - Open Input.js file and paste the code the follows into it
  
    `Vim Input.js`
    ```
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
    ```
    - Move back into client folder
  
    `cd ../..`
    - Install Axios
    
    `npm install axios`
    - Go to components folder
    `cd src/components`
  
    - After that open your ListTodo.js and paste the code that follows
    `vim ListTodo.js`
  
    ```
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
    ```
  
    - Also, open Todo.js and paste the following code

    ```
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
    ```
  
    - Move to src folder and open App.js
    
    ```
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
    ```
  
    - In the src directory open the App.css 
  
    `vim App.css`
    ```
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
    ```
    - Open index.css and paste the code
    ```
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
    ```
  - Go to Todo directory
  
  `cd ../..`
  - run the code below and access on http://publicid:3000
  
  `npm run dev`
