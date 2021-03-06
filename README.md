# Reauthenticate

## Because sometimes, you want that extra layer of security

[![Software License](https://img.shields.io/badge/license-MIT-brightgreen.svg?style=flat)](LICENSE.md)

Reauthenticate users by letting them re-enter their passwords for specific parts of your app (for Laravel 5).

```php
Route::group(['middleware' => ['auth','reauthenticate']], function () {

    Route::get('user/payment', function () {
        // Needs to re-enter password to see this
    });

});
```

## Note 
This package is a fork of [mpociot/reauthenticate](https://github.com/mpociot/reauthenticate), with two extra features and the readme instructions updated to Laravel 5.5+ which is the new LTS version of Laravel.

**I do not take credit for this work, I simply forked it and modified it.** A PR was submitted but not addressed, thus, this fork was published to packagist.

## Contents

- [Installation](#installation)
- [Usage](#usage)
- [Configuration](#configuration)
- [License](#license)

## Installation
<a name="installation" id="installation"></a>
In order to add reauthenticate to your project, just  run the following command in terminal:

```
composer require madmikeyb/reauthenticate
```

## Usage
<a name="usage" id="usage"></a>
### Add the middleware to your Kernel

In your `app\Http\Kernel.php` file, add the reauthenticate middleware to the `$routeMiddleware` array.

```php
protected $routeMiddleware = [
    // ...
    'reauthenticate'         => \Mpociot\Reauthenticate\Middleware\Reauthenticate::class,
    // ...
];
```

### Add the routes & views

By default, reauthanticate is looking for a route `auth/reauthenticate` and a view `auth.reauthenticate` that will hold a password field.

An example view can be copied from [here](https://github.com/mpociot/reauthenticate/blob/master/views/reauthenticate.blade.php). Please note that this file needs to be manually copied, because I didn't want to bloat this package with a service provider.

The HTTP controller methods can be used from the `Reauthenticates` trait, so your AuthController looks like this:

```php
<?php

namespace App\Http\Controllers\Auth;

use App\Http\Controllers\Controller;
use Mpociot\Reauthenticate\Reauthenticates;
use Illuminate\Foundation\Auth\AuthenticatesUsers;

class LoginController extends Controller
{
    /*
    |--------------------------------------------------------------------------
    | Login Controller
    |--------------------------------------------------------------------------
    |
    | This controller handles authenticating users for the application and
    | redirecting them to your home screen. The controller uses a trait
    | to conveniently provide its functionality to your applications.
    |
    */
    use AuthenticatesUsers, Reauthenticates;
```

Be sure to except the reauthenticate routes from the `guest` middleware.

```php
    /**
     * Create a new authentication controller instance.
     *
     * @return void
     */
    public function __construct()
    {
        $this->middleware('guest', ['except' => ['logout','getReauthenticate','postReauthenticate'] ]);
    }
```

To get started, add these routes to your `routes.php` file:

```php
// Reauthentication routes
Route::namespace('Auth')->group(function () {
    Route::get('auth/reauthenticate', 'LoginController@getReauthenticate');
    Route::post('auth/reauthenticate', 'LoginController@postReauthenticate');
});
```

That's it.

Once the user successfully reauthenticates, the valid login will be stored for 30 minutes by default.

## Optional Configuration
<a name="configuration" id="configuration"></a>

Reauthenticate can be (optionally) configured through your `config/app.php` file. The following keys are supported:

```php
return [
    /*
    |--------------------------------------------------------------------------
    | Reauthenticate
    |--------------------------------------------------------------------------
    */
    
    /**
     * The URL to redirect to after re-authentication is successful.
     */
    'reauthenticate_url' => '/custom-url',

    /**
     * The key to use as your re-authentication session key.
     */
    'reauthenticate_key' => 'custom-reauthentication-key',

    /**
     * The time (in minutes) that the user is allowed to access the protected area 
     * before being prompted to re re-authenticate.
     */
    'reauthenticate_time' => 30,
];
```

## License

Reauthenticate is free software distributed under the terms of the MIT license.
