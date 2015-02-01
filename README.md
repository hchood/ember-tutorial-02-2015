## Overview

In this and the next several sessions, we'll build a project management app with an Ember frontend and Rails API backend.  

![ER diagram](https://s3-us-west-2.amazonaws.com/2015-02-ember-tutorial/ember-tutorial-erd.png)

Tools we'll be using:

- ember-cli ([website](http://www.ember-cli.com/), [github](https://github.com/ember-cli/ember-cli))
- [rails-api](https://github.com/rails-api/rails-api)


## The plan

* Day 1:
  - Overview of Ember
  - Get setup
  - Backend:
    * Overview of APIs and JSON
    * Add project model, serializer, controller with index & show actions, routes
  - Frontend:
    * Overview of Ember
    * Add projects index page
    * On your own: Add projects show page

* Day 2:
  - Adding a project
  - Display tasks on project show page

* Day 3:
  - Adding a task
  - Marking a task complete

* Day 4:
  - Testing?  

  [Maybe we give them acceptance and controller tests for the projects CRUD actions.]


## Day 1

### Part I. Getting setup

#### A. Install stuff!

```no-highlight
# make sure Homebrew is up to date
brew update

# install node via Homebrew
# (this also installs npm, the node package manager,
# which we will use to install other tools we need)
brew install node

# install watchman
brew install watchman

# install bower
npm install -g bower

# install ember-cli
npm install -g ember-cli

# install rails-api gem
gem install rails-api
```

#### B. Generate the app

To start our app, we need to generate both a rails-api app and an ember app inside our app's top-level directory.

* Create the top-level app directory:

  ```no-highlight
  mkdir project-planner
  cd project-planner
  ```

* Create the ember app inside your app directory.  Rename it `frontend/`.

  ```no-highlight
  ember new project-planner --skip-git
  mv project-planner frontend
  ```

* Create the rails-api app using Postgres. Rename it `backend/`.

  ```no-highlight
  rails-api new project-planner -T --database=postgresql
  mv project-planner backend
  ```

* Initialize a git repo in your top-level app directory:

  ```no-highlight
  git init
  git add -A
  git commit -m "Initial commit."
  ```

* Create your database:

 ```no-highlight
 cd backend
 rake db:create
 ```

* Add `active_model_serializers` gem and testing and debugging gems and bundle. Commit your changes. **Make sure to specify version 0.8.3 for active_model_serializers if you're using Rails 4.2! (which you should be).**

  ```ruby
  # Gemfile

  gem 'active_model_serializers', '0.8.3'

  group :test do
    gem 'shoulda-matchers', require: false
  end

  group :development, :test do
    gem 'factory_girl_rails'
    gem 'faker'
    gem 'pry-rails'
    gem 'rspec-rails', '~> 3.0.0'
  end
  ```

  ```no-highlight
  cd backend
  bundle install
  git add -A
  git commit -m "Add active_model_serializers, debugging and testing gems."
  ```

Test out that your app is running by running the rails server and the ember server in separate terminal shells.

Start the rails server in one shell:

```no-highlight
cd backend
rails server
```

If you navigate to [localhost:3000](http://localhost:3000), you should see the standard Rails welcome page.

Start the ember server in the second shell:

```no-highlight
cd frontend
ember server --proxy http://localhost:3000
```

Your Ember app should now be running on port 4200, and will look for any data from our rails server [**explain this better**].  Navigating to [localhost:4200](http://localhost:4200), you should see the message "Welcome to Ember.js".

Hurray!  We have a Rails app that can serve up data to our Ember app.

Before we move on, let's tell our Ember app that it'll be using the ActiveModel adapter [**ERIC TALK ABOUT THIS**], and that all of its requests to our Rails app should be namespaced to `api/v1`.  This means that navigating to `localhost:4200/projects` will make a GET request to `localhost:3000/api/v1/projects` instead of `localhost:3000/projects`, for example.

Create a file called `frontend/app/adapters/application.js` and add the following:

```js
import DS from 'ember-data';

export default DS.ActiveModelAdapter.extend({
  namespace: 'api/v1'
});
```
> NOTE: You'll need to create the `frontend/app/adapters/` directory.

### Part II. `Projects` index & show pages - Building our Rails API

[Eric's sweet slides. Build index & show actions for projects.]

### Part III. Overview of Ember

[More Eric keynoting.]

### Part IV. `Projects` index & show pages - Adding the frontend

[Ditto.  Build index page with them, then have them do show page on their own.]
