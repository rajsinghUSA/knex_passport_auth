# Node.js Backend Auth with Knex.js, SQLite, and Passport.js

### Documenting my process of implementing authentication (and some authorization) for the Node.js backend using Knex.js, SQLite, and passport.js

# 0. (Prerequisite) Initialize your project directory 
Create a fresh new directory for this project to reside, and then run the following command:

```
npm init
```

# 1. Initial setup of the SQLite database
## Create a knexfile.js

At the commandline run the following command at the root of the project's directory. This will create a knexfile.js 

```
$ npx knex init
```
```
Output:
Created ./knexfile.js 
```

Now edit the newly created `knexfile.js` to look like this:

```
module.exports = {

  development: {
    client: 'sqlite3',
    connection: {
      filename: './dev.sqlite3'
    },
    migrations: {
      directory: __dirname + '/db/migrations'
    },
    seeds: {
      directory: __dirname + '/db/seeds'
    }
  },

  test: {
    client: 'sqlite3',
    connection: {
      filename: './test.sqlite3'
    },
    migrations: {
      directory: __dirname + '/db/migrations'
    },
    seeds: {
      directory: __dirname + '/db/seeds'
    }
  },

  staging: {
    client: 'postgresql',
    connection: {
      database: 'my_db',
      user:     'username',
      password: 'password'
    },
    pool: {
      min: 2,
      max: 10
    },
    migrations: {
      tableName: 'knex_migrations'
    }
  },

  production: {
    client: 'postgresql',
    connection: {
      database: 'my_db',
      user:     'username',
      password: 'password'
    },
    pool: {
      min: 2,
      max: 10
    },
    migrations: {
      tableName: 'knex_migrations'
    }
  }

};
```

## Create a migrations file
Migrations make database changes or updates such as creating or dropping tables, or even updating tables with new columns with constraints via generated scripts. These scripts can be built using the commandline tool `knex`, which we just used to create the `knexfile.js`.

We need to do a migration to create our table by running the following command:

```
$ npx knex migrate:make my-users-table
```
```
Output:
Using environment: development
Using environment: development
Using environment: development
Created Migration: /c/Users/Raj/Projects/testbackend/db/migrations/20200315042456_my-users-table.js
```

Knex just created a migration file within the `./db/migrations` directory, a path which we stipulated in `knexfile.js` and automatically created. The migration file bears a filename that is prepended with a timestamp so it knows the execution order when runnning multiple migrations. In my case the file is named `20200315042456_my-users-table.js`.

## Update the newly created migration file
Within this newly created migration file, any newly desired changes to the database are written within `exports.up`, and the instructions to undo those new changes are written within `exports.down`.

Now edit the newly created migration file by replacing its existing contents with the following:

```
exports.up = function(knex) {
  return knex.schema.createTable('users', table => {
    table.increments();
    table.string('username').unique().notNullable();
    table.string('password').notNullable();
    table.boolean('admin').notNullable().defaultTo(false);
    table.timestamp('created_at').notNullable().defaultTo(knex.raw('now'));
  });
};

exports.down = function(knex) {
  return knex.schema.dropTableIfExists('users');
};
```

And then apply these migration changes at the commandline by running the following command:

```
$ npx knex migrate:latest --env development
```


# 2. Give backend server access to the database
To prepare Knex for use with the backend server, let's create a file called `db_connection.js` in the `./db` directory and place the following code within:

```
const environment = process.env.NODE_ENV;
const config = require("../knexfile")[environment];
const knex = require("knex")(config);

module.exports = knex
```

Here, Knex is set to use the environment variable `NODE_ENV` (which defaults to `development`), determining the configuration that will be selected within `knexfile.js`, and then exported to be used in other javascript files.








Passport.js is an *authentication* library and sessions are a part of *authorization*

npm install --save body-parser cookie-session express passport passport-local

# Resources

1. https://medium.com/@karlmatthes/node-authentication-with-express-and-knex-d2d8204537c5
2. https://mherman.org/blog/node-passport-and-postgres/
3. https://blog.bitsrc.io/seeding-your-database-with-thousands-of-users-using-knex-js-and-faker-js-6009a2e5ffbf
4. https://gist.github.com/NigelEarle/70db130cc040cc2868555b29a0278261
