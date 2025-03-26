# Filament Generation Commands

## Advanced Resource Generation

* `php artisan make:filament-resource Product --view` - Creates a resource with custom view files
* `php artisan make:filament-resource Order --generate` - Generates all possible files for the resource
* `php artisan make:filament-resource Customer --force` - Overwrites existing files
* `php artisan make:filament-resource Invoice --soft-deletes --view` - Combines multiple options

## Panel Commands

* `php artisan make:filament-panel admin` - Creates a new panel named 'admin'
* `php artisan make:filament-panel customer --tenant` - Creates a tenant-aware panel

## Custom Component Generation

* `php artisan make:filament-page Dashboard --panel=admin` - Creates a page in specific panel
* `php artisan make:filament-widget Analytics --panel=admin --chart` - Creates a chart widget in specific panel
* `php artisan make:filament-widget RecentOrders --table` - Creates a table widget

## Action Commands

* `php artisan make:filament-action ExportPDF` - Creates a custom action
* `php artisan make:filament-action ImportCSV --panel=admin` - Creates a panel-specific action

## Layout Component Generation

* `php artisan make:filament-layout CustomLayout` - Creates a custom layout component
* `php artisan make:filament-layout TwoColumn --panel=admin` - Creates a panel-specific layout

## Theme and Asset Commands

* `php artisan filament:assets` - Publishes all Filament assets
* `php artisan filament:upgrade` - Upgrades Filament and its assets

## Development Tools

* `php artisan filament:check-translations` - Checks for missing translations
* `php artisan filament:install --scaffold` - Installs Filament with scaffolding
* `php artisan filament:install --pest` - Installs Filament with Pest testing

Remember to run `php artisan optimize:clear` after generating new components to ensure proper registration.

For more detailed information about each command and its options, refer to the official Filament documentation.