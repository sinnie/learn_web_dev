#Knex.js

## Terms:
* [__Knex.js__](http://Knex.jsjs.org/#Installation-node) - a third party JS library that builds SQL commands and sends them to a RDB like PostgreSQL; Communicate with a SQL server with Node.js; build and send SQL queries; it is a query builder; ORM (object relational mapping)
* [__Promise__](../promises.md) - an object that’s used for asynchronous operations; Actually an object that hasn’t completed yet but will in the future; It offers a separation of success handling from error handling
    * __async__ - non-blocking when it’s done
* __SQL Injection Attack__ - Occurs when user input is not filtered to escape characters and is then passed into an SQL conned, which results in the potential for a malicious user to manipulate the database commands that a web app performs
    * Protect against SQL injection attacks, escape the special characters that a user, may input into a web app. In SQL a single-quote ( ‘ ) character is escaped with another single-quote
    * Knex.js does this for you automatically; therefore, it is safer than SQL
* Query Builder - the API used to build and send SQL queries (`SELECT`, `INSERT`, `UPDATE`, `DELETE`)

>> NOTE:
> by convention, all table names are plural by default

## Connecting Knex.js to a SQL Server

When the `require(‘Knex.js’)(config)` => is called, Knex.js opens two connections to a server. This allows Knex.js to send multiple SQL commands to a server concurrently. When Knex.js.destroy( ) => is called, Knex.js closes the connections. If the connections are not closed, the program will run indefinitely.
* Knex.js can open up to 10 connections

## Why is Knex.js useful?
* One can build Node.js web applications that can create, read, update, and destroy the rows tables, and dos of a RDBS like PostgresQL
* Protects against SQL injection attacks
* `CRUD`
    * `select()`
    * `insert()`
    * `update()`
    * `del()`
* Query Builder - the API used to build and send SQL queries (`SELECT`, `INSERT`, `UPDATE`, `DELETE`)
    * `SELECT` -  created a `SELECT` command; Accepts an optional list of column names as string arguments and adds them tot eh SELECT clause of a query. When no arguments are specified, it adds a * to the SELECT clause. The `select()` method returns a promise. When the promise is resolved, the `.then()` method’s callback is triggered and given an array of objects for the matching rows in a table.
    * `WHERE` - Several Knex.js methods exist in adding dynamic `WEHRE` clauses to a query. The first is the `where()` method.
        * It accepts two arguments and one optional arg: a column name as a string and a value to match against
        * (Note: supplying `where()` with an undefined val will throw an error); To add `AND` clauses to a query, chain `where()` methods
        * accepts three args:
            * col name as string
            * operator as a string
            * value to operate against
        * accepts one arg:
            * object with key-value pairs
                * keys translate column names and the values translate to their respective comparison values.
                * if an object with multiple key-value pairs is given, the `where()` method adds multiple `AND` clauses to the query
        * `orWhere()` - works the same, except it adds an OR clause to the query, grouping its arguments in parens
        * `whereNot()`
        * `whereIn()`
        * `whereNotIn()`
        * `whereNull()`
        * `whereNotNull()`
        * `whereExists()`
        * `whereNotExists()`
        * `whereBetween()`
        * `whereNotBetween()`
        * `whereRaw()`
    * `orderBy` - adds an `ORDER BY` clause to the query
        * two args:
            * col name
            * direction
    * `limit()`
        * adds a LIMIT clause to the query
            * single number argument
    * `POST`
        * `insert()`
            * creates an `INSERT` command.
            * accepts an object of key-value pairs to be inserted into a row in the table
            * returns a promise; when the promise is resolved, the `.then()` method’s callback is triggered and given an object that contains the number of rows inserted
            * accepts a list of sting column names as a second argument, which indicates what columns of the newly inserted row to pass into the `.then()` method’s callback.
                * usually * is used to pass along all the columns of the row
                * any col value (id, col, value, etc) ( for this example )
    * `UPDATE`
        * `update()`
            *  returns a promise; when the promise is resolved, the `.then()` method’s callback is triggered and given a single value representing the number of rows updated
    * `DELETE`
        *  `del()`
            * no args
            * returns a promise; when the promise is resolved, the then( ) method’s callback is triggered and given a single value representing the number of rows deleted.
                * delete - is a keyword in js, so - del( ) is used by Knex.js.

## `DELETE` clause:
```javascript
  const env = 'development';
  const config = require('./knexfile.js')[env];
  const knex = require('knex')(config);


  knex(table)
    .then((result) => {
      console.log(result);
      knex.destroy();
    });
    .catch((err) =>{
      console.error(err);
      knex.destroy();
      process.exit(1);
    });

```




## `SELECT` clause:
```javascript
  'use strict';
  const env = 'development';
  const config = require('./Knex.jsfile.js')[env];
  const Knex.js = require('Knex.js')(config);

  Knex.js('movies')
    .select('id', 'title', 'rating', 'is_3d', 'score')
    .then((result) => {
      console.log(result);
      Knex.js.destroy();
    })
    .catch((err) => {
      console.error(err);
      Knex.js.destroy();
      process.exit(1);
    });
```

## `WHERE` clause:
```javascript
'use strict';

  const env = 'development';
  const config = require('./Knex.jsfile.js')[env];
  const Knex.js = require('Knex.js')(config);

  Knex.js('movies')
    .select('id', 'title', 'rating', 'is_3d', 'score')
    .where('score', '>=', 7.5)
    .where('rating', 'PG')
    .then((result) => {
      console.log(result);
      Knex.js.destroy();
    })
    .catch((err) => {
      console.error(err);
      Knex.js.destroy();
      process.exit(1);
    });
```

## OFFSET clause
* `offset()`

## JOIN clause
* `innerJoin()`

## DISTINCT clause
* `distinct()`

## Aggregate methods
* `count()`
* `max()`
* `min()`
* `sum()`
* `avg()`

## Helper methods
* `increment()`
* `decrement()`
* `pluck()`
* `first()`
* `raw()`

## Workflow to set up new DB and init:
```
dropdb example_db_dev
createdb example_db_dev
```

Then, setup a new Node.js project by running the following shell commands:

```
mkdir new_dir && cd new_dir
npm init -y
npm install --save pg
npm install --save Knex.js
touch Knexfile.js
touch index.js
```

## Knex.js setup workflow
In the Knexfile.js file, write and save the following code:

```javascript
module.exports = {
  development: {
    client: 'pg',
    connection: 'postgres://localhost/example_db_dev'
  }
};
```

In the index.js file, write and save the following code.
    * Knex.js must output a function and the configuration is required as an argument.

The Knex.js module is itself a function which takes a configuration object for Knex.js, accepting a few parameters. The client parameter is required and determines which client adapter will be used with the library.

```javascript
'use strict';

const env = 'development';
const config = require('./Knex.jsfile.js')[env];  // <— *
const Knex.js = require('Knex.js')(config);

const sql = Knex.js('movies').toString();

console.log(sql);

Knex.js.destroy(); //<— the program will not terminate unless the Knex.js.destroy( ) fn is called
```
---

# Knex Config, Migrations, and Seeds

## Terms:
* __batches__ - Any group of migration files that were executed before you rolled back
    * `npm run knex <table_name> migration:latest`

## KNEX MIGRATION (create/destroy tables)
* brew services list - see if a service is running
* psql -l - display all databases
* npm install —save knex: save knex locally to the npm project

## How do you configure a knex project?
```javascript
     'use strict';
     module.exports = {
       development: { // <— when we’re working with the development env, use this setting (also the default)  
         client: 'pg', // <— package tells Knex.js which db we are working with (PostgreSQL).
         connection: 'postgres://localhost/trackify_dev'
       }
     };
```
* When you install a Node.js module that includes an executable, the executable file is stored in `./node_modules/.bin`
* `./node_modules/.bin/knex migrate:currentVersion` - shows the current migration status
* To run an executable node module: the module is stored in `./node_modules/.bin`. Check the current migration status by running: `./node_modules/.bin/knex migrate:currentVersion`
* npm allows us to add command shortcuts in our `package.json` file inside a scripts object
```javascript
    "scripts": {
     "knex": "./node_modules/.bin/knex"
    },
```
    * And we can shorten this to:
``` javascript
    "scripts": {
      "knex": "knex"
    },
```
  * We run script commands listed in our `package.json` by typing npm run followed by the name we gave the command.
        * `npm run knex migrate:currentVersion`
* `npm run knex migrate:currentVersion`: to run script commands listed in package.json
* `npm run knex migrate:make <file_name> `: This automatically creates a migration folder in the root directory of your project (unless it already existed) and adds a tracks migration file into the folder.
    * la to see all files and directories
    * Do this in root as general good practice
* `npm run knex migrate:latest`: The migrate:latest command is used to run the migration file on the database.
    * `migrate:currentVersion` <— runs the `.up` function
    * `npm run knex migrate:latest`: The `migrate:latest` command is used to run the migration file on the database.
* `psql trackify_dev -c '\d knex_migrations’`:  Look at the columns of the knex_migrations table.
    * -c will allow you to send commands to the repl
* `psql trackify_dev -c 'SELECT * FROM knex_migrations;’` : to look at the rows in the knex_migrations table.
*  `npm run knex migrate:rollback`: The `migrate:rollback` command migrates the database backward by running the down function exported by your migration file.
    * rollback runs the `.down()` method
        * You can do things in the `.down()` function other than drop the table.
    * The `migrate:rollback` command migrates the database backward by running the down function exported by your migration file.
* p`sql trackify_dev -c '\d knex_migrations_lock’`: Add migration locking so multiple services cannot try to run migrations at same time. This added a new lock table. If migrations are locked and migrations are run by another service it results in an error. Two things cannot be updating a db simultaneously.
* `npm run knex migrate:make users`: Use the migrate:make command to create the new file.

## Why is the Knex Migration System Useful?
* consistent way to automate the management of db tables across all environments (dev, test, production)
* describing a change in a db from before to after
    * specify the changes that you are making to the structure of the tables (cols)
* Migration files consistently produce the same tables on an empty db
* Mistakes that are caught early allows a developer to drop the affected tables, rolling the db back to a known good state
    * useful for correcting bugs in a table’s structure

## How do you use Knex to migrate a PostgreSQL db?

```javascript
'use strict';

exports.up = function(knex) {
  return knex.schema.createTable('tracks', (table) => {
    table.increments();
    table.string('title').notNullable().defaultTo('');
    table.string('artist').notNullable().defaultTo('');
    table.iteger('likes').notNullable().defaultTo(0);
    table.timestamps(true, true);
  });
};

exports.down = (knex) => {
  return knex.schema.dropTable('tracks');
};
```

A knex migration will take all new migration files, group them in a batch and then apply them. A rollback will rollback all of the files in a batch.
    * Example:
        * Create Migration File 1 then Create Migration File 2. Then run `knex migrate:latest` Two migrations will run, and one batch created. If you call `knex migrate:rollback`, the two migrations will be rolled back, but only one batch.
        * To help illustrate this, here is an example:

        ```
    ┌──────────────----───────┐
    │ Create Migration File 1 │
    │ Create Migration File 2 │
    └────────────----─────────┘
                │
                V
    ┌─────────────────────────-----─────────┐
    │ knex migrate:latest                   │
    │ Two migrations run, one batch created │
    └───────────────────────-----───────────┘
                │
                V
    ┌───────────────────────────----------------────-─────┐
    │ knex migrate:rollback                               │
    │ Two migrations rolled back, one batch rolled back   │
    └────────────────────────-----------------────────────┘
    ```

    * vs
        * Create Migration File 1 One migration run, one batch created. knex migrate:latest Create Migration File 2 One migrations run, one batch created. knex migrate:rollback One migrations rolled back, one batch rolled back. knex migrate:rollback One migrations rolled back, one batch rolled back.

## Knex.js Seed System (create/destroy rows)
* `npm run knex seed:make 1_tracks` : command to use knex to seed a PostgreSQL database
* `npm run knex seed:run` : command used to execute seed files and seed a PostgreSQL db
  * Notice that seed files only export a single function that removes all rows from the table and then inserts the specified rows.

__Knex.js Seed System:__

The knex.js seed system allows developers to automate the initialization of table rows in JS.
    * The heart of the seed system are __seed files__. Unlike the knex.js migration system, the seed files do not run in batches; They all run every time you run the knex.js seed command.
* Why is the Seed System Useful?
    * Most web applications start with an initial set of table rows. It’s useful to be able to automatically seed a db with that set.
    *  Every time you rollback your database, one or more tables are dropped and all rows are removed.
    * It is helpful to be able to run migration and seed files and be up to speed with the rest of the team.
    * Seed files only export a single function that removes all rows from the table and then inserts the specified rows.

    ```javascript
        'use strict';
        exports.seed = function(knex) {
         return knex('tracks').del( ) // <—First it deletes all of the data
           .then(( ) => {
             return knex('tracks').insert([{ // <— Usually want to insert an arr of obj
               id: 1,
               title: 'Here Comes the Sun',
               artist: 'The Beatles',
               likes: 28808736,
               created_at: new Date('2016-06-26 14:26:16 UTC'),
               updated_at: new Date('2016-06-26 14:26:16 UTC')
             }, {
               id: 2,
               title: 'Hey Jude',
               artist: 'The Beatles',
               likes: 20355655,
               created_at: new Date('2016-06-26 14:26:16 UTC'),
               updated_at: new Date('2016-06-26 14:26:16 UTC')
             }]);
           });
           .then(() => {
             return knex.raw(
               "SELECT setval('tracks_id_seq', (SELECT MAX(id) FROM tracks));" // <— This allows us to test. Sets the raw sql to update seq id
             );
           });
        };
    // (No catch; knex creates one for you)
    ```
## Terms:
knex.js
* __Knex Migration System__ - allows developers to automate the management of db tables in JS
* Migration File - a file that moves the db up and down, or forwards and backwards through a set of changes applied to a single table
    * migrating db forward and then migrating the db backwards
    * name starts with a UTC timestamp and ends with a table name
        * order matters - hence the timestamp
    * Designed like this so that the Knex migration system can identify and order the migrations based on when the files where created and what tables they affect.
    * Exports:
        * `up()` - returns instructions to the knex.js migration system on how to migrate the db forward
            ```javascript
             CREATE TABLE tracks (
                   id serial PRIMARY KEY,
                   title varchar(255) NOT NULL DEFAULT '',
                   artist varchar(255) NOT NULL DEFAULT '',
                   likes integer NOT NULL DEFAULT 0,
                   created_at timestamp with time zone NOT NULL DEFAULT now(),
                   updated_at timestamp with time zone NOT NULL DEFAULT now()
                 );
                 table.timestamps(true, true); // <- Sets created and updated at timestamps
              ```
        * `down()` - returns instructions on how to migrate the db backward
* __Migrations:__ Migrations dictate how we define and update the database schema.
* __Knex.js Seed System:__ The knex.js seed system allows developers to automate the initialization of table rows in JavaScript.

---

## Workflow:
1. `mkdir <dir_name>`
2. `cd <dir_name>`
3. `npm init -y`
4. Add .DS_Store, node_modules, and npm-debug.log to a .gitignore file.
    * `echo '.DS_Store' >> .gitignore`
    * `echo 'node_modules' >> .gitignore`
    * `echo 'npm-debug.log' >> .gitignore`
5. Create a database called <dir_name> and confirm that it was created.
    * `createdb <dir_name>`
    * `psql -l`
6. `npm install --save pg knex` - The migration cli is bundled with the Knex.js install. Also, be sure to install the PostgreSQL client.
7. Create a `knexfile.js` file and configure the development environment.
    * `touch knexfile.js`
    ```javascript
        'use strict';

        module.exports = {
          development: {
            client: 'pg',
            connection: 'postgres://localhost/trackify_dev'
          }
        };
    ```
8. Add npm command shortcuts in the `package.json` file inside a scripts object
```javascript
     “scripts”: {
         “knex”: "knex"
     },
```
## Migrate Workflow:
* `npm run knex migrate:currentVersion `: to run script commands listed in package.json
* `npm run knex migrate:make tracks `: This creates a migration folder in the root directory of your project (unless it already existed) and adds a tracks migration file into the folder.
    * la to see all files and directories
    * Do this in root as general good practice
* `npm run knex migrate:latest`: The `migrate:latest` command is used to run the migration file on the database.
*  `npm run knex migrate:rollback`: The `migrate:rollback` command migrates the database backward by running the down function exported by your migration file.
## Seed Workflow:
1. `seed:make <file_name>` - command to create a new seed file
2. `seed:run <file_name>` - command to run the seed script and seed the db


### References:
https://www.postgresql.org/docs/current/static/sql-createindex.html
http://Knex.jsjs.org/#Builder
http://michaelavila.com/Knex.js-querylab/
https://en.wikipedia.org/wiki/SQL_injection
