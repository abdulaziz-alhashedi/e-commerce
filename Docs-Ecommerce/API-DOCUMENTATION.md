# Multi-Vendor E-Commerce API Documentation

## Overview

This document describes the RESTful API for our multi-vendor e-commerce marketplace. The API provides endpoints for managing products, vendors, customers, orders, and other marketplace functionality.

## Base URL

```
https://api.ourmarketplace.com/v1
```

## Authentication

### Bearer Token Authentication

All API requests must include an `Authorization` header with a valid bearer token:

```
Authorization: Bearer {your_access_token}
```

### Obtaining Access Tokens

#### Customer Authentication
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

#### Vendor Authentication
```
POST /vendor/auth/login
```

Request body:
```json
{
  "email": "vendor@example.com",
  "password": "password123"
}
```

Response:
```json
{
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9...",
  "token_type": "Bearer",
  "expires_in": 3600,
  "user": {
    "id": 1,
    "name": "John Doe",
    "email": "user@example.com",
    "role": "customer"
  }
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
    "first": "https://api.ourmarketplace.com/v1/products?page[number]=1&page[size]=15",
    "last": "https://api.ourmarketplace.com/v1/products?page[number]=5&page[size]=15",
    "prev": null,
    "next": "https://api.ourmarketplace.com/v1/products?page[number]=2&page[size]=15"
  },
  "meta": {
    "current_page": 1,
    "from": 1,
    "last_page": 5,
    "path": "https://api.ourmarketplace.com/v1/products",
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

## Customer Endpoints

### Products

#### List Products

```
GET /products
```

Query parameters:
- `filter[category]`: Filter by category ID
- `filter[shop]`: Filter by shop ID
- `filter[price_min]`: Minimum price
- `filter[price_max]`: Maximum price
- `filter[in_stock]`: Filter by stock availability (true/false)
- `sort`: Sort by attribute (e.g., name, price, created_at)
- `include`: Include related resources (e.g., category, images, shop)

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
        },
        "shop": {
          "data": {
            "id": "3",
            "type": "shops"
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
    },
    {
      "id": "3",
      "type": "shops",
      "attributes": {
        "name": "TechGadgets",
        "slug": "tech-gadgets",
        "description": "Quality tech products",
        "rating": 4.7
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
      },
      "shop": {
        "data": {
          "id": "3",
          "type": "shops"
        }
      }
    }
  }
}
```

### Shops

#### List Shops

```
GET /shops
```

Query parameters:
- `filter[featured]`: Filter featured shops (true/false)
- `filter[category]`: Filter by main category
- `sort`: Sort by attribute (e.g., name, rating, created_at)
- `include`: Include related resources (e.g., products)

Example response:
```json
{
  "data": [
    {
      "id": "3",
      "type": "shops",
      "attributes": {
        "name": "TechGadgets",
        "slug": "tech-gadgets",
        "description": "Quality tech products",
        "logo": "https://example.com/shops/3/logo.jpg",
        "banner": "https://example.com/shops/3/banner.jpg",
        "rating": 4.7,
        "products_count": 45,
        "is_featured": true,
        "created_at": "2023-01-15T10:00:00Z"
      },
      "relationships": {
        "products": {
          "links": {
            "related": "https://api.ourmarketplace.com/v1/shops/3/products"
          }
        }
      }
    }
    // More shops...
  ],
  "links": {
    // Pagination links
  },
  "meta": {
    // Pagination metadata
  }
}
```

#### Get Shop

```
GET /shops/{id}
```

Example response:
```json
{
  "data": {
    "id": "3",
    "type": "shops",
    "attributes": {
      "name": "TechGadgets",
      "slug": "tech-gadgets",
      "description": "Quality tech products",
      "logo": "https://example.com/shops/3/logo.jpg",
      "banner": "https://example.com/shops/3/banner.jpg",
      "email": "contact@techgadgets.com",
      "phone": "+1234567890",
      "address": "123 Tech Street",
      "city": "San Francisco",
      "state": "CA",
      "postal_code": "94103",
      "country": "USA",
      "rating": 4.7,
      "products_count": 45,
      "is_featured": true,
      "created_at": "2023-01-15T10:00:00Z"
    }
  }
}
```

#### Get Shop Products

```
GET /shops/{id}/products
```

### Cart & Checkout

#### Get Cart

```
GET /cart
```

Example response:
```json
{
  "data": {
    "id": "cart_123",
    "type": "carts",
    "attributes": {
      "subtotal": "249.97",
      "tax": "20.00",
      "shipping": "10.00",
      "discount": "0.00",
      "total": "279.97",
      "item_count": 3
    },
    "relationships": {
      "items": {
        "data": [
          {
            "id": "item_1",
            "type": "cart_items"
          },
          {
            "id": "item_2",
            "type": "cart_items"
          }
        ]
      }
    }
  },
  "included": [
    {
      "id": "item_1",
      "type": "cart_items",
      "attributes": {
        "quantity": 1,
        "price": "129.99",
        "subtotal": "129.99"
      },
      "relationships": {
        "product": {
          "data": {
            "id": "1",
            "type": "products"
          }
        },
        "shop": {
          "data": {
            "id": "3",
            "type": "shops"
          }
        }
      }
    },
    {
      "id": "item_2",
      "type": "cart_items",
      "attributes": {
        "quantity": 2,
        "price": "59.99",
        "subtotal": "119.98"
      },
      "relationships": {
        "product": {
          "data": {
            "id": "2",
            "type": "products"
          }
        },
        "shop": {
          "data": {
            "id": "3",
            "type": "shops"
          }
        }
      }
    }
  ]
}
```

#### Add to Cart

```
POST /cart/items
```

Request body:
```json
{
  "data": {
    "type": "cart_items",
    "attributes": {
      "product_id": 2,
      "quantity": 1,
      "variant_id": 5
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
    "id": "item_2",
    "attributes": {
      "quantity": 3
    }
  }
}
```

#### Remove from Cart

```
DELETE /cart/items/{id}
```

#### Checkout

```
POST /checkout
```

Request body:
```json
{
  "data": {
    "type": "orders",
    "attributes": {
      "shipping_address": {
        "first_name": "John",
        "last_name": "Doe",
        "address_line_1": "123 Main St",
        "city": "Anytown",
        "state": "CA",
        "postal_code": "12345",
        "country": "US",
        "phone": "555-123-4567"
      },
      "billing_address": {
        "same_as_shipping": true
      },
      "payment_method": "stripe",
      "payment_token": "tok_visa"
    }
  }
}
```

### Orders

#### List Orders

```
GET /orders
```

Example response:
```json
{
  "data": [
    {
      "id": "1001",
      "type": "orders",
      "attributes": {
        "order_number": "ORD-1001",
        "status": "processing",
        "payment_status": "paid",
        "subtotal": "249.97",
        "tax": "20.00",
        "shipping": "10.00",
        "discount": "0.00",
        "total": "279.97",
        "created_at": "2023-04-05T14:30:00Z"
      },
      "relationships": {
        "items": {
          "data": [
            {
              "id": "5001",
              "type": "order_items"
            },
            {
              "id": "5002",
              "type": "order_items"
            }
          ]
        },
        "shops": {
          "data": [
            {
              "id": "3",
              "type": "shops"
            }
          ]
        }
      }
    }
  ],
  "included": [
    {
      "id": "5001",
      "type": "order_items",
      "attributes": {
        "name": "Wireless Headphones",
        "sku": "WH-001",
        "quantity": 1,
        "price": "129.99",
        "subtotal": "129.99"
      },
      "relationships": {
        "product": {
          "data": {
            "id": "1",
            "type": "products"
          }
        },
        "shop": {
          "data": {
            "id": "3",
            "type": "shops"
          }
        }
      }
    }
  ]
}
```

#### Get Order

```
GET /orders/{id}
```

## Vendor Endpoints

### Vendor Authentication

#### Register as Vendor

```
POST /vendor/register
```

Request body:
```json
{
  "data": {
    "type": "shops",
    "attributes": {
      "name": "My Tech Shop",
      "email": "vendor@example.com",
      "password": "password123",
      "password_confirmation": "password123",
      "shop_name": "TechGadgets",
      "shop_description": "Quality tech products"
    }
  }
}
```

### Vendor Products

#### List Vendor Products

```
GET /vendor/products
```

#### Create Product

```
POST /vendor/products
```

Request body:
```json
{
  "data": {
    "type": "products",
    "attributes": {
      "name": "Bluetooth Speaker",
      "description": "Portable Bluetooth speaker with 20-hour battery life",
      "short_description": "High-quality portable speaker",
      "price": "89.99",
      "compare_price": "99.99",
      "cost": "45.00",
      "sku": "BS-001",
      "barcode": "1234567890123",
      "quantity": 30,
      "is_digital": false,
      "weight": 1.2,
      "weight_unit": "kg",
      "width": 15,
      "height": 8,
      "length": 15,
      "dimension_unit": "cm"
    },
    "relationships": {
      "category": {
        "data": {
          "id": "5",
          "type": "categories"
        }
      },
      "images": {
        "data": [
          {
            "type": "product_images",
            "attributes": {
              "path": "base64-encoded-image-data",
              "is_primary": true
            }
          }
        ]
      }
    }
  }
}
```

#### Update Product

```
PATCH /vendor/products/{id}
```

#### Delete Product

```
DELETE /vendor/products/{id}
```

### Vendor Orders

#### List Vendor Orders

```
GET /vendor/orders
```

Query parameters:
- `filter[status]`: Filter by order status
- `filter[date_from]`: Filter by minimum date
- `filter[date_to]`: Filter by maximum date

#### Update Order Status

```
PATCH /vendor/orders/{id}
```

Request body:
```json
{
  "data": {
    "type": "orders",
    "id": "1001",
    "attributes": {
      "status": "shipped",
      "tracking_number": "1Z999AA10123456784"
    }
  }
}
```

### Vendor Shop Management

#### Get Shop Profile

```
GET /vendor/shop
```

#### Update Shop Profile

```
PATCH /vendor/shop
```

Request body:
```json
{
  "data": {
    "type": "shops",
    "attributes": {
      "name": "TechGadgets Pro",
      "description": "Premium tech products for professionals",
      "email": "contact@techgadgets.com",
      "phone": "+1234567890",
      "logo": "base64-encoded-image-data",
      "banner": "base64-encoded-image-data"
    }
  }
}
```

### Vendor Earnings & Withdrawals

#### Get Earnings

```
GET /vendor/earnings
```

Query parameters:
- `filter[period]`: Filter by period (today, week, month, year)
- `filter[date_from]`: Filter by minimum date
- `filter[date_to]`: Filter by maximum date

Example response:
```json
{
  "data": {
    "type": "earnings",
    "attributes": {
      "total_sales": "12750.45",
      "total_commission": "1275.05",
      "net_earnings": "11475.40",
      "pending_clearance": "1500.00",
      "available_for_withdrawal": "9975.40",
      "period": "month",
      "date_from": "2023-04-01",
      "date_to": "2023-04-30"
    }
  }
}
```

#### List Transactions

```
GET /vendor/transactions
```

#### Request Withdrawal

```
POST /vendor/withdrawals
```

Request body:
```json
{
  "data": {
    "type": "withdrawals",
    "attributes": {
      "amount": "1000.00",
      "payment_method": "bank_transfer",
      "bank_name": "Example Bank",
      "account_name": "John Doe",
      "account_number": "1234567890",
      "routing_number": "123456789"
    }
  }
}
```

#### List Withdrawals

```
GET /vendor/withdrawals
```

## Admin Endpoints

Admin endpoints are available at `/admin/api/v1/` and require admin authentication.

### Vendor Management

#### List Vendors

```
GET /admin/api/v1/vendors
```

#### Approve Vendor

```
PATCH /admin/api/v1/vendors/{id}/approve
```

#### Reject Vendor

```
PATCH /admin/api/v1/vendors/{id}/reject
```

Request body:
```json
{
  "data": {
    "type": "shops",
    "attributes": {
      "rejection_reason": "Insufficient business information provided."
    }
  }
}
```

### Commission Management

#### Update Commission Rate

```
PATCH /admin/api/v1/vendors/{id}/commission
```

Request body:
```json
{
  "data": {
    "type": "shops",
    "attributes": {
      "commission_rate": 12.5
    }
  }
}
```

### Withdrawal Management

#### List Withdrawal Requests

```
GET /admin/api/v1/withdrawals
```

#### Approve Withdrawal

```
PATCH /admin/api/v1/withdrawals/{id}/approve
```

#### Reject Withdrawal

```
PATCH /admin/api/v1/withdrawals/{id}/reject
```

Request body:
```json
{
  "data": {
    "type": "withdrawals",
    "attributes": {
      "rejection_reason": "Invalid banking information."
    }
  }
}
```

## Webhooks

Webhook events are available for integrating with external systems:

```
POST /webhooks/orders
POST /webhooks/products
POST /webhooks/vendors
```

## Rate Limiting

To prevent abuse, the API implements rate limiting:

- Public endpoints: 60 requests per minute
- Authenticated customers: 120 requests per minute
- Vendors: 180 requests per minute
- Admins: 300 requests per minute

## Error Codes

The API uses standard HTTP status codes and provides detailed error messages:

- `400 Bad Request`: Invalid input
- `401 Unauthorized`: Authentication required
- `403 Forbidden`: Insufficient permissions
- `404 Not Found`: Resource not found
- `422 Unprocessable Entity`: Validation errors
- `429 Too Many Requests`: Rate limit exceeded
- `500 Internal Server Error`: Server error

## Versioning

The API is versioned via the URL path. The current version is `v1`. Future breaking changes will be introduced in new API versions. 