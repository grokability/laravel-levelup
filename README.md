## General Guidelines

## Your .env file

Make sure all of the values in your `.env` are single words with no spaces or special characters OR make sure they're enclosed in quotes. For examnple:

```
MAIL_FROM_NAME=Snipe-IT Asset Management
```

will break things.

```
MAIL_FROM_NAME='Snipe-IT Asset Management'
```

is safe.


### Base Composer

This is a very basic example of a composer file that will install Laravel and most of the common libraries we'd want to use in almost any project. Obviously, depending on the needs of the specific project. The easiest way to implement this in a new project will be to `laravel new projectname`, let it install as usual, and then swap out the composer file. 

```json
{
  "name": "username/snipe-it",
  "description": "A brief description here.",
  "keywords": ["keyword1", "keyword2", "etc"],
  "license": "AGPL-3.0-or-later",
  "type": "project",
    "require": {
    "php": ">=7.1.2",
    "barryvdh/laravel-debugbar": "^3.2",
    "eduardokum/laravel-mail-auto-embed": "^1.0",
    "enshrined/svg-sanitize": "^0.13.0",
    "erusev/parsedown": "^1.7",
    "guzzlehttp/guzzle": "^6.3",
    "intervention/image": "^2.4",
    "laravel/framework": "5.8.*",
    "laravel/passport": "4.*",
    "laravel/tinker": "^1.0",
    "laravelcollective/html": "^5.5",
    "rollbar/rollbar-laravel": "2.*",
    "schuppo/password-strength": "~1.5",
    "unicodeveloper/laravel-password": "^1.0",
    "watson/validating": "^3.0"
  },
    "require-dev": {
    "codeception/codeception": "2.3.6",
    "filp/whoops": "~2.0",
    "fzaninotto/faker": "~1.4",
    "phpunit/php-token-stream": "1.4.11",
    "phpunit/phpunit": "~6.0",
    "roave/security-advisories": "dev-master",
    "squizlabs/php_codesniffer": "*",
    "symfony/css-selector": "3.1.*",
    "symfony/dom-crawler": "3.1.*"
  },
    "autoload": {
        "classmap": [
            "database"
        ],
        "psr-4": {
            "App\\": "app/"
        }
    },
    "autoload-dev": {
        "classmap": [
            "tests/TestCase.php",
            "tests/unit/BaseTest.php"
        ]
    },
    "scripts": {
        "post-root-package-install": [
            "php -r \"file_exists('.env') || copy('.env.example', '.env');\""
        ],
        "post-create-project-cmd": [
            "php artisan key:generate"
        ],
        "post-autoload-dump": [
          "Illuminate\\Foundation\\ComposerScripts::postAutoloadDump",
          "@php artisan package:discover"
        ]
    },
    "config": {
        "preferred-install": "dist",
        "sort-packages": true,
        "optimize-autoloader": true,
        "process-timeout":3000,
        "platform": {
          "php": "7.1.2"
        }
    }

}


```

If you run into conflicts (for example, if your version of Laravel is later than the one here), you can manually include these libraries using:

### Bare Minimum

```
composer require barryvdh/laravel-debugbar
composer require laravel/tinker
composer require watson/validating

```
- `barryvdh/laravel-debugbar` is the most critical debugging tool we use
- `laravel/tinker` allows you to interact with your apps models, etc via a command line REPL tool
- `watson/validating` provides model level validation. While not required for every project, I can't see why you wouldn't use it.


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

