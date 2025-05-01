# E-Commerce Project Folder Structure - Multi-Vendor Marketplace

## Overview
This document outlines the recommended folder structure for your multi-vendor e-commerce marketplace using Laravel (backend), Vue.js (frontend for customer-facing app), and Filament (for admin and vendor panels).

## User Interface Architecture

Our multi-vendor marketplace implements a clear separation between interfaces for different user types:

1. **Admin Panel**: A Filament-powered dashboard accessed via `/admin` that provides marketplace administrators with comprehensive management tools, reporting, and vendor oversight.

2. **Vendor Panel**: A separate Filament-powered dashboard accessed via `/vendor` that gives vendors tools to manage their shops, products, orders, and finances.

3. **Customer Frontend**: A Vue.js-based storefront accessed via the root URL `/` where customers browse products, interact with vendor shops, and make purchases.

### Benefits of This Separation:

- **Security**: Admin and vendor functionality requires different permissions and access levels
- **User Experience**: Each user type has optimized interfaces for their specific needs
- **Performance**: Customer frontend can be optimized for shopping experience
- **Scalability**: Different parts of the application can be scaled independently
- **Maintenance**: Updates to one interface won't affect others

## Root Project Structure

```
ecommerce-project/
├── app/                      # Laravel backend code
│   ├── Filament/             # Filament admin/vendor panel configuration
│   │   ├── Resources/        # Filament resources
│   │   │   ├── ProductResource/
│   │   │   ├── OrderResource/
│   │   │   ├── CategoryResource/
│   │   │   ├── UserResource/
│   │   │   └── ShopResource/          # Vendor shop resource
│   │   ├── Widgets/          # Filament dashboard widgets
│   │   │   ├── AdminWidgets/         # Admin-specific widgets
│   │   │   └── VendorWidgets/        # Vendor-specific widgets
│   │   ├── Pages/            # Custom Filament pages
│   │   │   ├── VendorDashboard.php   # Vendor dashboard
│   │   │   └── AdminDashboard.php    # Admin dashboard
│   │   └── Resources.php     # Resource registration
├── bootstrap/                # Laravel bootstrap files
├── config/                   # Laravel configuration files
├── database/                 # Laravel database migrations/seeds
├── public/                   # Publicly accessible files
│   ├── index.php             # Laravel entry point
│   └── api/                  # API documentation
├── resources/                # Frontend resources
│   ├── js/                   # Vue.js frontend code (customer-facing)
│   │   ├── components/       # Vue components
│   │   │   ├── ui/           # UI components
│   │   │   ├── layout/       # Layout components
│   │   │   ├── product/      # Product components
│   │   │   ├── cart/         # Cart components
│   │   │   ├── checkout/     # Checkout components
│   │   │   └── shop/         # Vendor shop components
│   │   ├── pages/            # Vue page components
│   │   │   ├── Home.vue      # Homepage
│   │   │   ├── Shop.vue      # Product listing page
│   │   │   ├── Shops.vue     # Vendors listing page
│   │   │   ├── ShopDetail.vue# Vendor shop page
│   │   │   ├── ProductDetail.vue
│   │   │   ├── Cart.vue      # Cart page
│   │   │   ├── Checkout.vue  # Checkout page
│   │   │   └── Account/      # Account pages
│   │   │       ├── Dashboard.vue
│   │   │       ├── Orders.vue
│   │   │       ├── BecomeVendor.vue  # Vendor registration
│   │   │       └── Vendor/    # Customer portal vendor application
│   │   ├── store/            # Vuex store
│   │   ├── router/           # Vue Router configuration
│   │   ├── services/         # API services
│   │   ├── utils/            # Utility functions
│   │   ├── assets/           # Frontend assets
│   │   ├── App.vue           # Root Vue component
│   │   └── app.js            # Vue application entry point
│   ├── css/                  # CSS/SCSS styles
│   └── views/                # Laravel Blade templates
│       ├── app.blade.php     # Customer SPA entry point
│       ├── admin/            # Admin panel Blade templates (Filament)
│       └── vendor/           # Vendor panel Blade templates (Filament)
├── routes/                   # Laravel route definitions
│   ├── web.php               # Web routes
│   ├── api.php               # API routes
│   └── vendor.php            # Vendor-specific routes
├── storage/                  # Laravel storage
├── tests/                    # Test files
├── vendor/                   # Composer dependencies
├── node_modules/             # NPM dependencies
├── .env                      # Environment variables
├── .gitignore                # Git ignore file
├── composer.json             # PHP dependencies
├── package.json              # JavaScript dependencies
├── vite.config.js            # Vite configuration
└── README.md                 # Project documentation
```

## Filament Admin & Vendor Panel Structure

Filament organizes the admin and vendor panels using Resources, Pages, and Widgets with separate vendor and admin sections:

```
app/Filament/
├── Resources/                # Filament resources (CRUD interfaces)
│   ├── ProductResource/      # Product resource
│   │   ├── Pages/
│   │   └── ProductResource.php
│   ├── ShopResource/         # Vendor shop resource
│   │   ├── Pages/
│   │   │   ├── CreateShop.php
│   │   │   ├── EditShop.php
│   │   │   ├── ListShops.php
│   │   │   └── ViewShop.php
│   │   ├── RelationManagers/
│   │   │   └── ProductsRelationManager.php
│   │   └── ShopResource.php
│   ├── OrderResource/        # Order resource
│   │   ├── Pages/
│   │   ├── RelationManagers/
│   │   └── OrderResource.php
│   ├── WithdrawalResource/   # Vendor withdrawal requests
│   │   ├── Pages/
│   │   └── WithdrawalResource.php
│   ├── CategoryResource.php  # Category resource
│   └── UserResource.php      # User management resource
├── Panels/                   # Multiple panel configuration
│   ├── AdminPanel.php        # Admin panel configuration
│   └── VendorPanel.php       # Vendor panel configuration
├── Pages/                    # Custom admin pages
│   ├── AdminDashboard.php    # Admin dashboard
│   ├── VendorDashboard.php   # Vendor dashboard
│   ├── Settings.php          # Settings page
│   └── Reports/              # Report pages
│       ├── SalesReport.php
│       ├── VendorReport.php
│       ├── CommissionReport.php
│       └── InventoryReport.php
├── Widgets/                  # Dashboard widgets
│   ├── AdminWidgets/         # Admin-specific widgets
│   │   ├── TotalSales.php
│   │   ├── NewVendors.php
│   │   ├── PendingApprovals.php
│   │   └── CommissionEarned.php
│   └── VendorWidgets/        # Vendor-specific widgets
│       ├── VendorSales.php
│       ├── ProductInventory.php
│       ├── PendingOrders.php
│       └── EarningsWidget.php
└── Policies/                 # Access control policies
    ├── ProductPolicy.php
    ├── ShopPolicy.php
    └── WithdrawalPolicy.php
```

## API Structure Details

The API is organized to serve both customer-facing application and vendor operations:

```
app/Http/Controllers/Api/
├── v1/                       # API version 1
│   ├── Customer/             # Customer-facing endpoints
│   │   ├── ProductController.php
│   │   ├── ShopController.php
│   │   ├── CartController.php
│   │   ├── OrderController.php
│   │   ├── AuthController.php
│   │   ├── ProfileController.php
│   │   └── CheckoutController.php
│   ├── Vendor/               # Vendor-specific endpoints
│   │   ├── ProductController.php
│   │   ├── OrderController.php
│   │   ├── ShopController.php
│   │   ├── WithdrawalController.php
│   │   └── DashboardController.php
│   └── Admin/                # Admin-only endpoints
│       ├── ShopApprovalController.php
│       ├── ProductApprovalController.php
│       └── WithdrawalApprovalController.php
└── Documentation/            # API documentation controllers
    └── SwaggerController.php

routes/                       # Route definitions
├── api.php                   # Main API routes
└── vendor-api.php            # Vendor API routes

app/Services/                 # Business logic services
├── Payment/                  # Payment processing services
├── Order/                    # Order processing services
├── Shop/                     # Shop management services
│   └── CommissionService.php # Commission calculation service
└── Withdrawal/               # Withdrawal processing services

app/Repositories/             # Data access repositories
├── ProductRepository.php
├── OrderRepository.php
├── ShopRepository.php
├── UserRepository.php
└── WithdrawalRepository.php
```

## Customer-Facing Application Structure

The Vue.js frontend for customers is completely separate from the admin and vendor panels:

```
resources/js/
├── components/               # Vue components
│   ├── ui/                   # UI components
│   ├── layout/               # Layout components
│   ├── product/              # Product components
│   ├── shop/                 # Shop components
│   │   ├── ShopCard.vue      # Shop display card
│   │   ├── ShopHeader.vue    # Shop header with info
│   │   ├── ShopProducts.vue  # Products in shop
│   │   └── ShopReviews.vue   # Shop reviews
│   ├── cart/                 # Cart components
│   └── checkout/             # Checkout components
├── pages/                    # Vue page components
│   ├── Home.vue              # Homepage
│   ├── Shop.vue              # Product listing page
│   ├── Shops.vue             # Shop listing page
│   ├── ShopDetail.vue        # Individual shop page
│   ├── ProductDetail.vue     # Product detail page
│   ├── Cart.vue              # Cart page
│   ├── Checkout.vue          # Checkout page
│   ├── BecomeVendor.vue      # Vendor registration form
│   └── Account/              # Account pages
│       ├── Dashboard.vue     # Customer dashboard
│       ├── Orders.vue        # Order history
│       ├── Profile.vue       # Profile management
│       └── Vendor/           # Vendor application pages
│           ├── Application.vue # Apply to become vendor
│           ├── Status.vue    # Application status
├── store/                    # Vuex store
│   ├── index.js              # Store entry point
│   └── modules/              # Store modules
│       ├── products.js       # Products store module
│       ├── shops.js          # Shops store module
│       ├── cart.js           # Cart store module
│       ├── user.js           # User store module
│       ├── orders.js         # Orders store module
│       └── vendor.js         # Vendor application module
├── router/                   # Vue Router
├── services/                 # API services
│   ├── api.js                # API base configuration
│   ├── product.service.js    # Product API service
│   ├── shop.service.js       # Shop API service
│   ├── cart.service.js       # Cart API service
│   ├── user.service.js       # User API service
│   ├── order.service.js      # Order API service
│   └── vendor.service.js     # Vendor application service
├── utils/                    # Utility functions
├── assets/                   # Static assets
├── App.vue                   # Root Vue component
└── app.js                    # Vue application entry point
```

## Authentication and Access Control

The multi-vendor marketplace uses different authentication schemes for different user types:

1. **Admin Authentication**: 
   - Uses Filament's built-in authentication system
   - Accessible only at `/admin`
   - Full control over marketplace operations

2. **Vendor Authentication**:
   - Uses Filament's built-in authentication for the vendor panel
   - Accessible at `/vendor`
   - Limited to managing their own shop and products

3. **Customer Authentication**:
   - Uses Laravel Sanctum for API authentication
   - Managed through the Vue.js frontend
   - Used for shopping, account management, and vendor applications

## Setup Instructions

1. Create a new Laravel project:
   ```
   composer create-project --prefer-dist laravel/laravel ecommerce-project
   ```

2. Install Filament admin panel with multiple panels:
   ```
   composer require filament/filament
   php artisan filament:install --panels
   php artisan make:filament-panel admin
   php artisan make:filament-panel vendor
   ```

3. Configure multi-vendor database with Blueprint:
   ```
   composer require laravel-shift/blueprint
   php artisan vendor:publish --tag=blueprint-config
   ```

4. Create database schema using draft.yaml:
   ```
   php artisan blueprint:build
   ```

5. Install Vue.js with Vite for the customer-facing app:
   ```
   npm install vue vue-router vuex axios @vitejs/plugin-vue
   ```

6. Configure Vite in `vite.config.js` for the frontend app.

7. Set up vendor and admin routes:
   ```php
   // routes/vendor.php
   Route::middleware(['auth', 'vendor'])->prefix('vendor')->group(function () {
       // Vendor routes
   });
   ```

8. Configure permissions for different user roles:
   ```php
   // app/Policies/ShopPolicy.php
   public function update(User $user, Shop $shop)
   {
       return $user->id === $shop->user_id || $user->isAdmin();
   }
   ```

9. Build the frontend assets:
   ```
   npm run dev
   ```

10. Access the different panels:
    - Customer frontend: `/`
    - Admin panel: `/admin`
    - Vendor panel: `/vendor` 