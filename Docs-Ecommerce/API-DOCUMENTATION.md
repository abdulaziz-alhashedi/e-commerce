# E-Commerce API Documentation

## Overview

This document describes the RESTful API for our e-commerce platform. The API provides endpoints for managing products, customers, orders, and other e-commerce related functionality.

## Base URL

```
https://api.ourstore.com/v1
```

## Authentication

### Bearer Token Authentication

All API requests must include an `Authorization` header with a valid bearer token:

```
Authorization: Bearer {your_access_token}
```

### Obtaining Access Tokens

```
POST /auth/login
```

Request body:
```json
{
  "email": "user@example.com",
  "password": "password123"
}
```

Response:
```json
{
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9...",
  "token_type": "Bearer",
  "expires_in": 3600
}
```

## Response Format

All responses follow the JSON:API specification with the following structure:

```json
{
  "data": {
    // Resource object or array of resource objects
  },
  "meta": {
    // Metadata about the response
  },
  "links": {
    // Pagination links
  }
}
```

Error responses:

```json
{
  "errors": [
    {
      "status": "404",
      "title": "Resource not found",
      "detail": "The requested product could not be found"
    }
  ]
}
```

## Pagination

Paginated endpoints include the following query parameters:

- `page[number]`: The page number (default: 1)
- `page[size]`: Number of items per page (default: 15, max: 100)

Response includes pagination links:

```json
{
  "links": {
    "first": "https://api.ourstore.com/v1/products?page[number]=1&page[size]=15",
    "last": "https://api.ourstore.com/v1/products?page[number]=5&page[size]=15",
    "prev": null,
    "next": "https://api.ourstore.com/v1/products?page[number]=2&page[size]=15"
  },
  "meta": {
    "current_page": 1,
    "from": 1,
    "last_page": 5,
    "path": "https://api.ourstore.com/v1/products",
    "per_page": 15,
    "to": 15,
    "total": 75
  }
}
```

## Filtering, Sorting, and Inclusion

- **Filtering**: `filter[attribute]=value`
- **Sorting**: `sort=attribute` or `sort=-attribute` (descending)
- **Including related resources**: `include=relation1,relation2`

## Rate Limiting

API requests are limited to 60 requests per minute per user. Rate limit information is included in the response headers:

```
X-RateLimit-Limit: 60
X-RateLimit-Remaining: 59
X-RateLimit-Reset: 1617567600
```

## Endpoints

### Products

#### List Products

```
GET /products
```

Query parameters:
- `filter[category]`: Filter by category ID
- `filter[price_min]`: Minimum price
- `filter[price_max]`: Maximum price
- `filter[in_stock]`: Filter by stock availability (true/false)
- `sort`: Sort by attribute (e.g., name, price, created_at)
- `include`: Include related resources (e.g., category, images)

Example response:
```json
{
  "data": [
    {
      "id": "1",
      "type": "products",
      "attributes": {
        "name": "Wireless Headphones",
        "slug": "wireless-headphones",
        "description": "High-quality wireless headphones",
        "price": "129.99",
        "sale_price": null,
        "stock": 45,
        "sku": "WH-001",
        "is_active": true,
        "created_at": "2023-04-01T12:00:00Z",
        "updated_at": "2023-04-01T12:00:00Z"
      },
      "relationships": {
        "category": {
          "data": {
            "id": "5",
            "type": "categories"
          }
        }
      }
    }
    // More products...
  ],
  "included": [
    {
      "id": "5",
      "type": "categories",
      "attributes": {
        "name": "Electronics",
        "slug": "electronics"
      }
    }
  ],
  "links": {
    // Pagination links
  },
  "meta": {
    // Pagination metadata
  }
}
```

#### Get Product

```
GET /products/{id}
```

Example response:
```json
{
  "data": {
    "id": "1",
    "type": "products",
    "attributes": {
      "name": "Wireless Headphones",
      "slug": "wireless-headphones",
      "description": "High-quality wireless headphones",
      "price": "129.99",
      "sale_price": null,
      "stock": 45,
      "sku": "WH-001",
      "is_active": true,
      "created_at": "2023-04-01T12:00:00Z",
      "updated_at": "2023-04-01T12:00:00Z"
    },
    "relationships": {
      "category": {
        "data": {
          "id": "5",
          "type": "categories"
        }
      }
    }
  }
}
```

#### Create Product (Admin Only)

```
POST /products
```

Request body:
```json
{
  "data": {
    "type": "products",
    "attributes": {
      "name": "Bluetooth Speaker",
      "description": "Portable Bluetooth speaker with 20-hour battery life",
      "price": "89.99",
      "stock": 30,
      "sku": "BS-001",
      "is_active": true
    },
    "relationships": {
      "category": {
        "data": {
          "id": "5",
          "type": "categories"
        }
      }
    }
  }
}
```

### Categories

#### List Categories

```
GET /categories
```

Example response:
```json
{
  "data": [
    {
      "id": "1",
      "type": "categories",
      "attributes": {
        "name": "Clothing",
        "slug": "clothing",
        "description": "Apparel and fashion items",
        "is_active": true
      },
      "relationships": {
        "parent": {
          "data": null
        }
      }
    },
    {
      "id": "2",
      "type": "categories",
      "attributes": {
        "name": "Men's Clothing",
        "slug": "mens-clothing",
        "description": "Clothing for men",
        "is_active": true
      },
      "relationships": {
        "parent": {
          "data": {
            "id": "1",
            "type": "categories"
          }
        }
      }
    }
    // More categories...
  ]
}
```

### Customers

#### Register Customer

```
POST /customers
```

Request body:
```json
{
  "data": {
    "type": "customers",
    "attributes": {
      "name": "John Doe",
      "email": "john@example.com",
      "password": "securepassword123",
      "password_confirmation": "securepassword123",
      "phone": "123-456-7890",
      "address": "123 Main St",
      "city": "New York",
      "state": "NY",
      "zip": "10001",
      "country": "USA"
    }
  }
}
```

#### Get Customer Profile

```
GET /customers/me
```

### Orders

#### Create Order

```
POST /orders
```

Request body:
```json
{
  "data": {
    "type": "orders",
    "attributes": {
      "shipping_address": "123 Main St",
      "shipping_city": "New York",
      "shipping_state": "NY",
      "shipping_zip": "10001",
      "shipping_country": "USA",
      "notes": "Please leave at front door"
    },
    "relationships": {
      "items": {
        "data": [
          {
            "type": "order_items",
            "attributes": {
              "product_id": "1",
              "quantity": 2
            }
          },
          {
            "type": "order_items",
            "attributes": {
              "product_id": "3",
              "quantity": 1
            }
          }
        ]
      }
    }
  }
}
```

#### List Customer Orders

```
GET /orders
```

#### Get Order Details

```
GET /orders/{id}
```

### Cart

#### Get Cart

```
GET /cart
```

#### Add Item to Cart

```
POST /cart/items
```

Request body:
```json
{
  "data": {
    "type": "cart_items",
    "attributes": {
      "product_id": "1",
      "quantity": 2
    }
  }
}
```

#### Update Cart Item

```
PATCH /cart/items/{id}
```

Request body:
```json
{
  "data": {
    "type": "cart_items",
    "id": "1",
    "attributes": {
      "quantity": 3
    }
  }
}
```

#### Remove Cart Item

```
DELETE /cart/items/{id}
```

#### Clear Cart

```
DELETE /cart
```

## Status Codes

The API uses the following status codes:

- `200 OK`: The request was successful
- `201 Created`: Resource created successfully
- `204 No Content`: Request successful, no content to return
- `400 Bad Request`: Invalid request parameters
- `401 Unauthorized`: Authentication required or failed
- `403 Forbidden`: Authenticated but not authorized
- `404 Not Found`: Resource not found
- `422 Unprocessable Entity`: Validation errors
- `429 Too Many Requests`: Rate limit exceeded
- `500 Server Error`: Internal server error

## API Versioning

The API version is specified in the URL path (e.g., `/v1/products`). When breaking changes are introduced, a new version will be created (e.g., `/v2/products`).

## Implementation Notes for Developers

### Creating New Endpoints

1. Define routes in `routes/api.php`
2. Create controller with appropriate methods
3. Create API resources and resource collections
4. Implement validation using form requests
5. Add authentication and authorization checks
6. Document the endpoint in this file
7. Write tests for the endpoint

### Example Controller

```php
namespace App\Http\Controllers\Api;

use App\Http\Controllers\Controller;
use App\Http\Requests\StoreProductRequest;
use App\Http\Resources\ProductResource;
use App\Models\Product;
use Illuminate\Http\Request;

class ProductController extends Controller
{
    public function index(Request $request)
    {
        $query = Product::query();
        
        // Apply filters
        if ($request->has('filter.category')) {
            $query->where('category_id', $request->input('filter.category'));
        }
        
        // Apply sorting
        if ($request->has('sort')) {
            $sortField = $request->input('sort');
            $direction = 'asc';
            
            if (str_starts_with($sortField, '-')) {
                $direction = 'desc';
                $sortField = substr($sortField, 1);
            }
            
            $query->orderBy($sortField, $direction);
        }
        
        // Include relationships
        $includes = $request->input('include') ? explode(',', $request->input('include')) : [];
        
        $products = $query->paginate($request->input('page.size', 15));
        
        return ProductResource::collection($products)
            ->additional([
                'meta' => [
                    // Additional metadata
                ]
            ]);
    }
    
    public function store(StoreProductRequest $request)
    {
        $product = Product::create($request->validated());
        
        return new ProductResource($product);
    }
    
    // Other controller methods...
}
``` 