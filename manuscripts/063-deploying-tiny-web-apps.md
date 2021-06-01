- Deploying Tiny Web Apps
- Step 1 - Download and Install Heroku CLI
  
  The first step is [downloading and installing the Heroku CLI](https://devcenter.heroku.com/articles/heroku-cli). Then we can login into Heroku from the command line with:
  
     $ heroku login
  
  Once we're logged in, we can create a new app on Heroku by running: `Heroku create {app-name}`. Heroku apps are in a global namespace, therefore you need to choose a unique name for your application.
  
     $ heroku create fastchart --buildpack heroku/python
  
  Create a `Procfile` in our project directory. The `Procfile` is used to declare the commands run by the app on Heroku.
  
  The Procfile defines processes using the following format `{process-type}: {command}`. Since our application contains a web server, the process type is `web`.
  
     web: uvicorn main:app --port 7000
  
  Heroku has different [service plans](https://devcenter.heroku.com/articles/heroku-postgres-plans#hobby-tier). In this tutorial, we will use the hobby-dev plan, which is free and serves the purpose we need it for.
  
  Make sure you commit and push your source code. Then run this from the top level of the tree:
  
     $ heroku git:remote -a fastchart
     $ git subtree push --prefix {subfolder} heroku master
  
  Your app is now live at [https://fastchart.herokuapp.com](https://app-name.herokuapp.com/).
- Step 2 - Troubleshooting
  
  Make sure you run this from your app folder:
  
     $ heroku local
  
  Check out the Stackoverflow answer to [How to run uvicorn in Heroku?](https://stackoverflow.com/questions/59391560/how-to-run-uvicorn-in-heroku)