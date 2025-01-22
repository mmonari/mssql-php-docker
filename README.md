# Laravel + MS SQL Docker Setup  
**FOR DEVELOPMENT ENVIRONMENTS ONLY**

This repository provides a Docker setup for developing Laravel applications that require PHP/MS SQL drivers to access a Microsoft SQL Server instance. Please note, this configuration is intended strictly for development purposes and **should not** be used in production environments.

## Overview

This setup allows you to quickly spin up a development environment for Laravel applications with MS SQL Server and PHP 8.3 support. It builds upon a Laravel Sail-based project and includes additional containers to support services such as Redis, Mailpit, and MS SQL Server 2022.

## Prerequisites

- A Laravel application (Sail-based).
- Docker and Docker Compose installed on your development machine.

## Quick Setup

1. **Add Docker Files**  
   Copy the Docker-related files from this repository into the root of your Laravel project.

2. **Optional Configuration Changes**  
   Before building the Docker image, you may modify the environment variables, such as setting a custom timezone. For example:

   ```bash
   ENV TZ=America/Sao_Paulo
   ```

   You can also customize the `docker-compose.yml` file to update, add, or remove service containers according to your needs.

3. **Build the Docker Image and Start the Containers**  
   In you first 'sail up', ensure to build the image, so run the following command:

   ```bash
   ./vendor/bin/sail up --build
   ```

## Default Containers

The default setup includes the following services:

- **Ubuntu 24.04** (Base environment from the official Laravel Sail Dockerfile)
- **Redis** (For caching)
- **Mailpit** (For email testing)
- **MS SQL Server 2022** (Database)

> Note: The application is served via the `php artisan serve` command, which is not suitable for production environments. The setup mirrors Laravel Sail’s original configuration, which is tailored for development.

## Customization

Feel free to modify the `docker-compose.yml` file to suit your development needs. You can add or remove services, change configurations, or integrate additional tools.

### Example: Adding a MySQL Service

If you wish to add MySQL alongside MS SQL Server, you can update the `docker-compose.yml` to include a MySQL container:

```yaml
services:
  mysql:
    image: mysql:8.0
    ports:
      - "3306:3306"
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: laravel
      MYSQL_USER: user
      MYSQL_PASSWORD: password
```

## Important Notes

- This setup is intended for local development only. Do not use it in production.
- The Ubuntu container is configured for development and serves the Laravel application using the `php artisan serve` command, which is not performant or secure for production environments.
  
## Troubleshooting

If you encounter issues while building or starting the containers, here are a few tips:

- Ensure Docker is running and that your Docker Compose file is properly configured.
- Check for any port conflicts with other services running on your machine (such as MS SQL or Redis).
- Use `docker-compose logs` to view logs for individual containers.

You might encounter an issue with the self-signed DB server certificate.
Since this is intended only for development, you might just add the following in your config/database.php 'sqlsrv' entry:
 
```php
 'trust_server_certificate' => env('DB_TRUST_SERVER_CERTIFICATE', 'true'),
 ```
So the 'sqlsrv' entry would be like:

```php
        'sqlsrv' => [
            'driver' => 'sqlsrv',
            'url' => env('DB_URL'),
            'host' => env('DB_HOST', 'localhost'),
            'port' => env('DB_PORT', '1433'),
            'database' => env('DB_DATABASE', 'laravel'),
            'username' => env('DB_USERNAME', 'root'),
            'password' => env('DB_PASSWORD', ''),
            'charset' => env('DB_CHARSET', 'utf8'),
            'prefix' => '',
            'prefix_indexes' => true,
            // 'encrypt' => env('DB_ENCRYPT', 'yes'),
            'trust_server_certificate' => env('DB_TRUST_SERVER_CERTIFICATE', 'true'),
        ],
```
## Conclusion

This Docker setup provides a convenient way to develop Laravel applications that require MS SQL Server support. With minimal configuration, you can spin up a fully functional development environment tailored to your needs.

---
