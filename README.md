# PHP8.2+MS SQL Dockerfile

Used within a private project to quickly build a development environment with Laravel and MS SQL Server.

Derived from the official php 8.2 image, touched by "Laravel Sail" like features, and is total compatible to sail environment. 
Just add the Docker related files in this repository at the root of your project, build the image, and *sail it up* as usual ;-) 

	    docker build --no-cache
      ./vendor/bin/sail up

In this container I've  included:
 1. Composer
 2. Node.js

In the Dockerfile don't forget to change the timezone by setting the environment variable TZ to the desired timezone, for example:

    ENV TZ=America/Sao_Paulo

In this current docker-compose.yml, also change/add services as you wish. Default extra containers loaded are:

    redis and mailpit

Personal suggestion: add a MSSQL as a container since you are at it as well :-D.

Enjoy, and happy coding. 
