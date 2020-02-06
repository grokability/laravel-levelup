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

Migrations are the process through which all major frameworks (not just Laravel/PHP) handle creating and modifying database schemas. This allows developers to make sure their schemas are in sync with each other (and staging/production).

You can read [more about migrations here](https://github.com/grokability/laravel-levelup/blob/master/migrations-wtf.md).


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

