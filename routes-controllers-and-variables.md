One thing that can be confusing to folks new to frameworks is how the stuff we include in route definitions and the stuff we pass along through other methods are different. 

First let's recap some of the stuff we've gone over at a VERY high level regarding MVC frameworks:

- Models: Models are for business logic. What does each object need, what does it mean, how does it relate to the other objects? You lay down the important relationships between objects here. If a `User` `hasMany` `Items`, this is where you define that.
- Views: the front-endy bits. This is where your HTML goes. This (other than *maybe* some JS components) is the ONLY place HTML lives in your entire app ecosystem.
- Controllers: Controllers should take in web requests (via API or your application's GUI), do a few small things (sometimes validation, sometimes normalizing data that could be presented in a variety of ways but can only be handled one way in the database, etc), and then return info or redirect you someone. These should be as lean as possible. 
- Routes: (not an `M`, `V`, or a `C`, but still usually pretty important) This is the air traffic controller for all web requests that come in, and it actually matters for the purposes of this lesson. This is how the app tells the webserver which controller and controller method it should be looking at if someone types in a particular URL. Think of routes as your very first layer of interaction with the user. 

### Let's Talk About Routes, Baby

I just mentioned that routes are basically the air traffic controllers in an MVC framework, but what does that really mean? Routes take HTTP requests - as in, the things people type into their browser - and figure out where to direct that request. 

Let's look at a really simple route declaration:

```php
    Route::get('monsters', [
            'uses' => 'MonstersController@show'
    ]);
```

The `Route::get()` part of the code above is what tells you that this is:

1) This should be *invoked* by `<yourrootdomain.com>/monsters`, AND it's a `GET` request (more on that just below)
2) This should *handled* by your `MonstersController.php`, in the `show()` method.

I'll take a step back and look very briefly at HTTP requests. This part matters because you need to understand the difference in order to create your routes, and you can't invoke your controllers without having defined routes for them. 

In the most simple terms, at least in the ways it will impact you here:

### `GET` is for showing, `POST` is for changing. 

#### `GET` requests: 

In most modern frameworks, you'll see two primary types of `GET` requests. One is just regular HTTP protocol stuff, and and the other is more specific to frameworks.

Outside of frameworks, a typical `GET` request looks like this:

`monsterkillers.com/monsters?id=2`

`GET` requests usually pass important info (in this case, the ID number of the monster in question) along in the URL, by passing parameters in the URL itself. *Usually* that info is used for looking stuff up, NOT writing stuff to the database. We generally never use `GET` as the protocol for writing stuff, because anyone with a web browser can just arbitrarily change that info. 

For example, you wouldn't do:

`monsterkillers.com/monsters/edit/?id=2&name=Boogeyman`

Because that means that I (as the person with the web browser) might be able to arbirtraily change the name of that Monster - and your app UI might not want that to happen. For example, if in the form that I should have been using, you had a dropdown menu that gave me three choices: `Sleestak`, `Yeti`, and `Bunyip`, I (as the person using the web browser to modify requests outside of your form, because I can because they are `GET` requests) can now potentially inject in whatever arbitrary value I want for the name of this monster. It means I could edit an existing monster's name, potentially create new monsters name whatever I want, etc. There are other things we can do to prevent bad/ignorant actors from doing stuff like this, but the easist one is to never let people modify data using a `GET` request. 

The entry in the `routes` file would probably look something like this:

```php
    Route::get('monsters', [
            'uses' => 'MonstersController@index'
    ]);
```

We can talk about indexes later - it's one of those words in tech that has 100 meanings. For our purposes here, it's a placeholder methd that would *probably* display a list of monsters. But the method name doesn't matter exactly yet.

#### `POST` requests:


This is the *most* common type of HTTP request for forms. For the reasons above, and a few more. In `POST` requests, if we're using a modern framework, something called a CSRF token is required. CSRF stands for Cross Site Request Forgery, and that token's job is to make sure that the form that's being posted to the "saving" endpoint on your system is actually authorized to do so. Most modern frameworks *require* a CSRF token, and thankfully they handle a lot of the implementation for you. Frameworks like Laravel, for example, require you to make an *exception* if you want to POST a form without a CSRF token. That's a good thing. That means that if you forget to implement one and try to test out your form, it won't work.

A typical "save" `POST` request for in your routes might look like this:

```php
    Route::post('monsters', [
            'uses' => 'MonstersController@create'
    ]);
```

#### URL-scoped Requests

Okay... so we;ve discussed things like `monsterkillers.com/monsters?id=2`, but what about `monsterkillers.com/monsters/2`. Given what you already know about web servers and directories, it wouldn't be weird to think that we make a new directory for every monster here, since that's kinda what the URL is showing us. 

If we didn't know anything past what we've already discussed, I'd have posited that our server directory structure looked something like this:

- public
  - /monsters
    - /1
    - /2
    - /3
    - /4
    
That's a really natural thing to assume, given what most folks know about web servers, but it wouldn't be correct (or practical) in this context. In most of what we do in a framework app, we're not dealing with the file system as much as the database, so when we create a new monster with an ID of 5, we're *not* writing new directories. We've added a new record to the database, and have used some of the framework magic to just make it look like we wrote new directories. 

So the next question is - wait, what? How is that possible? Everything I understood about URL paths implied directories and files. 

Bear with me - this sounds way more confusing than it is. 

You already learned about `GET` requests and `POST` requests. AND (most importantly) you learned that `GET` is for showing and `POST` is for changing. Which means that you should hopefully see by now that when we're trying to show a monster with an ID of 5, the request is *still* a `GET`, even though it doesn't have a bunch of extra stuff (like `monster?id=5`) tacked onto the URL.

Before we go there though, let's talk briefly about how your Controller methods actually access the form fields you submit in a post. In Laravel, we use the [Request object](https://laravel.com/docs/5.8/requests).

Before we get too crazy into the weeds, what you mostly need to know about the `Request` object in Laravel is that it gives you access to URL and/or form-submitted parameters. 

As far as the `Request` object goes, it mostly doesn't care if you're `GET`ing or `POST`ing. Its job is to look at the HTTP request, and give you info. That means that if values are passed through HTTP, whether that's via a `GET` request or a `POST`, your `Request` object can see those values and therefore you can access them. 

A base experiment with this would be to create a one-field form and a submit button, two routes (your `GET` route to *show* the form, and your `POST` route to handle the submitted data). You'll need to create at least one view blade here, but that's out of scope for this convo. Ping me if you need help.  

Let's make an HTML form field for the monster's name, called `name`, submit that form (which gets submitted to the route you defined for your `POST`, because we're now changing data) and in your `POST` form handler, let's also do this:


```php
    public function store(Request $request)
    {
        $monster = new Monster; // This is referring to the Monster model
        $monster->fill($request->all());
        \Log::debug($request); // <-- THIS will show you in your log files what was posted

        // if the save didn't work
        if (!$monster->save()) {
            return redirect()->with('errors', 'That save didn't work.');
        }
        
        // Saved worked. These situations typically need more nuance than this, but it's a good enough start.
        return redirect()->back()->with('success', 'Saved!');
    }
```

(The PHP method `print_r()` will dump your arrays and objects, and the optional variable `true` means display the contents, versus returning true or false.)

Now, tail your log files. On a linux/MacOS system, that means running:


```
tail -f storage/logs/laravel.log
```

And finally, submit your form with some value for your `name` HTML field.

That will show you whatever you entered into your `name` field, and some extra fields that always get included in a `Request`. Laravel doesn't actually care about whether it's a `GET` or a `POST`. Your *routes* do, and you define those, but as long as you include the `Request` object as a method parameter, you can have your way with it. 


#### Accessing `GET` and `POST` variables

Laravel in particular makes this really simple. In our above example, we had a single HTML field called `name`. To access this field in our controller, we would do something like this for our `show()` method in our `MonstersController`. This is a `GET` request, since we're just displaying a form, and that form - before it's submitted - doesn't actually *do* anything other provide an interface to submit data to your `POST`. 


```php
/**
** this one just shows the monster-create blade, which lives in resources/views/.
** It contains the form HTML that we will be POSTing to the next method.
**/

    public function create(Request $request)
    {
        return view('create-monster');
    }
```

And now the `POST` handler in the controller:


```php
    public function store(Request $request)
    {
        $monster = new Monster; // This is referring to the Monster model
        $monster->fill($request->all());

        // if the save didn't work
        if (!$monster->save()) {
            return redirect()->with('errors', 'That save didn't work.');
        }
        
        // Saved worked. These situations typically need more nuance than this, but it's a good enough start.
        return redirect()->back()->with('success', 'Saved!');
    }
```

### URL Path variables

Okay, so now we've addressed user-submitted values via `GET` and `POST`, and hopefully you understand the difference. There is one more use-case that is pretty common in frameworks, which we talked about above. The old `monsterkillers.com/monsters/2` thing. If we're not actually creating directories on the fly for each new monster, how are we passing that data to a route or a controller so that we can actually use it?

We do this via routes. 

In the routes you've seen above, everything is pretty straightforward. Hit a URL, get sent to a controller and then either return new data (usually via API only), return a blade view, or redirect the user. 

So in a  `monsterkillers.com/monsters/2` situation - since you're not passing anything via parameter (aka  `monsterkillers.com/monsters?id=2` and you're only *getting* data, not changing it - how are we even doing this?

The answer is route paramaters. 

If we want variables to be part of the URL *path* (`foo/bar/blah/baz`), not parameters we tack onto the "normal" URL (`?foo=1&bar=2`), we can use route parameters.

An example of our route for something like `monsterkillers.com/monsters/2` could look like this:


```php
    Route::get('monsters/{id}', [
            'uses' => 'MonstersController@show'
    ]);
```

This tells the application to look at `MonsterControllers.php`, and to look at the `show()` method within that `MonsterControllers.php` file, and to pass that method *something* that we assign the name `id`. We assigned that name in the route - and that name doesn't matter. Could be `Route::get('monsters/{id}'`, could be `Route::get('monsters/{farts}'`, could be `Route::get('monsters/{monsterId}'` - that doesn't matter - what matters is that we've made a route parameter, and the only way the Controller method knows what that value is is by what we call it in the route definition.

So if you decided to make that route:


```php
    Route::get('monsters/{blahblahblah}', [
            'uses' => 'MonstersController@show'
    ]);
```


And our Controller `show()` method that the above route request would be accessing would look like this:

```php
    public function show($id)
    {
        $monster = Monster::findOrFail($id);
        return $monster;
    }
```    



Your MonstersController `show()` method would look like this:

```php
    public function show($blahblahblah)
    {
        $monster = Monster::findOrFail($blahblahblah);
        return $monster;
    }
```    

The names don't matter (except for you as the person who has to remember what you're passing around). The *consistency* matters. 
