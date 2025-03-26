# Essential Laravel Commands Guide

## Project Setup & Maintenance

```bash
# Create new Laravel project
composer create-project laravel/laravel project-name

# Install dependencies
composer install

# Generate application key
php artisan key:generate

# Clear various caches
php artisan cache:clear
php artisan config:clear
php artisan route:clear
php artisan view:clear

# Optimize application
php artisan optimize
php artisan optimize:clear
```

## Development Server

```bash
# Start development server
php artisan serve

# Start server on specific host and port
php artisan serve --host=0.0.0.0 --port=8080
```

## Database Operations

```bash
# Run migrations
php artisan migrate

# Rollback migrations
php artisan migrate:rollback

# Refresh migrations (rollback all and migrate)
php artisan migrate:refresh

# Fresh migrations (drop all tables and migrate)
php artisan migrate:fresh

# Seed database
php artisan db:seed

# Create migration
php artisan make:migration create_table_name_table

# Create seeder
php artisan make:seeder TableNameSeeder

# Create factory
php artisan make:factory ModelNameFactory
```

## MVC Generation

```bash
# Create controller
php artisan make:controller ControllerName
php artisan make:controller ControllerName --resource

# Create model
php artisan make:model ModelName
php artisan make:model ModelName -m    # with migration
php artisan make:model ModelName -mfsc # with migration, factory, seeder, controller

# Create middleware
php artisan make:middleware MiddlewareName

# Create request
php artisan make:request RequestName
```

## Authentication & Authorization

```bash
# Create authentication scaffolding
php artisan make:auth

# Create policy
php artisan make:policy PolicyName

# Create gate
php artisan make:policy PolicyName --model=ModelName
```

## Queue & Jobs

```bash
# Create job class
php artisan make:job JobName

# Run queue worker
php artisan queue:work

# Run failed queue jobs
php artisan queue:failed

# Retry failed queue jobs
php artisan queue:retry all
```

## Testing

```bash
# Create test class
php artisan make:test TestName
php artisan make:test TestName --unit

# Run tests
php artisan test
php artisan test --filter=TestName
```

## Route & API

```bash
# List all routes
php artisan route:list

# Create API controller
php artisan make:controller API/ControllerName --api

# Create API resource
php artisan make:resource ResourceName
```

## Storage & Assets

```bash
# Create storage link
php artisan storage:link

# Publish vendor assets
php artisan vendor:publish
```

## Maintenance

```bash
# Put application into maintenance mode
php artisan down

# Bring application out of maintenance mode
php artisan up

# View current status
php artisan about

# Generate IDE helper files
php artisan ide-helper:generate
php artisan ide-helper:models
```

## Package Development

```bash
# Create package service provider
php artisan make:provider ServiceProviderName

# Create package command
php artisan make:command CommandName
```

## Tips

- Add `-h` or `--help` to any command to see available options
- Use `php artisan list` to see all available commands
- Use `php artisan help command-name` for detailed command information

Remember to run `composer dump-autoload` after creating new classes or modifying composer.json.