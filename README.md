## General Guidelines & Common Pitfalls

### Your .env file

Make sure all of the values in your `.env` are single words with no spaces or special characters OR make sure they're enclosed in quotes. For example:

```
MAIL_FROM_NAME=Snipe-IT Asset Management
```

will break things.

```
MAIL_FROM_NAME='Snipe-IT Asset Management'
```

is safe.

### Creating/editing database schema

Migrations are the process through which all major frameworks (not just PHP) handle creating and modifying database schemas. This allows developers to make sure their schemas are in sync with each other (and staging/production).

In Laravel, you create a migration by typing `php artisan make:migration name_of_your_migration`. You then edit that newly generated file, which lives in `database/migrations` and make the changes you need to make, whether that's creating a new table or modifying an existing table. 

The `up()` method in that migration file is where you put the Laravel code to create/modify tables, and the `down()` method is where you put the code to reverse it. For example, if you were creating a new table named `florms`, the `up()` method would contain the code to create the `florms` table, and the `down()` method would contain the code to drop that new table. Each migration should have an `up()` and a `down()`, and every `up()` should be reversible by the `down()`.

Once you've written your `up()` code, save that file, then execute `php artisan migrate`. You'll see the migration get executed in the screen output. If you need to make a change to that migration before you commit, you can run `php artisan db:rollback` and reverse that migration, make your changes, and run `php artisan migrate` again. Once you're satisfied with your schema, you should commit and push that change.

When you execute `php artisan migrate`, Laravel will automatically populate the built-in `migrations` table to tell the application which migrations have already been run - which means a migration will never be run twice unless it throws an error while executing the migration. 

_Always use migrations, and never edit a database migration that has already been checked in and pushed_. 

The reason for this is that if another developer has already pulled and run your migrations and you change a field type, field length, etc, they will have already executed your first schema change, so their schema will not match yours and things will break in bizarre and mysterious ways that will be difficult to debug. If you accidentally used the wrong field type, name, size, etc and need to change it, create a new migration and make the change there. 


## Base Composer Libraries

These are the standard libraries most Laravel projects *should* include. I say *should* because every project may be a little different. You can manually include these libraries once you've created your laravel project and run your initial `composer install`. 

### Bare Minimum

```
composer require barryvdh/laravel-debugbar
composer require laravel/tinker
composer require watson/validating
composer require --dev roave/security-advisories:dev-master

```
- `barryvdh/laravel-debugbar` is the most critical debugging tool we use
- `laravel/tinker` allows you to interact with your app models, etc via a command line REPL tool
- `watson/validating` provides model level validation. While not required for every project, I can't see why you wouldn't use it.
- `roave/security-advisories` is a package that checks for known vulnerabilities in the packages you're trying to use or are currently using.

### If you need/want an API

```
composer require laravel/passport
```

### If you're using authentication

```
composer require unicodeveloper/laravel-password
composer require schuppo/password-strength
```

### If you're using email

```
composer require eduardokum/laravel-mail-auto-embed
```

`laravel-mail-auto-embed` just automatically embeds images like logos, etc in your HTML emails so they don't break if folks are behind a firewall or the app server isn't accessible.


### If you're using file uploads

```
composer require enshrined/svg-sanitize
composer require intervention/image
```

- `intervention/image` just gives you a really nice API for resizing and manipulating uploaded images
- `enshrined/svg-sanitize` allows you to strip XSS from user uploaded SVG files and must be used if you allow users to upload files

### Anything else

When considering using a package that isn't well known, it's always a good idea to run it past the team - but things to look for are:

- how recently has the package been updated?
- what do their Github issues look like? Tons of open issues with no response? Dig deeper into the closed issues too - it could just be that there is a lot of activity, and they are still actively participating but the rate of new issues is higher than their close rate (which is fine - we just want to know they're still active)
- Is the package trying to do too much or too little? Each new package is a new dependency and a new potential security issue. If the package just provides some syntactic sugar, you can probably skip it. If it's trying to do a million things, you probably *should* skip it, since if it becomes defunct, you've now based a lot of your functionality on some third-party package and you're gonna have a bad time. 

