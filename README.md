# Serving Laravel App in Vercel

Add `api` folder under the root project after that we have to add `index.php` file under the `api/` folder.

```php
<?php

/**
 * Here is the serverless function entry
 * for deployment with Vercel.
 */
require __DIR__ . '/../public/index.php';
```

It will forward all of the request from vercel to the `index.php`.

After that create `vercel.json` under the root directory.

```json
{
    "version": 2,
    "functions": {
        "api/index.php": {
            "runtime": "vercel-php@0.6.0"
        }
    },
    "routes": [
        {
            "src": "/(.*)",
            "dest": "/api/index.php"
        }
    ],
    "outputDirectory": "public/build",
    "env": {
        "APP_NAME": "Vercel Laravel",
        "APP_ENV": "production",
        "APP_KEY": "base64:CmdwjUo++yDpyhSlx78ZXQi3wEzdYNC3R8jAV0sbNqk=",
        "APP_DEBUG": "false",
        "APP_URL": "https://laravel-vercel-sooty.vercel.app",
        "VERCEL_DEMO_MODE": "true",
        "APP_CONFIG_CACHE": "/tmp/config.php",
        "APP_EVENTS_CACHE": "/tmp/events.php",
        "APP_PACKAGES_CACHE": "/tmp/packages.php",
        "APP_ROUTES_CACHE": "/tmp/routes.php",
        "APP_SERVICES_CACHE": "/tmp/services.php",
        "CACHE_DRIVER": "array",
        "LOG_CHANNEL": "stderr",
        "SESSION_DRIVER": "cookie",
        "VIEW_COMPILED_PATH": "/tmp/views",
        "SSR_TEMP_PATH": "/tmp/ssr",
        "NODE_PATH": "node"
    }
}
```

## version

Version what the verson of schema version of vercel json file.

## functions

Functions are where lambda funciton executed in vercel platform. In our app `vercel-php` function will run on the server.

## routes

Route is where vercel forward the request to the specific destination that we defined in the route object `dest`. Vercel will forward every request which is matched `(.*)` to the `/api/index.php` file.

## outputDirectory

Output directory is where nodejs dist file with custom name. In **Laravel** dist files are under `public/build` so we defined like so.

## env

Env is where our environment variables from the `.env` file.

**Note**: Don't forgot to add `APP_KEY` in the `env` and `APP_DEBUG` need to be `false` in production.

# Changing RouteServiceProvider.php

To serve api in laravel, we always request to the `/api` endpoint. However, in vercel the `api` is the folder name of **vercel api/** directory. So, we have to remove that `api` prefix from our route service provider. If dont' so, we have to request like that.

```
www.example.com/api -> www.example.com/api/api
                                        ^
                                        |
                                        |_ api folder of our app
```

So, change to the following style. We **have to** omit `api` prefix to avoid the above problem.
```php
 Route::middleware('api')
                ->prefix('v1')
                ->group(base_path('routes/api.php'));
```

## Endpoints of this app

[Larvel in Vercel](https://laravel-in-vercel.vercel.app/v1/ping)