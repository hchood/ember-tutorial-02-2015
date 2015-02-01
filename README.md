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
  - `/projects` index page
  - On your own: `/projects/:id` show page

  [should we have API pre-built? Maybe for project but not tasks?]

* Day 2:
  - Adding a project
  - Using ActiveModel serializers
    * build out tasks API
  - Display tasks on project show page

* Day 3:
  - Adding a task
  - Marking a task complete

* Day 4:
  - Testing?  

  [Maybe we give them acceptance and controller tests for the projects CRUD actions.]


## Day 1

### Overview of Ember

[Eric's sweet diagrams / Keynote slides]

### Getting setup

#### A. Install stuff!

```bash
# make sure Homebrew is up to date
brew update

# install node via Homebrew
# (this also installs npm, the node package manager,
# which we will use to install other tools we need)
brew install node

# install bower
npm install -g bower

# install ember-cli
npm install -g ember-cli
```

#### B. Generate the app

[Should we have them clone something or generate their own apps?  We could have them clone a project with an existing API for projects but also provide instructions for starting their own app]
