# Laravel + MS SQL Docker
### FOR DEVELOPMENT ENVIRONMENTS ONLY

Yes, you read it right: this is just intended to be used for Laravel apps development that require the php / mssql drivers to access a MS SQL Server instance. Please, **don't use it in production**. 

This is to be used to quickly setup a development environment with Laravel and MS SQL Server.

Just add the Docker related files in this repository at the root a Laravel (already sail based) project, then build the image, and *sail it up* as usual ;-) 
Prior to building, if you wish, make changes like the timezone by setting the environment:

    ENV TZ=America/Sao_Paulo

Or change the `docker-compose.yml` file to update, remove, add service containers as you wish. 
When ready, just run a build command:

    docker build --no-cache

and then you should be set to:

      ./vendor/bin/sail up

Here the default cointaners are :
- Ubuntu 22 *
- Redis
- Mailpit
- MS SQL Server 2022

(* This Ubuntu image is based on the same Dockerfile used and originaly set up in the [**Laravel Sail**](https://github.com/laravel/sail) package, so the app is served by a `php artisan serve` command, making it **unsuitable** for **production**. )
