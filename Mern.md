# SIMPLE TO-DO APPLICATION ON MERN WEB STACK
### create an EC2 instance and add a Tag name to it corresponding to the Project you are working on for easy identification
### Download and launch Mobaxterm
### create a new SSH session with , ‘ubuntu’ as username and private key (.pem file)
### Implement a backend configuration
### Update and Upgrade Ubuntu
```bash
sudo apt update
sudo apt upgrade
```
### To get the location of Node.js software from Ubuntu repositories
```bash
curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -
```
### Install Node.js on the server
```bash
sudo apt-get install -y nodejs
```
### Verify the node installation and node package manager
```bash
node -v 
npm -v 
```
### Application code Setup
### Create a new directory for your To-Do project
```bash
mkdir Todo
```
verify the To-Do directory
```bash
ls
```
### change Directory to the To-Do Directory
```bash
cd Todo/
```
### Initialise your project, so that a new file named package.json will be created 
```bash
npm init
```
### Install ExpressJS
```bash
npm install express
```
### create a file index.js
```bash
touch index.js
```
### Install the dotenv module
``` bash
npm install dotenv
```
Open the index.js file
```bash
vim index.js
```
Paste the Code below
```bash
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

### Specify to use port 5000 inbound rule
### Start our server
```bash
node index.js
```
### Server should be running on port 5000
![express](https://user-images.githubusercontent.com/41236641/116780145-8340fc80-aa72-11eb-8136-144099f451b2.JPG)

### Creates three routes for our To-do Application
* Create a new task
* Display list of all tasks
* Delete a completed task

### create routes that will define various endpoints

``` bash
mkdir routes
```
### Change directory to routes folder
```bash
cd routes
```
### create a file api.js
```bash
touch api.js
```
### open the file api.js
```bash
vim api.js
```
### paste the code
```bash
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

### To Create a Model
### To create a Schema and a model, install mongoose which is a Node.js package that makes working with mongodb easier.
```bash
npm install mongoose
```

### Create a new folder with mkdir models

### Change directory into the newly created ‘models’ folder with cd models

### create a file and name it todo.js
```bash
touch todo.js
```

### mkdir models && cd models && touch todo.js
### Open the file created with vim todo.js then paste the code
```bash
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
### update our routes from the file api.js in ‘routes’ directory
#### open api.js and delete the code inside with :%d command
```bash
:%d
```

### After deleting the code, paste the code
```bash
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

### We make use of mlab as a database to store our data where mLab provides MongoDB database as a service solution (DBaaS)
* Allow access to the MongoDB database from anywhere (Not secure, but it is ideal for testing)
* Create a MongoDB database and collection inside mLab

### To Create a file in your Todo directory and name it .env
```bash
touch .env
vi .env
```

### Add the connection string to access the database in it and edit to fit the database 
```bash
DB = 'mongodb+srv://<username>:<password>@<network-address>/<dbname>?retryWrites=true&w=majority'
```
### To update <username>, <password>, <network-address> and <database> according to my current setup

### To update the index.js to reflect the use of .env so that Node.js can connect to the database.
### Simply delete existing content in the file, and update it with the entire code 

```bash
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
### Start your server
```bash
node index.js
```

### Testing Backend Code without Frontend using RESTful API
### We will use Postman to test our API
* create a POST request to the API
```
http://<PublicIP-or-PublicDNS>:5000/api/todos
```
* make sure your set header key Content-Type as application/json
* Create a GET request to your API. This request retrieves all existing records from out To-do application

``` 
http://<PublicIP-or-PublicDNS>:5000/api/todos
```

### The To-Do application will supports all three operations

* Display a list of tasks - HTTP GET request
* Add a new task to the list - HTTP POST request
* Delete an existing task from the list - HTTP DELETE request

### front-end Creation
```
npx create-react-app client
```
### Install concurrently. It is used to run more than one command simultaneously from the same terminal window.
```
npm install concurrently --save-dev
```

### Install nodemon
```
npm install nodemon --save-dev
```

### In Todo folder open the package.json file.
```
"scripts": {
"start": "node index.js",
"start-watch": "nodemon index.js",
"dev": "concurrently \"npm run start-watch\" \"cd client && npm start\""
},
```

### Configure Proxy in package.json

### Change directory to ‘client’
```
cd client
```
### Open the package.json file
```
vi package.json
```

### From the To-Do Directory
```
npm run dev
```

### Create React Components
```
cd client
```

### Inside your src folder create another folder called components
```
mkdir components
```

### Move into the components directory
```
cd components
```

### Inside ‘components’ directory create three files Input.js, ListTodo.js and Todo.js
```
touch Input.js ListTodo.js Todo.js
```

### Open Input.js file and paste the code
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
### Install  Axios, which is a Promise based HTTP client for the browser and node.js
```
npm install axios
```

### Navigate to the componenets directory and open the listtodo.js file
```
cd src/components
vi ListTodo.js
```
### Edit and paste the code in the ListTodo.js 

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

### Then in Todo.js file write the following code

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

### Ensure that you are in the src folder and run

```
vi App.js
```
### Paste the code
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

### In the src directory open the App.css and paste the code 

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

### In the src directory open the index.css
```
vim index.css
```

### Paste the code

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
### while in the To-do Directory, run to launch the To-do App
```
npm run dev
```
![Todo-list-project3](https://user-images.githubusercontent.com/41236641/116781328-16316500-aa7a-11eb-89a3-e278b19296e6.JPG)
