# Developer Setup Guide - Multi-Vendor E-Commerce Marketplace

## Project Architecture Overview

This multi-vendor marketplace implements a three-tier user interface architecture:

1. **Customer Frontend** - Vue.js Single Page Application
   - Customer-facing storefront
   - Accessed via root URL `/`
   - Product browsing, cart, checkout, and account management

2. **Vendor Panel** - Filament Admin Panel
   - Vendor dashboard and management interface
   - Accessed via `/vendor`
   - Product management, order fulfillment, earnings tracking

3. **Admin Panel** - Filament Admin Panel
   - Marketplace administration dashboard
   - Accessed via `/admin`
   - Vendor management, commission control, reporting

This separation ensures clean code organization, proper access control, and optimized user experiences for each user type.

## Prerequisites

Before setting up the development environment, ensure you have the following installed:

- PHP 8.2+
- Composer 2.0+
- Node.js 18+ and npm
- MySQL 8.0+ or PostgreSQL 14+
- Git
- Docker and Docker Compose (optional, for containerized development)

## Environment Setup

### 1. Clone the Repository

```bash
git clone https://github.com/your-organization/marketplace.git
cd marketplace
```

### 2. Backend Setup (Laravel)

#### Install Dependencies

```bash
composer install
```

#### Environment Configuration

```bash
cp .env.example .env
php artisan key:generate
```

Edit the `.env` file to configure database connections and other environment-specific settings:

```
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=marketplace
DB_USERNAME=root
DB_PASSWORD=your_password

# AWS S3 configuration for file storage
AWS_ACCESS_KEY_ID=your_key_id
AWS_SECRET_ACCESS_KEY=your_secret
AWS_DEFAULT_REGION=us-west-2
AWS_BUCKET=your-marketplace-bucket
AWS_USE_PATH_STYLE_ENDPOINT=false

# Mail configuration
MAIL_MAILER=smtp
MAIL_HOST=mailhog
MAIL_PORT=1025
MAIL_USERNAME=null
MAIL_PASSWORD=null
MAIL_ENCRYPTION=null

# Queue configuration (for background jobs)
QUEUE_CONNECTION=redis
REDIS_HOST=127.0.0.1
REDIS_PASSWORD=null
REDIS_PORT=6379
```

#### Database Migration

Create the database:

```bash
mysql -u root -p
CREATE DATABASE marketplace;
EXIT;
```

Run migrations and seeders:

```bash
php artisan migrate
php artisan db:seed
```

### 3. Filament Admin/Vendor Panel Setup

Filament powers both the admin and vendor panels with separate configurations.

#### Install Filament

```bash
composer require filament/filament
```

#### Create Multiple Panels

```bash
# Install Filament with panels support
php artisan filament:install --panels

# Create the admin panel
php artisan make:filament-panel admin
php artisan filament:panel admin --register

# Create the vendor panel
php artisan make:filament-panel vendor
php artisan filament:panel vendor --register
```

#### Configure Panel Middleware

Edit the panel provider files to add proper middleware:

```php
// app/Providers/Filament/AdminPanelProvider.php
return $panel
    ->default(false)
    ->id('admin')
    ->path('admin')
    ->login()
    ->middleware([
        'web',
        Authenticate::class,
        'role:admin,super_admin',
    ]);
```

```php
// app/Providers/Filament/VendorPanelProvider.php
return $panel
    ->default(false)
    ->id('vendor')
    ->path('vendor')
    ->login()
    ->middleware([
        'web',
        Authenticate::class,
        'role:vendor',
    ]);
```

#### Generate Resources

```bash
# Admin panel resources
php artisan make:filament-resource Product --panel=admin
php artisan make:filament-resource Shop --panel=admin
php artisan make:filament-resource Order --panel=admin
php artisan make:filament-resource User --panel=admin
php artisan make:filament-resource Withdrawal --panel=admin

# Vendor panel resources
php artisan make:filament-resource Product --panel=vendor
php artisan make:filament-resource Order --panel=vendor
php artisan make:filament-resource Shop --panel=vendor --generate --simple
php artisan make:filament-resource Withdrawal --panel=vendor --generate --simple
```

#### Create Custom Dashboards

```bash
# Admin dashboard
php artisan make:filament-page Dashboard --panel=admin --type=Dashboard

# Vendor dashboard
php artisan make:filament-page Dashboard --panel=vendor --type=Dashboard
```

### 4. Customer Frontend Setup (Vue.js)

#### Install Vue.js with Vite

```bash
npm install
```

#### Configure Environment

Create a `.env.local` file in the frontend root:

```
VITE_API_URL=http://localhost:8000/api/v1
```

#### Development Server

To start the development server:

```bash
npm run dev
```

### 5. Multi-Vendor Specific Configuration

#### Configure User Roles and Permissions

Update the authorization policies in `app/Policies` to handle vendor-specific permissions:

```php
// app/Policies/ProductPolicy.php
public function viewAny(User $user)
{
    return true; // Everyone can view products
}

public function create(User $user)
{
    return $user->role === 'vendor' || $user->role === 'admin'; // Only vendors and admins can create
}

public function update(User $user, Product $product)
{
    // Vendors can only update their own products, admins can update any
    return ($user->role === 'vendor' && $product->shop->user_id === $user->id) 
        || $user->role === 'admin';
}
```

#### Setup Vendor Registration Flow

Enable vendor registration in the `.env` file:

```
ALLOW_VENDOR_REGISTRATION=true
VENDOR_REQUIRES_APPROVAL=true
DEFAULT_COMMISSION_RATE=10
```

Create a vendor registration controller:

```php
// app/Http/Controllers/VendorRegistrationController.php
public function register(VendorRegistrationRequest $request)
{
    $user = User::create([
        'name' => $request->name,
        'email' => $request->email,
        'password' => Hash::make($request->password),
        'role' => 'customer', // Initially a customer
    ]);

    $shop = Shop::create([
        'user_id' => $user->id,
        'name' => $request->shop_name,
        'slug' => Str::slug($request->shop_name),
        'description' => $request->shop_description,
        'status' => 'pending', // Requires approval
        'commission_rate' => config('marketplace.default_commission_rate', 10),
    ]);

    // Notify admins about new vendor application
    Notification::send(
        User::where('role', 'admin')->get(),
        new NewVendorApplication($shop)
    );

    return response()->json([
        'message' => 'Vendor application submitted successfully.',
        'status' => 'pending',
    ]);
}
```

#### Configure Commission System

Set up commission calculations in `app/Services/CommissionService.php`:

```php
// app/Services/CommissionService.php
public function calculateCommission(Order $order)
{
    foreach ($order->items as $item) {
        $shop = $item->product->shop;
        $commissionRate = $shop->commission_rate / 100;
        
        $item->commission_amount = $item->subtotal * $commissionRate;
        $item->save();
    }
    
    $order->commission_calculated = true;
    $order->save();
    
    return $order;
}
```

#### Setup Withdrawal System

Configure withdrawal limits and methods in `config/marketplace.php`:

```php
return [
    'withdrawals' => [
        'minimum_amount' => 50,
        'processing_days' => 3,
        'methods' => [
            'bank_transfer' => true,
            'paypal' => true,
            'stripe' => true,
        ],
    ],
];
```

### 6. Docker Setup (Optional)

For containerized development, use Docker Compose:

```bash
docker-compose up -d
```

This will start the following services:
- PHP/Laravel (Application)
- MySQL (Database)
- Redis (Cache/Queue)
- Mailhog (Email Testing)
- Node.js (Frontend)

### 7. Testing Setup

#### Configure Testing Environment

```bash
cp .env.example .env.testing
```

Edit `.env.testing` for testing-specific configurations.

#### Run Tests

```bash
php artisan test
```

For frontend tests:

```bash
npm run test
```

## Local Development

### Starting the Development Servers

To start the Laravel development server:

```bash
php artisan serve
```

To start the frontend development server:

```bash
npm run dev
```

### Creating an Admin Account

```bash
php artisan make:filament-user
```

When prompted, select the admin panel and set the user role to admin.

### Creating a Vendor Account

You can create a vendor account through the registration page or use the artisan command:

```bash
php artisan make:filament-user --panel=vendor
```

To approve a vendor through the admin panel, log in as an admin and update the shop status to 'active'.

### Accessing Different Panels

- Customer Frontend: `http://localhost:8000/`
- Admin Panel: `http://localhost:8000/admin`
- Vendor Panel: `http://localhost:8000/vendor`

## Common Issues and Solutions

### CORS Issues

If you encounter CORS issues when making API requests from the frontend:

1. Check that your `config/cors.php` includes the frontend URL:

```php
'allowed_origins' => [env('FRONTEND_URL', 'http://localhost:3000')],
```

2. Ensure the CORS middleware is enabled in `app/Http/Kernel.php`.

### Filament Panel Access Issues

If you can't access the Filament panels:

```bash
php artisan optimize:clear
php artisan filament:upgrade
```

If panels aren't correctly registering:

```php
// Check app/Providers/AppServiceProvider.php to ensure panels are registered
public function boot(): void
{
    Filament::registerPanel(
        \App\Providers\Filament\AdminPanelProvider::class
    );
    
    Filament::registerPanel(
        \App\Providers\Filament\VendorPanelProvider::class
    );
}
```

### Database Migration Errors

If migrations fail:

```bash
php artisan migrate:fresh
```

**CAUTION**: This will drop all tables and run all migrations from scratch.

## Contribution Guidelines

### Branching Strategy

- `main`: Production-ready code
- `develop`: Development branch
- `feature/feature-name`: For new features
- `fix/issue-name`: For bug fixes

### Pull Request Process

1. Create a branch from `develop`
2. Make your changes
3. Run tests: `php artisan test && npm run test`
4. Create a pull request to `develop`
5. Get at least one code review

### Code Style

We use Laravel Pint for PHP code style:

```bash
./vendor/bin/pint
```

For JavaScript/Vue, use ESLint:

```bash
npm run lint
```

## Deployment

### Staging Environment

The staging environment is automatically deployed when changes are merged into the `develop` branch.

### Production Environment

Production is deployed when changes are merged into the `main` branch after successful testing in staging.

### Environment Variables for Production

Ensure the following environment variables are set for production:

```
APP_ENV=production
APP_DEBUG=false
LOG_LEVEL=warning
```

## Documentation

- API Documentation: `http://localhost:8000/api/documentation`
- Laravel Documentation: [https://laravel.com/docs](https://laravel.com/docs)
- Filament Documentation: [https://filamentphp.com/docs](https://filamentphp.com/docs)
- Vue.js Documentation: [https://vuejs.org/guide](https://vuejs.org/guide)

## Support

For developer support, please contact:
- Email: dev-support@marketplace.com
- Slack: #dev-marketplace-team 