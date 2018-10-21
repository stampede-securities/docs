# Cloud Hosting

This tutorial will talk you through how to host backend APIs and frontend projects on Heroku and mlabs, as well as how to use heroku pipelines for creating staging (dev) and production versions of your projects.

## Table of Contents

- [Heroku Apps](#heroku-apps)
- [Heroku Pipelines](#heroku-pipelines)
- [Hosting on Mlab](#hosting-on-mlab)
- [Deploying an API](#deploying-an-api)

## Heroku Apps

Heroku apps are essentially virtual machines that require minimal set up and configuration by the user. We use Heroku apps for most of our hosting, because it's very easy to deploy code onto them, they have good travis integration and the service is free. Because Heroku apps are free, they do power down if the site hasn't received any requests for some period of time. This means that first time you visit a webapp or hit an API endpoint that is hosted on Heroku, it might take a few seconds to get a response.
(NOTE: when we ship apps off to clients, we move the heroku application to the paid plan, where they are stable and behave the same as if we hosted them on a different platform like AWS or Google Cloud).

### Creating Heroku Apps

Go to https://dashboard.heroku.com/ and log in with the stampede credentials. Once you're on the dashboard, press "new" in the top left corner. Choose "Create New App" and give the app a name.

- Our naming convention is projectName-appType-level, where projectName is the name of the project, appType is either "api", "webapp" or "frontend" and level is "staging" or "dev" for testing apps and "prod" for production apps (we give prod app links to clients for testing). Putting this all together, you would get names such as "farmnivorous-api-staging" or "lullaby-webapp-prod".

Make sure the region is the US and then press create app. Congratulations! You have sucessfully created a Heroku app!

### Deploying code to a Heroku App

Before deploying your code on Heroku, make sure your app is ready for hosting. The primary thing that you have to check is to make sure that your not assigning a port to your app yourself. When Heroku deploys an app, it assigns it a port that you can access through the following environment variable: `process.env.PORT`. For example if you're using Express to create a server (which is what we use for our APIs), then you would have to make sure that you get the port from the environment variable. It's also good to fall back onto a default port for running the app locally. As such your server should be initalized using a line similar to this:

```
var server = app.listen(process.env.PORT || 5000)
```

There are two ways we use to deploy code onto heroku apps: Through the Heroku CLI and through Travis. Deploying through Travis is our preferred way, but sometimes a manual push is required (for example, if Travis is down).

#### Deploying Through the Heroku CLI

1. Download and install the Heroku CLI: https://devcenter.heroku.com/articles/heroku-cli .
2. Log into the Heroku CLI using `heroku login` and the stampede credentials.
3. Clone your project's repository onto your computer and `cd` into it.
4. Make sure you're on the master branch. Only code pushed from the master branch gets deployed on Heroku.
5. Add Heroku as a remote to your project repository `heroku git:remote -a yourHerokuApp`, replacing `yourHerokuApp` with the name of the heroku app you just created.
6. Commit your code and push it to Heroku using `git push heroku master`.

- **Note:** Pushing to Heroku does not also push your code to github.

Your code should now get deployed on the app.

#### Deploying Through Travis

Travis can be configured to deploy the newest code to your app whenever the master branch of your repo or another specified branch gets updated. To do this, you need to add the following to your `.travis.yml` file in the repo.

```
deploy:
    provider: heroku
    api_key: <Paste the stampede heroku API key here
    app: <Paste the name of your heroku app here>
```

If you want to deploy code from a specific branch, you can modify the lines to look like so:

```
deploy:
  - provider: heroku
    api_key: secret-key
    app: farmnivorus-webapp-staging
  - provider: heroku
    api_key: secret-key
    app: farmnivorous-prod
    on:
      branch: production
```

This configuration will deploy all changes on the master branch to the farmnivorus-webapp-staging Heroku app and all changes on the production branch to the farmnivorous-prod Heroku app.

Once you've modified the `.travis.yml` file and you push the code, travis should start deploying code automatically.

### Configuring a Heroku App

There are 2 mains aspects of a Heroku app that you might want to configure: Config variables and buildpacks.

#### Config Variables

Config variables are Heroku's version of Environment variables. to set them go to your app's dashboard on Heroku, then go to the Settings pane and under the config variables section press "Reveal Config Vars". After that it's pretty intuitive to add new config variables. You can access them in your application by using `process.env.VAR_NAME`. For example if you set a config variable called `SECRET_API_KEY`, then you can access it in your app through `process.env.SECRET_API_KEY`. In general it is good to put secrets such as API keys and environment dependent values (such as API or database urls) in config variables.

#### Buildpacks

Heroku Buildpacks tell Heroku how exactly to build your app. Most of the time you don't have to change the buildpack yourself, beacause Heroku can figure out which one to use automatically. However on React projects, you need to set it manually. To do this, got to your app's dashboard on Heroku, then go to the Settings pane and under the buildpacks section press "add buildpack". Then choose your buildpack. For React apps use "https://github.com/mars/create-react-app-buildpack.git"

## Heroku Pipelines

Heroku pipelines allow you to tie multiple apps together into one app pipeline, where code starts off being deployed on a staging app and then gets manually promoted to a production app if the build is deemed stable. The motivation for doing this is for having a stable build for clients to test on. The main idea is that you will have a staging build, that is hooked to Travis, so that can be used to test all the newest changes on the app. You also have a production app. Once a week, a stable staging build is promoted to the production app for the client to test on. This way the team can keep on working on the project and deploying changes to the staging app (potentiall breaking it) without affecting the client's testing efforts.

### Creating a Pipeline

To create a Heroku pipeline, go the the main Heroku dashboard, press new in the top left corner and choose "Create new pipeline". Name your pipeline (the naming scheme should be similar to the app naming scheme, except it won't have the level indicator. For example: "Farmnivorous-webapp"), leave the other fields how they are and press "Create pipeline". You should now see a screen with a staging area and a production area. Both areas should have an "add app" button. Using those add a staging app and a production app to your pipeline. Once you've added both apps, you should get a button labeled "promote to production" underneath the staging app. By pressing it and going through the confirmation modal, you can promote the build on the staging app and deploy it onto the production app.

- **Note:** Promoting an app to production does not rebuild the app. It takes the prebuilt slug and copies it straight onto the new app. This means that if your app relies on config variables during the build process and those config variables vary between your staging and production app, you cannot use a pipeline. React Apps fall under this category, because they get config variables compiled into them during the build process. Read more about how to get around this in the [Deploying a REACT Project](#deploying-a-react-project) section.

## Hosting on Mlab

Mlab is a service for hosting Mongo databases easily and for free (up to 500mb of data). Mlab offers an easy for connecting to it and gives a nice way to look at the data in your database.

### Creating a Database

Go to https://mlab.com and create an account. Once you've created an account and are logged in you should arrive at the Mlab dashboard. To create a new database press "Create New" under the "MongoDB Deployments" section. Choose any cloud provider (I usually default to AWS) and choose the sandbox plan. Aftewards press continue on the bottom right.Then choose an appropriate region (closest to the client or Harvard) and press continue again. Give your DB a name and once again press continue. Finally review your options and press "submit order".

- **Note:** Make sure the total price says "Free".

You have now created a database! It should appear in your Mlab dashboard. Press on it and you will be taken to the database dashboard.

### Connecting to a Database

Before you connect to your database, you need to create a user for it. In the Database dashboard, go to the Users section and press "Add Database User". Fill in an username and password and make sure the "Make read-only" is unchecked. Press create to create the user.

Once the user has been created you can connect to your database in your app through mongoose using the following line:

```
mongoose.connect(DATABASE_URI, {server: {socketOptions: {keepAlive: 120}}})
```

where `DATABASE_URI` is the database URI given to you on the Mlab database dashboard.

![Database URI on Mlab](https://github.com/hsadev/library/blob/master/standards/photos/mlabs.png)

Replacing the `<dbuser>` and `<dbpassword>` with the credentials of the database user you just created.

- **Note:** It is generally a good idea to have the Database URI stored as an environment/config variable, because it keeps the database user credentials safe and it allows you to have the staging and production versions of your app connect to different databases (we don't want clients to see random test data that the engineers have been testing with).

### Inspecting the data

To inspect the data in your database, go to the collections section.

## Deploying an API

Now that you know how to deploy everything, let's walk through the process of properly deploying an API.

1. Go to Mlab and create 2 databases: A staging DB and a production DB.
2. Edit your app, so that it gets the database URI from an environment variable.
3. Create 2 Heroku apps: a staging API app and a production API app.
4. Create a new Heroku pipeline and add the 2 apps that you just created as the staging and production apps.
5. Set the config variables in your 2 Heroku apps. Make sure to set the database URI variable in the staging app equal to the URI of the staging database and the database URI variable in the production app equal to the URI of the production database.
6. Update the `.travis.yml` file in your repo to automatically deploy to the staging app.
7. Push the updated `.travis.yml` file.
8. Promote the staging app to a production app.
9. Make sure your frontend applications now target the staging or production API apps instead of a local instance.

Congratulations! You have sucessfully deployed your API.
