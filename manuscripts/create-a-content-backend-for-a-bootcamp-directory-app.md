- Create a Content Backend for a Bootcamp Directory App
- Create a Content Backend for a Bootcamp Directory App
  
  **Introduction**
  
  Using a Headless CMS gives you the flexibility to develop a front end using any frameworks or tools. For example, you could develop the front end as a web page, mobile app, or even a Command Line Interface ["CLI"].
  
  And if you're a serious web developer, you'll want to keep your business and data logic (headless CMS) separate from your user interface (front end).
  
  In this tutorial you'll be using Node.js to create a backend for a Bootcamp Directory App. You'll simulate the front end using HTTP API requests from the Postman app. The Headless CMS can listen and process these requests and send data & status back to the front end.
- Prerequisites
  
  * DotEnv
  * Express
  * Node
  * Nodemon
  * [Postman](https://www.getpostman.com/) Windows application to understand how HTTP API works.
- Step 1 - Creating a New Node.js Project 
  
  Ensure that *Node.js* has been installed, the next thing to do is create a new project.
  
  (1) Create a folder anywhere on your PC for this project, e.g. "*d:\Theme-source\07bootcampapi*" ["root folder"]
  
  (2) Open a terminal in the above folder and type this command to create the "*package.json*" file:
  
     $ npm init
  
  You can enter the following (default) values for each of these entries as below:
  
     package name: (07bootcampapi)
     version: (1.0.0)
     description: Content Backend for a Bootcamp Directory App
     entry point: server.js
     test command:
     git repository:
     keywords:
     author: Dennis Lee
     license: MIT
  
  These entries will be stored in the *package.json* file.
  
     07bootcampapi/              <-- Root of your project
       |- package.json           <-- Node.js project entries
  
  (3) Edit the file *package.json*. Find the **scripts** tag and replace it with the following:
  
     "scripts": {
       "start": "SET ENV_MODE=production&& nodemon server.js",
       "dev": "nodemon server.js"
     },
  
  (4) Create a subfolder *config*, and create a new file *config.env* within that subfolder with the following text;
  
     ENV_MODE=development
     ENV_PORT=5000
  
  Your project folder should look like this:
  
     07bootcampapi/
       |- package.json
       +- config/                <-- Holds any config files
          |- config.env          <-- Environment variables file
- Step 2 - Installing Dependencies
  
  **Development Dependencies**
  
  Ensure that you're in the root folder of the project.
  
  (1) First, we are going to install development dependencies (these will not be deployed to production).
  
     $ npm install -D nodemon
  
  **Local Dependencies**
  
  (2) Second, we are going to install local dependencies (these will be available to project only).
  
     $ npm install express dotenv
- **Express** : HTTP API Framework
- **DotEnv** : Variables from a *.env* file can be loaded into** process.env**.
  
  Note: Node.js **require** doesn't look in the folder where global modules are installed unless you set NODE_PATH.
  
  (3) Create a new file *.gitignore* and enter the following text:
  
     config/
     node_modules/
  
  Your project folder should look like this:
  
     07bootcampapi/
       |- .gitignore             <-- Files and folders entered here are ignored by Git
       |- package.json
       +- config/
          |- config.env
       +- node_modules/          <-- Development dependencies
- Step 3 - Creating a New Server.js
  
  (1) The pseudo code for a new *server.js* file is as follows:
- Load libraries for **express**, **dotenv**
- Load environment variables from *config/config.env*
- Create a new object **objServer** that listens on **ENV_PORT**
  
     const dotenv = require('dotenv');
     const express = require('express');
     
     dotenv.config({
       path: 'config/config.env'
     });
     
     const objServer = express();
     const intPort = process.env.ENV_PORT || 5000; // if undefined
     
     // Enter router and controller code here
     
     objServer.listen(
       intPort,
       console.log(`Server running in ${process.env.ENV_MODE} mode on port ${intPort}`)
     );
  
  Note: If you have a string that contains variables, you have to use **backstop** (`) instead of **quote** (') to wrap the string.
  
  Your project folder should look like this:
  
     07bootcampapi/
       |- .gitignore
       |- package.json
       |- server.js              <-- Main app
       +- config/
          |- config.env
       +- node_modules/
- Step 4 - Defining a Route Structure
  
  For our project, we have a database with the following tables:
- **bootcamp**
- **course**
- **review**
- **user**
  
  We want to build a backend with a feature to Create, Read, Update, Delete ["CRUD"] entries in each of our tables.
  
  In other words, we have to implement the HTTP API methods **POST**, **GET**, **PUT** (or **PLACE**), and **DELETE** for these routes (corresponding to each table above):
- **/api/v1/bootcamp**
- **/api/v1/course**
- **/api/v1/review**
  
  **- /api/v1/user**
  
  For example, the Read feature for table **user** is implemented as **/api/v1/user/GET**.
  
  By using a versioning, i.e. **v1**, in our routes, we can maintain backward compatibility for existing client frontends.
  
  In addition to the above routes, we have a route for authentication as follows:
  
  **- /api/v1/auth**
- Step 5 - Evolution of Route Management
  
  The evolution of route management takes us on a journey from a single file to an automated route management.
  
  **Single File**
  
  The simplest route management is by writing the router and controller code in the main file, i.e. *server.js*.
  
  For example, in table **user**, we have the route **/api/v1/user** and we have to implement the HTTP API methods **POST**, **GET**, **PUT** (or **PLACE**) and **DELETE **as follows:
  
     // GET method
     objServer.get('/api/v1/user', (req, res) => {
       res.status(200).json({ msg: 'Show all users' });
     });
     // GET method with id
     objServer.get('/api/v1/user/:id', (req, res) => {
       res.status(200).json({ msg: `Show user with ${req.params.id}` });
     });
     // POST method
     objServer.post('/api/v1/user', (req, res) => {
       res.status(200).json({ msg: 'Create new user' });
     });
     // PUT method with id
     objServer.put('/api/v1/user:id', (req, res) => {
       res.status(200).json({ msg: `Update user with ${req.params.id}` });
     });
     // DELETE method with id
     objServer.delete('/api/v1/user:id', (req, res) => {
       res.status(200).json({ msg: `Delete user with ${req.params.id}` });
     });
  
  **Route and Controller File**
  
  In this route management, we separate the code for routes and controllers from the main file.
  
  For example, we have a file *user.js* within a *routes/* subfolder. In this file we have the code as follows:
  
     const express = require('express');
     const objRouter = express.Router();
     
     // GET method
     objRouter.get('/', (req, res) => {
       res.status(200).json({ msg: 'Show all users' });
     });
     // GET method with id
     objRouter.get('/:id', (req, res) => {
       res.status(200).json({ msg: `Show user with ${req.params.id}` });
     });
     // POST method
     objRouter.post('/', (req, res) => {
       res.status(200).json({ msg: 'Create new user' });
     });
     // PUT method with id
     objRouter.put('/:id', (req, res) => {
       res.status(200).json({ msg: `Update user with ${req.params.id}` });
     });
     // DELETE method with id
     objRouter.delete('/:id', (req, res) => {
       res.status(200).json({ msg: `Delete user with ${req.params.id}` });
     });
     
     module.exports = objRouter;
  
  where we substitute the object **objServer** (as this is undefined) with the object **objRouter**.
  
  Also, we no longer need to include the route path **/api/v1/user** in our route file.
  
  In our main file **server.js**, we need to specify where the route file **user.js** is located and then associate the route path **/api/v1/user** with the file as follows:
  
     // specify route file
     const objUser = require('./routes/user.js');
     
     // associate route path to file
     objServer.use('/api/v1/user', objUser);
  
  **Controller File**
  
  In this route management, we separate the code for controllers from the route file.
  
  For example, we have a file *userController.js* within a controllers*/* subfolder. In this file we have the code as follows:
  
     // GET method
     exports.getUsers = (req, res, next) => {
       res.status(200).json({ msg: 'Show all users' });
     }
     // GET method with id
     exports.getUser = (req, res, next) => {
       res.status(200).json({ msg: `Show user with ${req.params.id}` });
     }
     // POST method
     exports.postUser = (req, res, next) => {
       res.status(200).json({ msg: 'Create new user' });
     }
     // PUT method with id
     exports.putUser = (req, res, next) => {
       res.status(200).json({ msg: `Update user with ${req.params.id}` });
     }
     // DELETE method with id
     exports.deleteUser = (req, res, next) => {
       res.status(200).json({ msg: `Delete user with ${req.params.id}` });
     }
  
  In our route file **user.js**, we need to specify where the controller file **userController.js** is located and then associate the route path with each of the methods:
  
     const express = require('express');
     const objRouter = express.Router();
     
     // specify route file
     const {
       getUsers,
       getUser,
       postUser,
       putUser,
       deleteUser
     } = require('../controllers/userController.js');
     
     // associate route path to each method
     objRouter
       .route('/')
       .get(getUsers)
       .post(postUser);
     
     objRouter
       .route('/:id')
       .get(getUser)
       .put(putUser)
       .delete(deleteUser);
     
     module.exports = objRouter;
  
  **Automated Route Management**
  
  Finally, in this route management, we could use the module **express-mvc-router **to automate our route management. 
  
  This **Node.js** module simplifies route management by automatically loading controller files from the default subfolder *controllers*, and generating routes based on controller methods.
  
  Read the [documentation](https://github.com/cesarrdzs/express-mvc-router) for the above **Node.js** module.
- Step 6 - Asynchronous Legacy Callback
  
  A callback is basically a function that is passed by reference as a parameter to another function.
  
  For example,
  
     setTimeout( function() { }, 1000);
  
  where the first parameter is a callback, declared as an in-line function, while the second parameter is just an integer, i.e. 1000 milliseconds.
  
  You may sometimes see a shorthand code for a function declaration:
  
     setTimeout( () => {}, 1000);
  
  When do you use the **callback** function?
  
  Assuming that you have two functions, let's say post() and get(), and the get() should always be after the post() function, in order to display the latest content.
  
     function post(new, callback) { 
       // insert new
       callback();
     }
  
     function get() { // get all }
  
  Our main should have a single execution line as follows:
  
     // main
     post(new, get);
  
  Using this method, we need to write asynchronous code in functions that take precedence, e.g. post().
- Step 7 - Asynchronous Promise .. Then
  
  A promise is basically a class that has several constructors, the most commonly used is:
  
     new Promise( resolve, reject );
  
  where resolve and reject are both keywords, and also callback functions, such that you should call the resolve() method after your function has completed successfully, otherwise call the reject() function if there is any error.
  
  For example, our previous function post() should instead return a Promise object:
  
     function post(new) {
       return new Promise(resolve, reject) => {
         // insert new
         if( !error) resolve();
         else reject('Error: Something went wrong');
       }
     }
  
  Also, our main should have a single execution line, appended with the then() method:
  
     // main
     post(new).then(get);
  
  Using this method, we need to write asynchronous code in functions that take precedence, e.g. post().
- Step 8 - Asynchronous Async .. Await
  
  The latest Javascript ES7 introduces the keywords "**async .. await**", which still uses promises, but in a more elegant way.
  
  For example, both our previous function post() and get() doesn't require any asynchronous code:
  
     function post(new) { // insert new }
     function get() { // get all }
  
  We create a new wrapper function do(), with the **async** keyword, as follows:
  
     async function do() {
       await post(new);
       get();
     }
  
  Our main should then have a single execution line as follows:
  
     do();
  
  Using this method, we do not need to write asynchronous code in functions that take precedence, e.g. post(), instead we write asynchronous code in a higher level function, e.g. do().
- References
  
  [Traversy Media (2019), Node.js API Masterclass With Express & MongoDB, Udemy](https://www.udemy.com/course/nodejs-api-masterclass/)
  
  [Traversy Media (2018), Async JS Crash Course - Callbacks, Promises, Async Await, YouTube](https://youtu.be/PoRJizFvM7s)