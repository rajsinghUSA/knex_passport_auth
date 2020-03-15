# Node.js Backend Auth with Knex.js, SQLite, and Passport.js

### Documenting my process of implementing authentication (and some authorization) for the Node.js backend using Knex.js, SQLite, and passport.js


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
## Create a migrations file within a migrations directory
We need to do a migration before we create a table
```
$ npx knex migrate:make my-users-table
```
```
Output:
Using environment: development
sqlite does not support inserting default values. Set the `useNullAsDefault` flag to hide this warning. (see docs http://knexjs.org/#Builder-insert).
Using environment: development
Using environment: development
Created Migration: /c/Users/Raj/Projects/testbackend/migrations/20200315013840_my-users-table.js
```
Knex just created a file within the `./migrations` directory, bearing a filename that's prepended with a timestamp so it knows the execution order when runnning multiple migrations. In my case the file is named `20200315013840_my-users-table.js`.

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
    table.timestamp('created_at').notNullable().defaultTo(knex.raw('now()'));
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










# Resources

1. https://medium.com/@karlmatthes/node-authentication-with-express-and-knex-d2d8204537c5
2. https://mherman.org/blog/node-passport-and-postgres/
3. https://blog.bitsrc.io/seeding-your-database-with-thousands-of-users-using-knex-js-and-faker-js-6009a2e5ffbf
4. 
