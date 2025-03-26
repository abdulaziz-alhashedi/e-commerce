<p align="center"><a href="https://laravel.com" target="_blank"><img src="https://raw.githubusercontent.com/laravel/art/master/logo-lockup/5%20SVG/2%20CMYK/1%20Full%20Color/laravel-logolockup-cmyk-red.svg" width="400" alt="Laravel Logo"></a></p>

# Laravel Project with Filament & Blueprint

This project is built using Laravel, enhanced with Filament for admin panel generation and Blueprint for rapid development. Here's how to get started and make the most of these powerful tools.

## Prerequisites

- PHP 8.1 or higher
- Composer
- Node.js & NPM
- MySQL or PostgreSQL

## Project Setup

1. Clone the repository:
```bash
git clone <repository-url>
cd <project-directory>
```

2. Install PHP dependencies:
```bash
composer install
```

3. Install NPM packages:
```bash
npm install
```

4. Set up environment:
```bash
cp .env.example .env
php artisan key:generate
```

5. Configure your database in `.env` file:
```env
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=your_database
DB_USERNAME=your_username
DB_PASSWORD=your_password
```

6. Run migrations:
```bash
php artisan migrate
```

## Using Blueprint for Development

Blueprint accelerates development by generating code from YAML definitions.

1. Define your models in `draft.yaml`:
```yaml
models:
  Post:
    title: string
    content: text
    published_at: timestamp nullable
    relationships:
      belongsTo: User
```

2. Generate code:
```bash
php artisan blueprint:build
```

This will create:
- Model
- Migration
- Factory
- Controller
- Form Requests

## Working with Filament

Filament provides a powerful admin panel interface.

1. Create a resource:
```bash
php artisan make:filament-resource Post
```

2. Customize the resource in `app/Filament/Resources/PostResource.php`

3. Access the admin panel at: `http://your-app.test/admin`

### Common Filament Patterns

1. Form Fields:
```php
public static function form(Form $form): Form
{
    return $form->schema([
        Forms\Components\TextInput::make('title'),
        Forms\Components\RichEditor::make('content'),
        Forms\Components\DateTimePicker::make('published_at'),
    ]);
}
```

2. Table Columns:
```php
public static function table(Table $table): Table
{
    return $table->columns([
        Tables\Columns\TextColumn::make('title'),
        Tables\Columns\TextColumn::make('published_at'),
    ]);
}
```

## Development Workflow

1. Define models and relationships in `draft.yaml`
2. Generate code with Blueprint
3. Create Filament resources for admin interface
4. Customize generated code as needed
5. Add business logic and custom features

## Running the Application

1. Start the development server:
```bash
php artisan serve
```

2. Compile assets:
```bash
npm run dev
```

3. Access the application:
- Main site: `http://localhost:8000`
- Admin panel: `http://localhost:8000/admin`

## Best Practices

1. **Blueprint**
- Keep `draft.yaml` updated
- Review generated code
- Use seeders for test data

2. **Filament**
- Organize resources logically
- Use widgets for dashboards
- Implement proper authorization

3. **General**
- Follow Laravel conventions
- Write tests for custom features
- Use Git for version control

## Useful Commands

```bash
# Blueprint
php artisan blueprint:build    # Generate code
php artisan blueprint:erase    # Remove generated code
php artisan blueprint:trace    # Show what will be generated

# Filament
php artisan make:filament-resource    # Create resource
php artisan make:filament-widget      # Create widget

# Laravel
php artisan migrate:fresh --seed      # Reset database
php artisan test                      # Run tests
```

## Contributing

Please read our [Contributing Guide](CONTRIBUTING.md) for details on our code of conduct and development process.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
