## Overview

In this and the next several sessions, we'll build a project management app with an Ember frontend and Rails API backend.  

![ER diagram](https://s3-us-west-2.amazonaws.com/2015-02-ember-tutorial/ember-tutorial-erd.png)

Tools we'll be using:

- ember-cli ([website](http://www.ember-cli.com/), [github](https://github.com/ember-cli/ember-cli))
- [rails-api](https://github.com/rails-api/rails-api)
- [ActiveModel serializers](https://github.com/rails-api/active_model_serializers)

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

### Part I. Install stuff!

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

### Part II. Generate the Rails app & build projects API

#### A. Generate the Rails app

To start our app, we need to generate both a rails-api app and an ember app inside our app's top-level directory.  Since we'll work on the Rails side first, let's start out by adding a new rails-api app:

* Create the top-level app directory:

  ```no-highlight
  mkdir project-planner
  cd project-planner
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

* Add `active_model_serializers` gem and testing and debugging gems, run RSpec generator, and bundle. Commit your changes. **Make sure to specify version 0.8.3 for active_model_serializers if you're using Rails 4.2! (which you should be).**

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
  bundle install
  rails g rspec:install
  git add -A
  git commit -m "Add active_model_serializers, debugging and testing gems."
  ```
  > NOTE: Make sure you're inside of your `backend/` directory when running `bundle`, `rails server`, and other rails commands.

Start your `rails server` and navigate to [localhost:3000](http://localhost:3000). You should see the standard Rails welcome page.

#### B. Building our Rails API for projects index & show pages

[Eric's sweet slides. Build index & show actions for projects.]

### Part III. Overview of Ember

[More Eric keynoting.]

### Part IV. Generate the Ember app & build projects index & show pages

Now that we have a Rails API to serve up projects information, we can start building out our frontend.

#### A. Generate the Ember app

Make sure you're in your top-level app directory.

  * Create the Ember app inside your app directory.  Rename it `frontend/`.

    ```no-highlight
    ember new project-planner --skip-git
    mv project-planner frontend
    ```

  * Test out that your app is running by running the rails server and the ember server in separate terminal shells.

    Start the Rails server in one shell:

  ```no-highlight
  cd backend
  rails server
  ```

  Start the Ember server in the second shell:

  ```no-highlight
  cd frontend
  ember server --proxy http://localhost:3000
  ```

  Your Ember app should now be running on port 4200, and will send any requests for data to our Rails server.  Navigating to [localhost:4200](http://localhost:4200), you should see the message "Welcome to Ember.js".


Before we move on, let's tell our Ember app that it'll be using the ActiveModel adapter [**ERIC TALK ABOUT THIS**], and that all of its requests to our Rails app should be namespaced to `api/v1`.  This means that navigating to `localhost:4200/projects` will make a GET request to `localhost:3000/api/v1/projects` instead of `localhost:3000/projects`, for example.

  * Create a file called `frontend/app/adapters/application.js` and add the following:

  ```js
  import DS from 'ember-data';

  export default DS.ActiveModelAdapter.extend({
    namespace: 'api/v1'
  });
  ```
  > NOTE: You'll need to create the `frontend/app/adapters/` directory.

#### B. Add projects index page

##### i. Modify router

When we navigate to [localhost:4200/projects](http://localhost:4200/projects), the first thing Ember will do is to look for a corresponding entry in the router.  Let's add that:

```js
// frontend/app/router.js

Router.map(function() {
  // add these lines
  this.resource('projects', function() {
    // generates an entry for projects index for you
  });
});
```
> This is kind of like adding `resources :projects, only: :index` in your Rails app's `routes.rb` file.

##### ii. Add `projects/index` route

Once it finds the right entry in the router, it will look for a route matching that entry.  (Yes, this is confusing. In Ember, there's a router and there are routes and they are different things. The job of the router is to map browser requests to routes in our app.  The primary job of each route is to grab the data from the Rails API that we will need for the `/projects` page.)

Let's add that route:

```no-highlight
$ ember g route projects/index
version: 0.1.7
installing
  create app/routes/projects/index.js
  create app/templates/projects/index.hbs
installing
  create tests/unit/routes/projects/index-test.js
```

As you can see, this command generated a route at `frontend/app/routes/projects/index.js`, as well as a template that will be rendered when we navigate to `localhost:4200/projects` and a test file taht we won't worry about right now.

For our route to retrieve the projects data we need, we'll need to add a **model hook**:

```js
// frontend/app/routes/projects/index.js

import Ember from 'ember';

export default Ember.Route.extend({
  // add these lines
  model: function() {
    return this.store.find('project');
  }
});
```

##### iii. Add `project` model

For the model hook in our route to work, we need a `project` model in our Ember app that mirrors the `Project` model in our Rails app:

```no-highlight
$ ember g model project
version: 0.1.7
installing
  create app/models/project.js
installing
  create tests/unit/models/project-test.js
```

This generated a basic project model without any attributes. We need to set the right attributes on our project model so that we can access them in our template later.  

```js
// frontend/app/models/project.js

import DS from 'ember-data';

export default DS.Model.extend({
  // add these two lines:
  name: DS.attr('string'),
  description: DS.attr('string')
});
```

##### iv. Modify the template

Almost done. All we need to do now is modify the template that was created when we ran `ember g route projects/index` so that it's displaying the correct information.

Currently, it looks like this:

```js
// frontend/app/templates/projects/index.hbs

{{outlet}}
```

Let's replace that `{{outlet}}` with the following:

```js
// frontend/app/templates/projects/index.hbs

<h3>All Projects</h3>

<ul>
  {{#each project in model}}
    <li>{{project.name}}</li>
  {{/each}}
</ul>
```

> This looks kind of like `erb`, right? `model` is essentially an array of project objects, and we're iterating over that array using the Handlebars `each` helper to create list items for each project. Handlebars provides other helpers, like `link-to`, that we'll use later.

Navigate to [localhost:4200/projects](http://localhost:4200/projects) and you should see a nice list of the projects in our database.


#### C. Add the Ember inspector Chrome plugin

Chrome has an awesome extension called Ember Inspector that will be super useful for debugging your ember apps.  Add it to your browser.  Now if you open your JavaScript console using `cmd + alt + J`, you should see a tab called "Ember":

![JavaScript console](https://s3-us-west-2.amazonaws.com/2015-02-ember-tutorial/ember-inspector.png)

There's a whole bunch of cool stuff in here, but for now just check to see that your projects have been loaded by selecting the "Data" option on the left and clicking on "projects" under "Model Types". You should see a list of all your projects on the right.  Clicking on "Routes" will show you all of the routes in your app.  It's kind of like Ember's equivalent of `rake routes`. You should see an entry for the route you just added, `projects.index`.

#### D. Add project show page

To add a project show page, we have to go through more or less the same steps we did for the index page, except we don't need to generate the project model.

##### i. Add links to project show pages

Let's start by adding links to the project show pages on our project index page using Handlebars' `link-to` helper:

```js
// frontend/app/templates/projects/index.hbs


<h3>All Projects</h3>

<ul>
  {{#each project in model}}
    <li>{{link-to project.name 'projects.show' project}}</li>
  {{/each}}
</ul>
```

##### ii. Add router entry

```js
// frontend/app/router.js

Router.map(function() {
  this.resource('projects', function(){
    // add this line
    this.route('show', {path: ':id'});
  });
});
```
##### iii. Add `projects/show` route

```no-highlight
$ ember g route projects/show
version: 0.1.7
installing
  create app/routes/projects/show.js
  create app/templates/projects/show.hbs
installing
  create tests/unit/routes/projects/show-test.js
```

Now we need to add a model hook to our route.  This one will be slightly different from our index routes's model hook, in that it will need to know the `id` of the specific project. We can specify the project's id by passing `params` to our model function and passing a second argument, `params.id`, to our `find` function call:

```js
// frontend/app/routes/projects/show.js

import Ember from 'ember';

export default Ember.Route.extend({
  // add these lines
  model: function(params) {
    return this.store.find('project', params.id);
  }
});
```

##### iv. Modify the template

Update the `show.hbs` template to display the project's name and description. You can access the project model's attributes by simply calling the attribute name inside of Handlebars moustaches:

```hs
{{name}}
```

```js
// frontend/app/templates/projects/show.hbs

<h3>{{name}}</h3>

<div id="description">
  Description: {{description}}
</div>
```

Test it out in your browser and make sure you can navigate from the projects index page to a project's show page.
