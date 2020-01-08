### Base Composer

This is a very basic example of a composer file that will install Laravel and most of the common libraries we'd want to use in almost any project. Obviously, depending on the needs of the specific project. 

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
    "doctrine/cache": "^1.8",
    "doctrine/common": "^2.10",
    "doctrine/dbal": "2.9.0",
    "doctrine/inflector": "^1.3",
    "doctrine/instantiator": "^1.2",
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
