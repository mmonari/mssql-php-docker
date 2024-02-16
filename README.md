Dockerfile file to build the php 8.2 + ms sql server driver
Used within a private project to quickly build a development environment with Laravel and MS SQL Server.

Derived from the official php 8.2 image with a touch of "Laravel Sail" like features

Also included:
-> Composer 
-> Node.js

In the Dockerfile change the timezone by setting the environment variable TZ to the desired timezone, for example:
ENV TZ=America/Sao_Paulo

In docker-compose.yml, change/add services as you wish. The default services are:
-> redis
-> mailpit

Suggestion: add MSSQL as a container as well. 