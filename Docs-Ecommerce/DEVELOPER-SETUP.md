# Developer Setup Guide

This guide will help you set up your development environment for working on the e-commerce project.

## Prerequisites

Before starting, ensure you have the following installed:

- **PHP 8.2+**
- **Composer 2.x**
- **Node.js 18.x+** and **npm**
- **MySQL 8.x** or **PostgreSQL 14.x+**
- **Git**
- **Visual Studio Code** (recommended) or your preferred IDE

## Initial Setup

### 1. Clone the Repository

```bash
git clone <repository-url>
cd <project-directory>
```

### 2. Install PHP Dependencies

```bash
composer install
```

### 3. Install JavaScript Dependencies

```bash
npm install
```

### 4. Environment Configuration

```bash
cp .env.example .env
```

Open the `.env` file and configure:

```env
APP_NAME="E-Commerce Store"
APP_URL=http://ecommerce.test

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=ecommerce
DB_USERNAME=your_username
DB_PASSWORD=your_password
```

### 5. Generate Application Key

```bash
php artisan key:generate
```

### 6. Create Database

Create a new database matching the name in your `.env` file:

#### MySQL
```bash
mysql -u root -p
CREATE DATABASE ecommerce;
exit;
```

#### PostgreSQL
```bash
psql -U postgres
CREATE DATABASE ecommerce;
\q
```

### 7. Run Migrations and Seeders

```bash
php artisan migrate --seed
```

### 8. Storage Setup

```bash
php artisan storage:link
```

### 9. Build Frontend Assets

```bash
npm run dev
```

### 10. Start Development Server

```bash
php artisan serve
```

Your application should now be running at `http://localhost:8000`

## Development Workflow

### Database Changes

We use Blueprint to generate code from model definitions:

1. Edit the `draft.yaml` file to add or modify models
2. Run Blueprint to generate code:

```bash
php artisan blueprint:build
```

### Admin Panel

Filament admin panel is available at `/admin`. To create admin resources:

```bash
php artisan make:filament-resource ModelName
```

### API Development

1. Create controllers in `app/Http/Controllers/Api`
2. Define routes in `routes/api.php`
3. Create API resources for response transformation
4. Write tests for all endpoints

### Common Commands

```bash
# Run tests
php artisan test

# Clear cache
php artisan optimize:clear

# Watch assets during development
npm run dev

# Build assets for production
npm run build

# Run code style fixing
./vendor/bin/pint

# Generate IDE helper files
php artisan ide-helper:generate
php artisan ide-helper:models
php artisan ide-helper:meta
```

## Git Workflow

1. Create a new branch for your feature/fix:
```bash
git checkout -b feature/feature-name
```

2. Make your changes and commit them with descriptive messages:
```bash
git add .
git commit -m "Add feature X that does Y"
```

3. Push your branch to the remote repository:
```bash
git push origin feature/feature-name
```

4. Create a pull request on GitHub/GitLab

5. After code review and approval, your changes will be merged

## Coding Standards

- Follow [PSR-12](https://www.php-fig.org/psr/psr-12/) coding standards
- Use Laravel best practices and design patterns
- Write meaningful comments and documentation
- Include tests for new features

## Troubleshooting

### Common Issues

#### Composer Memory Limit

If you encounter memory limit errors with Composer:

```bash
COMPOSER_MEMORY_LIMIT=-1 composer require package-name
```

#### File Permissions

If you encounter file permission issues:

```bash
chmod -R 775 storage bootstrap/cache
```

#### Database Connection Issues

Ensure your database service is running and credentials are correct in `.env`.

### Getting Help

If you need assistance:

1. Check the project documentation
2. Ask in the team communication channel
3. Review Laravel documentation for framework-specific issues

## Additional Tools

### Recommended VS Code Extensions

- PHP Intelephense
- Laravel Blade Snippets
- Laravel Snippets
- Laravel Blade Formatter
- Laravel Artisan
- DotENV

### Browser Extensions

- Vue.js Devtools (if using Vue.js)
- Redux DevTools (if using Redux)
- Web Developer
- Postman (or use the desktop app)

## Security Best Practices

- Never commit sensitive credentials
- Always validate user input
- Use Laravel's built-in security features
- Follow OWASP security guidelines
- Report security vulnerabilities immediately

## Resources

- [Laravel Documentation](https://laravel.com/docs)
- [Filament Documentation](https://filamentphp.com/docs)
- [Blueprint Documentation](https://blueprint.laravelshift.com/docs)
- [Team Knowledge Base](link-to-internal-wiki) 