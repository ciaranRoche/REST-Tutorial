NOTE: THIS IS PART 2/4 FOR MY TUTORIAL THAT CAN BE FOUND ON DEV.TO

----
#<center>REST in 5 minutes!</center>
----

On a very high level, REST or REpresentational State Transfer is an architectural style that separates the client and a server. A client sends requests to /server and server responds while their concerns are separated, which means, a client has no idea how a server works, all it needs to know is that under given URL certain data is accessed. Server, on the other hand, does not care how many clients, or what clients are going to request the data from it! The client uses [HTTP methods](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods) to access the server, some of which are: GET, POST, PUT, DELETE and PATCH.

The tutorial below will give you a glimpse on how to build typical REST API. We will do that by writing a simple Node.js server! Let's give it a go! Finished version of the tutorial can be found in my [Github repo](https://github.com/MStokluska/REST-Tutorial).

###Requirements

- Node.js installed
- Visual Studio Code

###Let's get started 

- Open your Visual Studio Code (VSC) and hit  ctrl + &#96;  to open up the console
- Create a folder called `server` and navigate to it
- Type in your console:
```
[@localhost server]$ npm init
```
This will start npm package creator, feel free to fill in some details but if you want, you can just leave it empty by hitting "Enter" few times!

- Add `index.js` file and write the following code in:

```js
console.log("hello world")
```

- In your package.json add a start script and if you have an echo script, you can delete it:
```js
 "scripts": {
    "start": "node index.js"
  }
```

So it looks like this:

```js
{
  "name": "server",
  "version": "1.0.0",
  "main": "index.js",
  "license": "MIT",
  "scripts": {
    "start": "node index.js"
  }
}
```
- In your command line type
```
[@localhost server]$ npm start
```
And you should see the "hello world" output in your console! Npm looks through scripts specified in package.json and executes them. Package.json can contain a lot of settings and scripts for your projects, for example, license - MIT means it is "free software" and anyone can use it, there's a lot of useful stuff you can learn about package.json but let's not go into it now;) Without our "start" script we would have to execute:
```
[@localhost server]$ node index.js
```
If you are interested you can find more information about scripts [here](https://docs.npmjs.com/misc/scripts).

- Next, we are going to use Express which is a wrapper around the native Node.JS HTTP library which is responsible for handling HTTP requests. So let's add express dependencies to our project:
```
[@localhost server]$ npm install express
```
What this command does it simply adding express to our dependencies, one of the reasons why we need express installed is because it allows us to user GET, POST, PUT, DELETE and other HTPP methods in a very easy way!

- Once that's done, we can finally start up our very first express server!
Edit your index.js to look like this:

```js
const express = require('express');
const { users, tasks } = require('./db');

const app = express();

app.get('/_ping', (req, res) => {
  res.send('pong');
});

const port = 4000
app.listen(port, () => {
  console.log(`Server is listening on port ${port}`);
});
```
- const express = require('express') - we are simply importing express from express dependencies that we have downloaded
- const app = express() - we are creating an express app
- app.get('/_ping') - this is our very first used HTTP method - GET - which simply expects to GET something once the server is accessed from given URL
- res.send('pong') - once our app is accessed on '/_ping' let's send "pong" back to the client!
- const port - setting up a port variable to 4000 
- app.listen(port...) - using the set variable to enable the server to listen for requests on a given port

To summarize, we have set up a node.js server that is using express. Our server listens on ``localhost:4000/ ``, accepts requests from ``/_ping`` and responds with pong.

- To test it, in your console type in:
```
[@localhost server]$ npm start
```
- Next, open your browser and go to ``http://localhost:4000/_ping`` and enjoy the power of Node.js and express! Pong should be visible in your browser! ...or if you want to stick to console type:
```
[@localhost server]$ curl localhost:4000/_ping
```
- Our next step is to add some kind of data, for this purpose lets mock a database connection, in a typical situation our server would communicate with a database like postgres, mysql or mongo, but for the purpose of this tutorial, let's use a mock database. Create ``db.js`` file.

```js
const users = [
    {
      id: '1',
      firstName: 'Michael',
      lastName: 'Stone',
      title: 'Mr',
      email: 'michael@example.com',
      taskId: '20',
    },
    {
      id: '2',
      firstName: 'John',
      lastName: 'Barry',
      title: 'Mr',
      email: 'john@example.com',
      taskId: '21',
    },
    {
      id: '3',
      firstName: 'Mary',
      lastName: 'Savage',
      title: 'Mrs',
      email: 'mary@example.com',
      taskId: '22',
    },
  ];
  
  const tasks = [
    {
      id: '20',
      title: 'Restocking',
      description: 'please restock soft drinks section',
      status: 'to be completed',
      assignedTo: '1',
    },
    {
      id: '21',
      title: 'Cleaning',
      description: 'please clean your desk!',
      status: 'to be completed',
      assignedTo: '2',
    },
    {
      id: '22',
      title: 'Documentation update',
      description: 'please update our customers details',
      status: 'to be completed',
      assignedTo: '3',
    },
  ];
  
  module.exports.users = users;
  module.exports.tasks = tasks;
```
Our database is very simple, it contains few users and few tasks, each person has an assigned task and each task is assigned to a certain user.

- Next, as we don't want to be sending "pong" only anymore to our client but the actual data, add the following lines to our index:
```js
const { users, tasks } = require('./db');
```
- Which allows us to access tasks and users objects from db file, and also, add other routes to our index:
```js
app.get('/tasks', (req, res) => {
    res.json(tasks);
});

app.get('/users', (req, res) => {
    res.json(users);
});
```
- After adding new routes and restarting the server, visit localhost:4000/users or localhost:4000/tasks from your browser! Or simply,
in your console type:
```
[@localhost server]$ curl localhost:4000/users
[@localhost server]$ curl localhost:4000/tasks
```
- Let's imagine we would like to pull users details providing name, we can do it by passing in params, let's assume we know the first names of our users and this is how we would like to find them. Add the following GET method to our index.ts:
```js
app.get('/users/:userName', (req, res) => {
    res.send(users[users.findIndex(u => u.firstName === req.params.userName)]);
  });
```
and give it a try by using curl or visiting localhost:4000/users/Michael.
```
[@localhost server]$ curl localhost:4000/users/Michael
```
In your response you should have got details concerning Michael only, however, as this GET request takes in PARAM, you can just change the URL to any other name that is in our database to pull the record of the required user! Try this in your command line:
```
[@localhost server]$ curl localhost:4000/users/John
```
See how cool REST can be?! Now, let's go through another HTTP method - DELETE! To do that add another method to our server:
```js
app.delete('/users/delete/:userName', (req, res) => {
    const indexOfUserToBeDeleted = users.findIndex(u => u.firstName === req.params.userName);
    users.splice(indexOfUserToBeDeleted, 1);
    res.json(users);
});
``` 
- In your command line type:
```
[@localhost server]$ curl -X "DELETE" localhost:4000/users/delete/Michael
```
With DELETE and a very short JavaScript code we are able to delete an item from our array of users! 

One last method I would like to go through is POST, POST can be used to input new object into our array or update the existing one. Let's use it to add a new user object to our users array!
- Add a POST method to index.js:
```js
app.post('/users/addUser/:userId&:firstName&:lastName&:title&:email&:taskId', (req, res) => {
    const newUser = {
        userId: req.params.userId,
        firstName: req.params.firstName,
        lastName: req.params.lastName,
        title: req.params.title,
        email: req.params.email,
        taskId: req.params.taskId,
    };
    users.push(newUser);
    res.json(users);
})
```
- Restart the server and run following curl command:
```
[@localhost server]$ curl -X "POST" http://localhost:4000/users/addUser/9\&Anne\&OBrien\&Ms\&anne@example.com 
 \&8
```
- It returns all registered users already but to double check, you can run our curl command for getting all users also to see our database updated with an extra user!

- Lastly, our server accepts only requests from localhost, which even although it is a very simple server, makes it unusable to anyone except ourselves. To change it and allow requests from other sources we need to enable CORS support. I highly recommend reading more about CORS [here](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS). To do that, first, add dependencies to our project:
```
[@localhost server]$ npm install cors
```
and add the following lines to our index.ts
```js
const cors = require('cors')     <---------------------------------
const express = require('express');
const { users, tasks } = require('./db');
app.use(cors());             <---------------------------------
```
Your index.ts should look like this:
```js
const express = require('express');
const { users, tasks } = require('./db');
const cors = require('cors')

const app = express();

app.use(cors())

app.get('/_ping', (req, res) => {
    res.send('pong');
});

app.get('/tasks', (req, res) => {
    res.json(tasks);
});

app.get('/users', (req, res) => {
    res.json(users);
});

app.get('/users/:userName', (req, res) => {
    res.send(users[users.findIndex(u => u.firstName === req.params.userName)]);
});

app.delete('/users/delete/:userName', (req, res) => {
    const indexOfUserToBeDeleted = users.findIndex(u => u.firstName === req.params.userName);
    users.splice(indexOfUserToBeDeleted, 1);
    res.json(users);
});

app.post('/users/addUser/:userId&:firstName&:lastName&:title&:email&:taskId', (req, res) => {
    const newUser = {
        userId: req.params.userId,
        firstName: req.params.firstName,
        lastName: req.params.lastName,
        title: req.params.title,
        email: req.params.email,
        taskId: req.params.taskId,
    };
    users.push(newUser);
    res.json(users);
})

const port = 4000
app.listen(port, () => {
    console.log(`Server is listening on port ${port}`);
});
```
There you go! Your very first, very simple also, Node.js server that uses Express with REST endpoints! It is very easy, there's a lot more you can do with REST, but I guess my target here was to give you a brief idea of what REST is. What we did is similar to what I was exposed to on my adventure to become a software developer, which is still ongoing;) but it was also a huge eye-opener for me as for understanding client-server architecture! However, as I'm sure you can imagine now, you need to expose a different endpoint for each data you want to send, our project has only 2 entities, users and tasks, and it's very plain and simple - imagine how complex REST could be if you add another 10, 20 or more entities which can somehow interact with each other! Imagine that everything we did so far would need to be accessing the database through, for example, SQL statements, that would need to be placed in a different layer of our server to hide business logic! Imagine writing all those REST endpoints for your "dream" application! Not that easy anymore, isn't it?!


Let's now head straight into graphQL!
