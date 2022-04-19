
# XPRSS

A fast, unopinionated, minimalist web framework for PHP.

The first version of this framework is a direct clone of [express-php](https://github.com/aeberdinelli/express-php), which tries to clone the NodeJS [ExpressJS framework](https://www.npmjs.com/package/express) writing style. It does not have an event loop, it just mimics the method and helpers names that ExpressJS offers.

## Install
**Note**: To run XPRSS you need PHP >= 7.0 and Apache.

The preferred installation is using Composer:

`composer require hxgf/xprss:0.0.3@dev`

Then, copy the .htaccess to the root of your site:

`cp vendor/hxgf/xprss/.htaccess ./.htaccess`

(if you don't want to copy, put this in a new .htaccess file)

```
RewriteEngine on
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^(.*)$ /index.php?route=$1 [L,QSA]
```

## Usage
If you installed using composer it's easy to initialize:

```php
<?php
include __DIR__.'/vendor/autoload.php';

use XPRSS\XPRSS;
use XPRSS\Router;

$xprss = new XPRSS();
$router = new Router();

$router->get('/', function($req, $res) {
	$res->send('hello world!');
});

$xprss->listen($router);
?>
```

## Routes
Routes are handled using a Router instance, for example:

```php
$router = new Router();
$router->get('/', function($req, $res) {
    // This will be called when someone goes to the main page using GET method.
});
```

You can handle post requests as well using post() instead of get(). Same for put() and delete().

## Route with dynamic parameters
You can route dynamic URL using parameters, for example:

```php
$router = new Router();
$router->get('/:something/:else', function($req, $res) {
    /**
     * Now let's imagine someone enters to URL: /hello/bye, then:
     *
     * $req->params->something will contain 'hello'
     * $req->params->else will contain 'bye'
     */
});
```

## Responses
If you're developing an API for example, you can send json simply doing:

```php
$router->post('/', function($req, $res) {
	$res->json(array(
		'error'		=> false,
		'message'	=> 'Hello'
	));
});
```

You can also send a custom http response code using:

```php
$router->post('/', function($req, $res) {
	$res->status(201)->json({
		'error'		=> false,
		'message'	=> 'Created!'
	});
});
```

**TIP**: There are a few more examples in the `index.php` file in this repository.

## Static files
If you wish to serve static files (likes images, html only) you can use:

```php
// If you visit /static/image.png, this will return the file views/public/image.png
$router->use('/static', $xprss->static('views/public'));
```

## Template engines
You're on your own for templating...at some point we'll have a guide for how to support various template engines (jade/pug, handlebars, twig, etc)

We'd like to to be as easy as:

```php
// Configure the engine to Pug
$xprss->set('view engine','pug');

// Jade was renamed to Pug, but we recognize it ;)
$xprss->set('view engine','jade');

// Or Mustache
$xprss->set('view engine','mustache');

// Set the path to the template files
$xprss->set('views','./views/pug');

// Now you can do something like this
$router->get('/', function($req, $res) {
	$res->render('index.jade');
});

// Or this
$router->get('/users/:username', function($req, $res) {
	$res->render('index.jade', array(
		'name'	=> $req->params->username
	));

	// Now in jade, you can use #{name} to get that variable!
});

```


## Request info
- You have the body of the request in $res->body no matter if you re handling POST or PUT.
- You have the query string under $req->query
- You have the cookies in $req->cookies
- You have all the request headers in $req->headers
