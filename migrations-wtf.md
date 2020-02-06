## Migrations - WTF?

If you're new to frameworks and/or ORM (Object-Relational Mapping) systems but have dipped your toes into some database stuff, migrations might seem like a weird concept. 

Perhaps you're used to running SQL commands directly via command line or through a GUI tool to create tables, alter table structure, etc. That's pretty normal, but can cause problems when you're working with multiple developers at the same time. 

Let's say you run a database-modifying command on your local system, but forgot to tell the other developers. Now your schemas are out of sync, and it can take some time to figure out what the differences are, which one is "right", etc.

In Ruby on Rails, Laravel, and other popular frameworks, we use migrations to handle those changes instead. Effectively what this means is that you have a new table called `migrations` that gets created automatically by your framework. That `migrations` table's job is to keep track of what database-altering changes have already been run. It does this by way of migration files. 

In Laravel, when we want to make a change to the database, we use the artisan command:

```
php artisan make:migration some_short_description_of_the_change
```

This will create a migration template in `database/migrations` with the `some_short_description_of_the_change` name. I typically like to start the `some_short_description_of_the_change` with what the action is, for example if I'm creating a new table, I might use:

```
php artisan make:migration create_users_table
```

and if I am altering a table, I try to describe the change, like:

```
php artisan make:migration added_city_to_users
```

The reason for this is that over time, you may end up with many migration files (my biggest project has 300 or so migration files!), and if you ever need to reference them, having names you can easily scan will save you time. 

Once that migrations file is created, you'll need to add the schema changes you want to execute. Each migration file has an `up()` and a `down()` method. The `up()` method is the change you want to execute, while the `down()` method rolls it back. For example:

```php
public function up()
    {
        Schema::create('flights', function (Blueprint $table) {
            $table->bigIncrements('id');
            $table->string('name');
            $table->string('airline');
            $table->timestamps();
        });
    }
```

creates a table called `flights`, and the corresponding `down()` method:

```php
public function down()
    {
        Schema::drop('flights');
    }
```

drops that table. You always want to have both, so that a change can be rolled back cleanly. 

When you're done in that migrations file, you'll still have to actually execute the migration to perform the change. You do this by running:

```
php artisan migrate
```

If you're in production mode in your app, it will ask you if you're sure you want to make the change. If you're in local or develop mode, it won't. Artisan will then try to execute the migration, and if it does so successfully, it will show you a list of all of the migrations that were executed. At the same time behind the scenes, it adds at least one record to the `migrations` table which consists of the filename (`TIMESTAMP_some_short_description_of_the_change`) and the "batch" number. 

This is how Laravel knows not to run the same migration twice. This also means you should NEVER change the filename of a migration after it's been committed or run, since it will see that renamed migration file as a new migration and will try to execute it again, which will almost certainly result in an error.

If the migration fails for any reason, no record will be added to that `migrations` table for that migration.

If you need to roll back the migration, you would run:

```
php artisan migrate:rollback
```

This undoes the most recent batch of migrations, and this is why your `down()` method is really important. When you roll back, Laravel will try to execute the code in the `down()` method, which should reverse the change you made in the `up()` method. If it doesn't exactly undo that change, you will likely see errors when you try to migrate again.

For example, if you add a column to a table in your `up()` method, and forget to drop it in your `down()` method, next time you run that migration after a rollback, you'll get an error that the column already exists, since it was never dropped in your `down()` method.

### Remember that dropping a column means dropping all of the data in that column!

For most scenarios, you wouldn't have any data in that column yet (except maybe test data), but be careful of copypasta here. If you accidentally specify the wrong column or table name to drop in your `down()` method, you could potentially destroy data.

### Never change a migration "back in time" 

Once a migration file has been committed and pushed, it's untouchable. Do not modify it! Doing so will cause inconsistencies across developer systems who have already run the older migration, and will cause you no end of headache trying to troubleshoot. If you made a mistake in your schema and need to correct it, make a new migration. 

__There is one single exception to this rule, and it should be considered a nuclear option to be used only when there is literally no other way__. I have had to do back-in-time migration changes when an older migration becomes incompatible with a newer version of MySQL/MariaDB. The reason I had to do this is because even though we corrected the schema issue in a later migration, the initial *installation* migrations would fail, so we could never even *reach* the correcting migration. It sucked, and I hated it, but there was no other way. This should almost never happen though. In seven years, I've only had to do this once. In general, back-in-time changes should be considered verboten. 

That doesn't mean you can't experiment locally, making a migration, rolling it back, changing the migration file, running it again, etc. But once it's been committed and pushed up to the repo, it should be considered sacred.

Laravel's [documentation on migrations](https://laravel.com/docs/5.7/migrations) is actually pretty solid, and I strongly recommend giving it a thorough read. 


