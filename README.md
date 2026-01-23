# Laravel + MS SQL Docker Setup
**FOR DEVELOPMENT ENVIRONMENTS ONLY**

This repository provides a streamlined Docker setup for developing Laravel applications that require PHP/MS SQL drivers to access a Microsoft SQL Server instance. Please note, this configuration is intended strictly for development purposes and **should not** be used in production environments.

## Overview

This setup allows you to quickly spin up a development environment for Laravel applications with MS SQL Server and PHP 8.4 support. It provides a minimal, focused configuration with just the essential services needed for Laravel development with SQL Server connectivity.

## PHP Version Support

This repository provides two PHP versions, both using **official Microsoft SQL Server drivers**:

### PHP 8.4 (Default - Recommended)
- Uses official Microsoft ODBC Driver 18 and PHP extensions (`sqlsrv`, `pdo_sqlsrv`)
- Multi-stage Dockerfile build for cleaner layering
- **Current default configuration**

### PHP 8.3 (Alternative)
- Uses official Microsoft ODBC Driver 18 and PHP extensions (`sqlsrv`, `pdo_sqlsrv`)
- Located in the `docker/8.3` directory

**To use PHP 8.3 instead:**
1. Update your `docker-compose.yml`:
   ```yaml
   services:
     laravel.test:
       build:
         context: './docker/8.3'  # Change from './docker/8.4'
         dockerfile: Dockerfile
         args:
           WWWGROUP: '${WWWGROUP:-1000}'
       image: 'mssql-php-8.3/app'  # Change from 'mssql-php-8.4/app'
   ```
2. Rebuild your containers:
   ```bash
   ./vendor/bin/sail build
   ```

## Prerequisites

- A Laravel application (Sail-based recommended)
- Docker and Docker Compose installed on your development machine

## Quick Setup

1. **Add Docker Files**
   Copy the Docker-related files from this repository into the root of your Laravel project.

2. **Optional Configuration Changes**
   Before building the Docker image, you may modify environment variables. For example, set a custom timezone:

   ```bash
   ENV TZ=America/Sao_Paulo
   ```

   You can also customize the `docker-compose.yml` file to update ports, environment variables, or add additional services according to your needs.

3. **Build the Docker Image and Start the Containers**
   In your first run, ensure to build the image, so run the following command:

   ```bash
   ./vendor/bin/sail up --build
   ```

   For subsequent runs, you can use:

   ```bash
   ./vendor/bin/sail up
   ```

## Default Containers

The default setup includes the following services:

- **Laravel Application** (Ubuntu 24.04 with PHP 8.4, Node.js, Composer, and all necessary extensions)
- **MS SQL Server 2022** (Database server with Developer edition)

## Environment Variables

Create a `.env` file in your project root with the following variables:

```env
# Application
APP_PORT=80
VITE_PORT=5173

# Database
DB_CONNECTION=sqlsrv
DB_HOST=sqlsrv
DB_PORT=1433
DB_DATABASE=laravel
DB_USERNAME=sa
DB_PASSWORD=YourStrong@Passw0rd

# Docker
WWWGROUP=1000
WWWUSER=1000

# Forwarded Ports
FORWARD_DB_PORT=1433
```

## Database Configuration

Update your `config/database.php` to include the SQL Server connection:

```php
'sqlsrv' => [
    'driver' => 'sqlsrv',
    'host' => env('DB_HOST', 'sqlsrv'),
    'port' => env('DB_PORT', '1433'),
    'database' => env('DB_DATABASE', 'laravel'),
    'username' => env('DB_USERNAME', 'sa'),
    'password' => env('DB_PASSWORD', ''),
    'charset' => env('DB_CHARSET', 'utf8'),
],
```

## Customization

Feel free to modify the `docker-compose.yml` file to suit your development needs. You can:

- Change port mappings
- Add environment variables
- Modify volume mounts
- Add additional services (Redis, Mailpit, etc.)

### Example: Adding Redis

If you wish to add Redis alongside SQL Server, update the `docker-compose.yml`:

```yaml
services:
  redis:
    image: redis:alpine
    ports:
      - "6379:6379"
    volumes:
      - redis_data:/data
    networks:
      - sail

volumes:
  redis_data:
    driver: local
```

## Troubleshooting

If you encounter issues while building or starting the containers:

- **Build Issues**: Ensure Docker has sufficient resources and your system meets the requirements
- **Connection Issues**: Verify your `.env` database configuration matches the container settings
- **Port Conflicts**: Check that the specified ports aren't already in use by other services
- **Permission Issues**: Ensure your user has Docker permissions and the project directory is accessible

### Common Issues

- **SQL Server Connection**: Make sure the `DB_HOST` is set to `sqlsrv` (the container name)
- **Password Requirements**: SQL Server requires a strong password with uppercase, lowercase, numbers, and symbols
- **Memory Usage**: SQL Server container requires adequate RAM (minimum 2GB recommended)

## Architecture

The setup uses:

- **Microsoft ODBC Driver 18**: Official Microsoft driver for SQL Server connectivity
- **PHP Extensions**: `sqlsrv` and `pdo_sqlsrv` installed via PECL
- **Ubuntu 24.04**: Base image with PHP 8.4 from Ondrej's PPA
- **Microsoft SQL Server 2022**: Official container image with Developer edition
- **Laravel Sail Pattern**: Familiar development workflow for Laravel developers

## Development Workflow

1. Make changes to your Laravel application
2. Run `sail up` to start containers
3. Access your application at `http://localhost`
4. Use `sail artisan` for Laravel commands
5. Use `sail composer` for Composer commands
6. Use `sail npm` for Node.js commands

## Security Notes

- This setup is for development only
- SQL Server runs with `ACCEPT_EULA=Y` for convenience
- Default passwords should be changed for any exposed deployments
- No production hardening is applied

## Contributing

Feel free to submit issues and enhancement requests!

---

**Note**: This setup is optimized for Laravel development with SQL Server. For production deployments, use proper infrastructure with security hardening, backups, and monitoring.
