# API Documentation

This document provides comprehensive documentation for the MERN Order Management System REST API.

## Table of Contents

- [Base URL](#base-url)
- [Authentication](#authentication)
- [Response Format](#response-format)
- [Error Handling](#error-handling)
- [Endpoints](#endpoints)
  - [Authentication Endpoints](#authentication-endpoints)
  - [Order Endpoints](#order-endpoints)
- [Data Models](#data-models)
- [Rate Limiting](#rate-limiting)
- [Examples](#examples)

## Base URL

```
Development: http://localhost:5000/api
Production: https://your-domain.com/api
```

## Authentication

### JWT Token-based Authentication

All order-related endpoints require authentication using JSON Web Tokens (JWT).

#### Header Format
```
Authorization: Bearer <jwt_token>
```

#### Token Structure
```json
{
  "userId": "507f1f77bcf86cd799439011",
  "email": "user@example.com",
  "iat": 1640995200,
  "exp": 1643587200
}
```

#### Token Expiration
- **Development**: 30 days
- **Production**: 7 days (recommended)

## Response Format

### Success Response
```json
{
  "success": true,
  "data": {
    // Response data
  },
  "message": "Operation completed successfully"
}
```

### Error Response
```json
{
  "success": false,
  "error": {
    "message": "Error description",
    "code": "ERROR_CODE",
    "details": {}
  }
}
```

## Error Handling

### HTTP Status Codes

| Status | Code | Description |
|--------|------|-------------|
| 200 | OK | Request successful |
| 201 | Created | Resource created successfully |
| 400 | Bad Request | Invalid request data |
| 401 | Unauthorized | Authentication required |
| 403 | Forbidden | Insufficient permissions |
| 404 | Not Found | Resource not found |
| 409 | Conflict | Resource already exists |
| 422 | Unprocessable Entity | Validation failed |
| 500 | Internal Server Error | Server error |

### Error Codes

| Code | Description |
|------|-------------|
| VALIDATION_ERROR | Input validation failed |
| AUTHENTICATION_FAILED | Invalid credentials |
| TOKEN_EXPIRED | JWT token has expired |
| TOKEN_INVALID | JWT token is invalid |
| USER_NOT_FOUND | User does not exist |
| EMAIL_EXISTS | Email already registered |
| ORDER_NOT_FOUND | Order does not exist |
| PERMISSION_DENIED | Insufficient permissions |
| DATABASE_ERROR | Database operation failed |
| SERVER_ERROR | Internal server error |

## Endpoints

### Authentication Endpoints

#### Register User

Create a new user account with shop details.

```http
POST /api/auth/register
```

**Request Body**
```json
{
  "shopName": "My Electronics Store",
  "ownerName": "John Doe",
  "email": "john@example.com",
  "password": "password123",
  "phone": "123-456-7890",
  "address": "123 Main St, City, State 12345"
}
```

**Validation Rules**
- `shopName`: Required, string, 3-100 characters
- `ownerName`: Required, string, 2-100 characters
- `email`: Required, valid email format, unique
- `password`: Required, string, 6-100 characters
- `phone`: Required, string, 10-20 characters
- `address`: Required, string, 10-500 characters

**Success Response (201)**
```json
{
  "success": true,
  "data": {
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "user": {
      "_id": "507f1f77bcf86cd799439011",
      "shopName": "My Electronics Store",
      "ownerName": "John Doe",
      "email": "john@example.com",
      "phone": "123-456-7890",
      "address": "123 Main St, City, State 12345",
      "createdAt": "2023-01-01T00:00:00.000Z"
    }
  },
  "message": "User registered successfully"
}
```

**Error Responses**
- `400`: Validation error
- `409`: Email already exists

#### Login User

Authenticate user and return JWT token.

```http
POST /api/auth/login
```

**Request Body**
```json
{
  "email": "john@example.com",
  "password": "password123"
}
```

**Success Response (200)**
```json
{
  "success": true,
  "data": {
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "user": {
      "_id": "507f1f77bcf86cd799439011",
      "shopName": "My Electronics Store",
      "ownerName": "John Doe",
      "email": "john@example.com",
      "phone": "123-456-7890",
      "address": "123 Main St, City, State 12345"
    }
  },
  "message": "Login successful"
}
```

**Error Responses**
- `400`: Validation error
- `401`: Invalid credentials
- `404`: User not found

#### Get Current User

Get information about the currently authenticated user.

```http
GET /api/auth/me
```

**Headers**
```
Authorization: Bearer <jwt_token>
```

**Success Response (200)**
```json
{
  "success": true,
  "data": {
    "user": {
      "_id": "507f1f77bcf86cd799439011",
      "shopName": "My Electronics Store",
      "ownerName": "John Doe",
      "email": "john@example.com",
      "phone": "123-456-7890",
      "address": "123 Main St, City, State 12345",
      "createdAt": "2023-01-01T00:00:00.000Z"
    }
  }
}
```

**Error Responses**
- `401`: Token invalid or expired
- `404`: User not found

### Order Endpoints

All order endpoints require authentication.

#### Create Order

Create a new order for the authenticated user.

```http
POST /api/orders
```

**Headers**
```
Authorization: Bearer <jwt_token>
Content-Type: application/json
```

**Request Body**
```json
{
  "customerName": "Jane Smith",
  "customerPhone": "555-123-4567",
  "customerAddress": "456 Oak Ave, City, State 67890",
  "product": "Laptop Computer"
}
```

**Validation Rules**
- `customerName`: Required, string, 2-100 characters
- `customerPhone`: Required, string, 10-20 characters
- `customerAddress`: Required, string, 10-500 characters
- `product`: Required, string, 2-200 characters

**Success Response (201)**
```json
{
  "success": true,
  "data": {
    "order": {
      "_id": "507f1f77bcf86cd799439012",
      "customerName": "Jane Smith",
      "customerPhone": "555-123-4567",
      "customerAddress": "456 Oak Ave, City, State 67890",
      "product": "Laptop Computer",
      "userId": "507f1f77bcf86cd799439011",
      "createdAt": "2023-01-01T12:30:00.000Z",
      "updatedAt": "2023-01-01T12:30:00.000Z"
    }
  },
  "message": "Order created successfully"
}
```

**Error Responses**
- `400`: Validation error
- `401`: Authentication required
- `422`: Unprocessable entity

#### Get All Orders

Retrieve all orders for the authenticated user.

```http
GET /api/orders
```

**Headers**
```
Authorization: Bearer <jwt_token>
```

**Query Parameters**
- `page`: Page number (default: 1)
- `limit`: Items per page (default: 20, max: 100)
- `sort`: Sort field (default: createdAt)
- `order`: Sort order (asc/desc, default: desc)

**Success Response (200)**
```json
{
  "success": true,
  "data": {
    "orders": [
      {
        "_id": "507f1f77bcf86cd799439012",
        "customerName": "Jane Smith",
        "customerPhone": "555-123-4567",
        "customerAddress": "456 Oak Ave, City, State 67890",
        "product": "Laptop Computer",
        "userId": "507f1f77bcf86cd799439011",
        "createdAt": "2023-01-01T12:30:00.000Z",
        "updatedAt": "2023-01-01T12:30:00.000Z"
      }
    ],
    "pagination": {
      "currentPage": 1,
      "totalPages": 5,
      "totalOrders": 95,
      "hasNext": true,
      "hasPrev": false
    }
  }
}
```

**Error Responses**
- `401`: Authentication required

#### Search Orders

Search orders by customer name, phone, product, or address.

```http
GET /api/orders/search
```

**Headers**
```
Authorization: Bearer <jwt_token>
```

**Query Parameters**
- `q`: Search query (required)
- `page`: Page number (default: 1)
- `limit`: Items per page (default: 20, max: 100)

**Example Request**
```http
GET /api/orders/search?q=Jane&page=1&limit=10
```

**Success Response (200)**
```json
{
  "success": true,
  "data": {
    "orders": [
      {
        "_id": "507f1f77bcf86cd799439012",
        "customerName": "Jane Smith",
        "customerPhone": "555-123-4567",
        "customerAddress": "456 Oak Ave, City, State 67890",
        "product": "Laptop Computer",
        "userId": "507f1f77bcf86cd799439011",
        "createdAt": "2023-01-01T12:30:00.000Z",
        "updatedAt": "2023-01-01T12:30:00.000Z"
      }
    ],
    "pagination": {
      "currentPage": 1,
      "totalPages": 1,
      "totalOrders": 1,
      "hasNext": false,
      "hasPrev": false
    },
    "searchQuery": "Jane"
  }
}
```

**Error Responses**
- `400`: Missing search query
- `401`: Authentication required

#### Get Single Order

Retrieve a specific order by ID.

```http
GET /api/orders/:id
```

**Headers**
```
Authorization: Bearer <jwt_token>
```

**URL Parameters**
- `id`: Order ID (MongoDB ObjectId)

**Success Response (200)**
```json
{
  "success": true,
  "data": {
    "order": {
      "_id": "507f1f77bcf86cd799439012",
      "customerName": "Jane Smith",
      "customerPhone": "555-123-4567",
      "customerAddress": "456 Oak Ave, City, State 67890",
      "product": "Laptop Computer",
      "userId": "507f1f77bcf86cd799439011",
      "createdAt": "2023-01-01T12:30:00.000Z",
      "updatedAt": "2023-01-01T12:30:00.000Z"
    }
  }
}
```

**Error Responses**
- `401`: Authentication required
- `403`: Permission denied (order belongs to different user)
- `404`: Order not found

#### Update Order

Update an existing order.

```http
PUT /api/orders/:id
```

**Headers**
```
Authorization: Bearer <jwt_token>
Content-Type: application/json
```

**URL Parameters**
- `id`: Order ID (MongoDB ObjectId)

**Request Body**
```json
{
  "customerName": "Jane Smith",
  "customerPhone": "555-123-4567",
  "customerAddress": "456 Oak Ave, City, State 67890",
  "product": "Laptop Computer - Updated"
}
```

**Success Response (200)**
```json
{
  "success": true,
  "data": {
    "order": {
      "_id": "507f1f77bcf86cd799439012",
      "customerName": "Jane Smith",
      "customerPhone": "555-123-4567",
      "customerAddress": "456 Oak Ave, City, State 67890",
      "product": "Laptop Computer - Updated",
      "userId": "507f1f77bcf86cd799439011",
      "createdAt": "2023-01-01T12:30:00.000Z",
      "updatedAt": "2023-01-01T13:00:00.000Z"
    }
  },
  "message": "Order updated successfully"
}
```

**Error Responses**
- `400`: Validation error
- `401`: Authentication required
- `403`: Permission denied
- `404`: Order not found

#### Delete Order

Delete an order by ID.

```http
DELETE /api/orders/:id
```

**Headers**
```
Authorization: Bearer <jwt_token>
```

**URL Parameters**
- `id`: Order ID (MongoDB ObjectId)

**Success Response (200)**
```json
{
  "success": true,
  "data": {
    "order": {
      "_id": "507f1f77bcf86cd799439012",
      "customerName": "Jane Smith",
      "customerPhone": "555-123-4567",
      "customerAddress": "456 Oak Ave, City, State 67890",
      "product": "Laptop Computer",
      "userId": "507f1f77bcf86cd799439011",
      "createdAt": "2023-01-01T12:30:00.000Z",
      "updatedAt": "2023-01-01T12:30:00.000Z"
    }
  },
  "message": "Order deleted successfully"
}
```

**Error Responses**
- `401`: Authentication required
- `403`: Permission denied
- `404`: Order not found

## Data Models

### User Model

```javascript
{
  _id: ObjectId,
  shopName: String (required, 3-100 chars),
  ownerName: String (required, 2-100 chars),
  email: String (required, unique, valid email),
  password: String (required, hashed),
  phone: String (required, 10-20 chars),
  address: String (required, 10-500 chars),
  createdAt: Date (default: Date.now),
  updatedAt: Date (default: Date.now)
}
```

### Order Model

```javascript
{
  _id: ObjectId,
  customerName: String (required, 2-100 chars),
  customerPhone: String (required, 10-20 chars),
  customerAddress: String (required, 10-500 chars),
  product: String (required, 2-200 chars),
  userId: ObjectId (required, ref: 'User'),
  createdAt: Date (default: Date.now),
  updatedAt: Date (default: Date.now)
}
```

## Rate Limiting

### Default Limits
- **Authentication endpoints**: 5 requests per minute per IP
- **Order endpoints**: 100 requests per minute per authenticated user
- **Search endpoints**: 30 requests per minute per authenticated user

### Rate Limit Headers
```http
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 95
X-RateLimit-Reset: 1640995260
```

### Rate Limit Exceeded Response
```json
{
  "success": false,
  "error": {
    "message": "Too many requests, please try again later",
    "code": "RATE_LIMIT_EXCEEDED",
    "retryAfter": 60
  }
}
```

## Examples

### JavaScript (Axios)

```javascript
// Register user
const register = async (userData) => {
  try {
    const response = await axios.post('/api/auth/register', userData);
    const { token, user } = response.data.data;
    
    // Store token
    localStorage.setItem('token', token);
    
    return { token, user };
  } catch (error) {
    console.error('Registration failed:', error.response.data.error.message);
    throw error;
  }
};

// Create order
const createOrder = async (orderData) => {
  try {
    const token = localStorage.getItem('token');
    const response = await axios.post('/api/orders', orderData, {
      headers: {
        'Authorization': `Bearer ${token}`,
        'Content-Type': 'application/json'
      }
    });
    
    return response.data.data.order;
  } catch (error) {
    console.error('Order creation failed:', error.response.data.error.message);
    throw error;
  }
};

// Get orders with pagination
const getOrders = async (page = 1, limit = 20) => {
  try {
    const token = localStorage.getItem('token');
    const response = await axios.get(`/api/orders?page=${page}&limit=${limit}`, {
      headers: {
        'Authorization': `Bearer ${token}`
      }
    });
    
    return response.data.data;
  } catch (error) {
    console.error('Failed to fetch orders:', error.response.data.error.message);
    throw error;
  }
};
```

### cURL Examples

```bash
# Register user
curl -X POST http://localhost:5000/api/auth/register \
  -H "Content-Type: application/json" \
  -d '{
    "shopName": "My Store",
    "ownerName": "John Doe",
    "email": "john@example.com",
    "password": "password123",
    "phone": "123-456-7890",
    "address": "123 Main St, City, State"
  }'

# Login user
curl -X POST http://localhost:5000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{
    "email": "john@example.com",
    "password": "password123"
  }'

# Create order (replace TOKEN with actual JWT)
curl -X POST http://localhost:5000/api/orders \
  -H "Authorization: Bearer TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "customerName": "Jane Smith",
    "customerPhone": "555-123-4567",
    "customerAddress": "456 Oak Ave, City, State",
    "product": "Laptop Computer"
  }'

# Get all orders
curl -X GET http://localhost:5000/api/orders \
  -H "Authorization: Bearer TOKEN"

# Search orders
curl -X GET "http://localhost:5000/api/orders/search?q=Jane" \
  -H "Authorization: Bearer TOKEN"
```

### Python (requests)

```python
import requests
import json

# Base URL
BASE_URL = "http://localhost:5000/api"

# Register user
def register_user():
    url = f"{BASE_URL}/auth/register"
    data = {
        "shopName": "My Store",
        "ownerName": "John Doe",
        "email": "john@example.com",
        "password": "password123",
        "phone": "123-456-7890",
        "address": "123 Main St, City, State"
    }
    
    response = requests.post(url, json=data)
    
    if response.status_code == 201:
        result = response.json()
        token = result['data']['token']
        return token
    else:
        print(f"Registration failed: {response.json()}")
        return None

# Create order
def create_order(token, order_data):
    url = f"{BASE_URL}/orders"
    headers = {
        "Authorization": f"Bearer {token}",
        "Content-Type": "application/json"
    }
    
    response = requests.post(url, json=data, headers=headers)
    
    if response.status_code == 201:
        return response.json()['data']['order']
    else:
        print(f"Order creation failed: {response.json()}")
        return None
```

## Testing

### Postman Collection

You can import the following Postman collection to test all endpoints:

```json
{
  "info": {
    "name": "MERN Order Management API",
    "description": "API collection for testing the MERN Order Management System"
  },
  "variable": [
    {
      "key": "baseUrl",
      "value": "http://localhost:5000/api"
    },
    {
      "key": "token",
      "value": ""
    }
  ]
}
```

### Automated Testing

For automated testing, ensure you have a test database configured and use the following test structure:

```javascript
// Example test using Jest and Supertest
describe('Order API', () => {
  let token;
  
  beforeAll(async () => {
    // Setup test user and get token
    const response = await request(app)
      .post('/api/auth/login')
      .send({
        email: 'test@example.com',
        password: 'testpassword'
      });
    
    token = response.body.data.token;
  });
  
  describe('POST /api/orders', () => {
    test('should create a new order', async () => {
      const orderData = {
        customerName: 'Test Customer',
        customerPhone: '123-456-7890',
        customerAddress: '123 Test St',
        product: 'Test Product'
      };
      
      const response = await request(app)
        .post('/api/orders')
        .set('Authorization', `Bearer ${token}`)
        .send(orderData)
        .expect(201);
      
      expect(response.body.success).toBe(true);
      expect(response.body.data.order.customerName).toBe(orderData.customerName);
    });
  });
});
```

This API documentation provides comprehensive information for developers to integrate with the MERN Order Management System.
