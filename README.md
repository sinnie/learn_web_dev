# Web Dev Cheet Sheet

## [Promises]('./promise.md')

## [Node]('./node.md')


##Express Server Middleware:
* cookie-parser (npm install --save cookie-parser) <— Parse Cookie header and populate req.cookies with an object keyed by the cookie names
* bcrypt-as-promised (npm install --save bcrypt-as-promised)- Hashes, salts, and compares passwords/inputs
* jwt - jsonwebtoken (npm install --save jsonwebtoken) <— the token conforms to JWT standard (pronounced jot). A JWT is a method of communication the authentication state through an open standard.
* dotenv (npm install —save-dev dotenv) <— zero-dependency module that loads environment variables from a .env file into process.env.
```javascript
'use strict';
if (process.env.NODE_ENV !== 'production') {
  require('dotenv').config();
}
```
* Express (npm install —save express)- server side framework - makes creating HTTP servers easier
* Body-parser (`npm install —save body-parser`) <— parses things: JSON
* Morgan (`npm install —save morgan`) <— logs things to the terminal - the time requests take for the server to complete
* humps (`npm install —save humps`)  <-- (camelize/decamelize)
* Boom (`npm install —save boom`) - atomizes error messaging; Boom formats the error object for you
* knex - Knex facilitates: Object-relational mapping (converting data between incompatible type systems; sql & js)
  * `‘pg'` <— PostgreSQL
  * `npm install --save pg`
  * `npm install--save knex`
* Foreman - (`npm install --save-dev foreman` ) foreman is a manager for procfile-based apps that aims to abstract away the details of the procfile format and allow developers to run an app directly or export it to some other process management format
* Joi - ( `npm install --save express-validation joi` ) Joi allows you to create blueprints or schemas for JavaScript objects (an object that stores information) to ensure validation of key information.
* ESLint - ( `npm install -D eslint eslint-config-ryansobol` ) ESLint is an open source JavaScript linting utility. Code linting is a type of static analysis that is frequently used to find problematic patterns or code that doesn’t adhere to certain style guidelines.
* Request - ( `npm install --save request` )  is designed to be the simplest way possible to make http calls. It supports HTTPS and follows redirects by default.
* [Passport](http://passportjs.org/docs/overview) - authentication middleware for node.js that is designed for a singular purpose: to authenticate requests.
Authentication mechanisms, known as strategies, are packaged as individual modules. Applications can choose which strategies to employ, without creating unnecessary dependencies.


## ESLint:
### Ryan’s Config:

#### ESLint Setup:
1. touch .eslintrc.js
2. Add language configuration and environment configuration to the .eslintrc.js file:

```javascript
module.exports = {
  extends: [
    'ryansobol/browser',
    'ryansobol/es5',
    'ryansobol/es6',
    'ryansobol/jquery',
    'ryansobol/node',
    'ryansobol/materialize',
    'ryansobol/express',
    'ryansobol/react'
  ],
  parserOptions: {
    sourceType: 'module'
  }
};
```

3. Run `eslint` locally and fix any linting errors: ./node_modules/.bin/eslint .
4. Additionally, add a script to the `package.json` file:

```javascript
{
  "script": {
    "lint": "eslint ."
  }
}
```

5. Then run the npm script and fix any linting errors: `npm run lint`

## Knex:

### Knex Setup:

1. `mkdir <dir_name>`
2. `cd <dir_name>`
3. `npm init -y` - initialize it in npm which will create a package.json file that saves dependencies and scripts
4. `npm install --save pg` - PostgreSQL - So knex can talk to it
5. `npm install --save knex` - ORM
6. `touch knexfile.js ` (or knex init)
7. `touch index.js`

8. In the knexfile.js file, write and save the following code (environment configuration).
  * This code exports the development env to have a client of PostgreSQL

```javascript
'use strict';

module.exports = {
  development: {
    client: 'pg',
    connection: 'postgres://localhost/bookshelf_dev'
  },

  test: {
    client: 'pg',
    connection: 'postgres://localhost/bookshelf_test'
  },

  production: {
    client: 'pg',
    connection: process.env.DATABASE_URL
  }
};
```

9. In the knex.js file, write and save the following code.
  * Knex must output a function and the configuration is required as an argument. The knex module is itself a function which takes a configuration object for Knex, accepting a few parameters. The client parameter is required and determines which client adapter will be used with the library.

```javascript
'use strict';

const env = 'development';
const config = require('./knexfile.js')[env];   <— *
const knex = require('knex')(config);

Module.exports = knex;
```
* _Troubleshooting tip:_ sqlite3 is the default knex db

### Knex Migrate Flow:
* `npm run knex migrate:currentVersion `: to run script commands listed in package.json
* `npm run knex migrate:make <file_name>` : This creates a migration folder in the root directory of your project (unless it already existed) and adds a tracks migration file into the folder.npm
  * Do this in root as general good practice; But it doesn’t really matter
npm run knex migrate:latest: The migrate:latest command is used to run the migration file on the database.
* `npm run knex migrate:rollback`: The migrate:rollback command migrates the database backward by running the down function exported by your migration file.

### Knex Seed Flow:
* `npm run knex seed:make <file_name>` - command to create a new seed file. Files are created in the dir specified in the knexfile.js
* `npm run knex seed:run <file_name>` - command to run the seed script and seed the db
* `npm run knex seed:run` - runs all seed scripts; seed files are run in alphabetical order


## Heroku:
### Heroku Deployment:
1. Create production environment on heroku - `heroku apps:create <project_name>` (Where username is your github username)
2. Inspect the properties of the production database - `heroku apps:info`
3. Specify the exact version of Node.js on the production environment: In package.json:

 ```javascript
     "engines": {
          "node": "DEV_VERSION"
     }
```

4. `heroku addons:create heroku-postgresql` -  Create a PostgreSQL database for the production environment
5. `heroku pg:info `- Inspect the properties of the production database
6. Specify the connection URL to the production database server by adding the following property to the knexfile.js file:

```javascript
    production: {
     client: ‘pg’,
      connection: process.env.DATABASE_URL
    }
```

7. Automatically migrate the production database after on deployment by adding the following property to the package.json file:

```javascript
    "scripts": {
       "knex”:”knex"
       "heroku-postbuild": "knex migrate:latest"
     }
```

8. Install foreman - `npm install —save-dev foreman  `
9. Create a Procfile - start the server on the production env - `echo ‘web: node server.js’ > Procfile` (A Procfile is a mechanism for declaring what commands are run by your application's dynos on the Heroku platform. It follows the process model. You can use a Procfile to declare various process types, such as multiple types of workers, a singleton process like a clock, or a consumer of the Twitter streaming API.) ← if you do not have a space between the : and node it will break everything
10. Easily test foreman from the dev env by adding an nf script to package.json <--not needed with brunch

```javascript
  "scripts": {
       "knex”:”knex"
       "heroku-postbuild”: “knex migrate:latest",
       "nf”: “nf start",
       "nodemon”: “nodemon server.js"
   }
```

Generate secret key to be used to sign session information on the prod. env.
               bash -c ‘heroku config:set JWT_SECRET=$(openssl rand -hex 64)'
Deploy to Heroku: git push heroku master
Inspect the production Environment : heroku apps:info
Inspect the production Database: heroku pg:info
Seed the production database: heroku run npm run knex seed:run
Inspect the production Database again: heroku pg:info
Login to the production Database and verify: heroku pg:psql

With Brunch:
In package.json, make sure you move all of the following dependencies from devDependencies to dependencies:
babel-brunch
Babel-preset-es2015
Brunch version in package.json 2.8.2
clean-css-brunch
javascript-brunch
postcss-brunch
uglify-js-brunch
       2. "heroku-postbuild": "brunch build --production; knex migrate:latest", -  Update in package.json the script heroku-postbuild. This builds the public folder on deployment (no auto reloading is needed as that's for development).
Also, ensure that $http is included in every service

Heroku addons: create heroku-postgresql --version 9.5

Heroku Dev Center - Getting Started on Heroku with Node.js
Heroku Dev Center - Heroku Postgres


## Git:
git add .
git commit -m ‘msg'
git checkout -b <branch_name> - make a new branch
git checkout master - go back to the master branch
git merge <branch_name> - merges the branch to whichever branch you have checked out
git branch -d <branch_name> - deletes the branch_name
git st - git status; fish shortcut
 git checkout -b <branch_name> <commit sha> - revert to branch even if deleted

### Feature Branch Workflow Steps:
merge the commits from the local Feature branch into the local master branch.
pushing your local master branch to the central origin/master branch, adding the feature commits into the official project history.
Now that all three branches contain the same commits, you can safely delete the Feature branch.
Feature Branch Workflow Team Based Steps:
git log --oneline --graph --all --decorate=short
or git ll to show commit logs of local repo
git add .
git c ‘commit msg’
git checkout master (git co master)
git pull
git checkout <feature_branch>
git rebase master
git checkout master
git merge <feature_branch>
git push
git branch -d <branch_name>





## Angular 1.x w/ES6
uiRouter




## React
React-router V4



## Brunch:
Brunch - ultra-fast HTML5 build tool. Brunch is a builder. Not a generic task runner, but a specialized tool focusing on the production of a small number of deployment-ready files from a large number of heterogenous development files or trees.
### Ken’s Angular config:
1. `npm install -g brunch`
2. `cd path/to/app`
3. `brunch new  <project_name>  --skeleton kmcgrady/with-angular`
4. Build app/app.js file, which will initialize the application
  import angular from 'angular'
  angular.module('todoApp', []);
### Ryan’s React Config
* `brunch new <project_name>` --skeleton ryansobol/with-react
-s flag short for skeleton
