# Helpers

Added support for developers, available in development to help improve efficiency, currently providing `scaffolding`, `database command line` and `artisan command line`three tools, if there are better other utilities The idea of welcome to provide advice.

Installation:

```
composer require laveroadmin/helpers

php artisan admin:import helpers
```

> Part of the function of the tool will create or delete files in the project, there may be some file or directory permissions errors, the problem needs to be resolved. Another part of the database and artisan command can not be used in the web environment.

## Scaffold

This Tool can help you build controller, model, migrate files, and run migration files. access by visit `http://localhost/admin/helpers/scaffold`.

Which set the migration table structure, the primary key field is automatically generated do not need to fill out.

> **Preview:** *Scaffold*

## Database command line

Database command line tool for web integration,Currently supports `mysql`,`mongodb` and `redis`,access by visit `http://localhost/admin/helpers/terminal/database`.

Change the database connection in the upper right corner, and then in the bottom of the input box to enter the corresponding database query and then enter, you can get the query results:

> **Preview:** *Database*

The use of the database and the operation of the database is consistent, you can run the selected database support query.

## Artisan command line

Web version of `Laravel`'s `artisan` command line,you can run artisan commands in it,access it by visit `http://localhost/admin/helpers/terminal/artisan`.

> **Preview:** *Artisan*

## Route list

This tool can use more intuitive to show all the routes, including uri, http methods and middleware, and also you can query routes. access it by visit`http://localhost/admin/helpers/routes`.

> **Preview:** *Routes*
