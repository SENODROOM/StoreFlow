# Architecture Documentation

This document describes the architecture of the MERN Order Management System, including system design, data flow, and technical decisions.

## Table of Contents

- [Overview](#overview)
- [System Architecture](#system-architecture)
- [Technology Stack](#technology-stack)
- [Data Flow](#data-flow)
- [Component Architecture](#component-architecture)
- [Database Design](#database-design)
- [Security Architecture](#security-architecture)
- [API Design](#api-design)
- [Frontend Architecture](#frontend-architecture)
- [State Management](#state-management)
- [Error Handling](#error-handling)
- [Performance Considerations](#performance-considerations)
- [Scalability](#scalability)

## Overview

The MERN Order Management System is a full-stack web application designed for shopkeepers to manage customer orders and purchases. The system follows a client-server architecture with clear separation of concerns between frontend and backend components.

### Key Architectural Principles

- **Separation of Concerns**: Clear distinction between frontend, backend, and database layers
- **RESTful API Design**: Standard HTTP methods and status codes
- **JWT-based Authentication**: Stateless authentication with JSON Web Tokens
- **Data Isolation**: Each user can only access their own data
- **Responsive Design**: Mobile-first approach with progressive enhancement
- **Component-based Architecture**: Modular, reusable components

## System Architecture

```
┌─────────────────┐    HTTP/HTTPS    ┌─────────────────┐    ┌─────────────────┐
│   Frontend      │ ◄──────────────► │   Backend       │ ◄──► │   Database      │
│   (React SPA)   │                  │   (Express.js)  │      │   (MongoDB)     │
│                 │                  │                 │      │                 │
│ - UI Components │                  │ - REST API      │      │ - Orders        │
│ - State Mgmt    │                  │ - Auth Middleware│     │ - Users         │
│ - Routing       │                  │ - Business Logic│      │ - Indexes       │
│ - HTTP Client   │                  │ - Validation    │      │                 │
└─────────────────┘                  └─────────────────┘      └─────────────────┘
```

### Architecture Layers

#### 1. Presentation Layer (Frontend)
- **React SPA**: Single Page Application with client-side routing
- **Component-based UI**: Reusable React components
- **State Management**: React Context API for global state
- **HTTP Client**: Axios for API communication

#### 2. Application Layer (Backend)
- **Express.js Server**: RESTful API server
- **Authentication Middleware**: JWT token verification
- **Route Handlers**: Business logic implementation
- **Data Validation**: Input sanitization and validation

#### 3. Data Layer (Database)
- **MongoDB**: NoSQL document database
- **Mongoose ODM**: Object Data Modeling for MongoDB
- **Data Models**: Structured schemas for Users and Orders

## Technology Stack

### Frontend Technologies

| Technology | Version | Purpose |
|------------|---------|---------|
| React | 18.2.0 | UI Library |
| React Router DOM | 6.20.0 | Client-side routing |
| Axios | 1.6.2 | HTTP client |
| React Scripts | 5.0.1 | Build tooling |
| CSS3 | - | Styling and animations |

### Backend Technologies

| Technology | Version | Purpose |
|------------|---------|---------|
| Node.js | 14+ | JavaScript runtime |
| Express.js | 4.18.2 | Web framework |
| MongoDB | 4.4+ | Database |
| Mongoose | 8.0.0 | ODM |
| JWT | 9.0.2 | Authentication |
| bcryptjs | 2.4.3 | Password hashing |
| CORS | 2.8.5 | Cross-origin requests |
| dotenv | 16.3.1 | Environment variables |

## Data Flow

### Authentication Flow

```
User → Frontend → Backend → Database
  ↓      ↓         ↓         ↓
Login → POST /api/auth/login → Verify credentials → Generate JWT → Store token
  ↓      ↓         ↓         ↓
      Store JWT in localStorage
  ↓      ↓         ↓         ↓
Subsequent requests include JWT → Verify middleware → Allow access
```

### Order Management Flow

```
User Action → Frontend Component → API Call → Backend Route → Database
    ↓               ↓                ↓           ↓            ↓
Create Order → OrderForm Component → POST /api/orders → Validation → Save Order
    ↓               ↓                ↓           ↓            ↓
View Orders → PurchasedProducts → GET /api/orders → Auth Check → Fetch Orders
    ↓               ↓                ↓           ↓            ↓
Search Orders → Search Input → GET /api/orders/search → Filter → Return Results
    ↓               ↓                ↓           ↓            ↓
Delete Order → Delete Button → DELETE /api/orders/:id → Auth Check → Remove Order
```

## Component Architecture

### Frontend Component Hierarchy

```
App.js
├── AuthContext.js (Global State)
├── Router Setup
│   ├── Public Routes
│   │   ├── Login.js
│   │   └── Register.js
│   └── Protected Routes (PrivateRoute.js)
│       ├── OrderForm.js
│       └── PurchasedProducts.js
├── Navigation
└── Styling (App.css, index.css)
```

### Component Responsibilities

#### AuthContext.js
- Global authentication state management
- User data persistence
- Login/logout functionality
- Token management

#### PrivateRoute.js
- Route protection wrapper
- Authentication verification
- Redirect logic for unauthenticated users

#### OrderForm.js
- Order creation interface
- Form validation
- API integration for order submission
- Success/error handling

#### PurchasedProducts.js
- Order display and management
- Search functionality
- Delete operations
- Real-time data fetching

## Database Design

### User Schema

```javascript
{
  _id: ObjectId,
  shopName: String (required),
  ownerName: String (required),
  email: String (required, unique),
  password: String (required, hashed),
  phone: String (required),
  address: String (required),
  createdAt: Date (default: Date.now),
  updatedAt: Date (default: Date.now)
}
```

### Order Schema

```javascript
{
  _id: ObjectId,
  customerName: String (required),
  customerPhone: String (required),
  customerAddress: String (required),
  product: String (required),
  userId: ObjectId (required, ref: 'User'),
  createdAt: Date (default: Date.now),
  updatedAt: Date (default: Date.now)
}
```

### Database Relationships

- **One-to-Many**: One User can have many Orders
- **Foreign Key**: Order.userId references User._id
- **Data Isolation**: Orders are filtered by userId in all queries

### Indexes

```javascript
// User indexes
db.users.createIndex({ email: 1 }, { unique: true })

// Order indexes
db.orders.createIndex({ userId: 1 })
db.orders.createIndex({ createdAt: -1 })
db.orders.createIndex({ 
  customerName: "text", 
  customerPhone: "text", 
  product: "text", 
  customerAddress: "text" 
})
```

## Security Architecture

### Authentication & Authorization

#### JWT Token Structure
```javascript
{
  "header": {
    "alg": "HS256",
    "typ": "JWT"
  },
  "payload": {
    "userId": ObjectId,
    "email": String,
    "iat": Number (issued at),
    "exp": Number (expiration)
  }
}
```

#### Security Measures
- **Password Hashing**: bcryptjs with salt rounds
- **JWT Secret**: Environment variable-based secret key
- **Token Expiration**: 30 days default
- **Input Validation**: Server-side validation for all inputs
- **CORS Configuration**: Restricted to specific origins
- **Environment Variables**: Sensitive data not in code

#### Middleware Security
```javascript
// Authentication middleware flow
Request → Extract JWT → Verify Token → Find User → Attach to Request → Continue
    ↓           ↓            ↓           ↓              ↓           ↓
  Error if    Invalid      User not    User found    req.user =   Next
  missing     token        found       in database   user object
```

## API Design

### RESTful Endpoints

#### Authentication Endpoints
```
POST   /api/auth/register    - Create new user account
POST   /api/auth/login       - Authenticate user and get token
GET    /api/auth/me          - Get current user info
```

#### Order Endpoints (Protected)
```
GET    /api/orders           - Get all orders for authenticated user
POST   /api/orders           - Create new order
GET    /api/orders/search    - Search orders (query parameter)
DELETE /api/orders/:id       - Delete specific order
```

### Response Format

#### Success Response
```javascript
{
  "success": true,
  "data": {
    // Response data
  },
  "message": "Operation successful"
}
```

#### Error Response
```javascript
{
  "success": false,
  "error": {
    "message": "Error description",
    "code": "ERROR_CODE"
  }
}
```

### HTTP Status Codes

| Status | Usage |
|--------|-------|
| 200 | Successful GET request |
| 201 | Successful POST (resource created) |
| 400 | Bad request (validation error) |
| 401 | Unauthorized (authentication required) |
| 403 | Forbidden (insufficient permissions) |
| 404 | Resource not found |
| 500 | Internal server error |

## Frontend Architecture

### Component Design Patterns

#### Functional Components with Hooks
```javascript
const OrderForm = () => {
  const [formData, setFormData] = useState(initialState);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(null);
  
  const { user } = useContext(AuthContext);
  
  const handleSubmit = async (e) => {
    // Form submission logic
  };
  
  return (
    <form onSubmit={handleSubmit}>
      {/* JSX */}
    </form>
  );
};
```

#### Custom Hooks (Potential Enhancement)
```javascript
const useOrders = () => {
  const [orders, setOrders] = useState([]);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(null);
  
  // Custom hook logic
  
  return { orders, loading, error, fetchOrders, createOrder };
};
```

### Routing Architecture

```javascript
// Route configuration
const App = () => {
  return (
    <Router>
      <Routes>
        <Route path="/login" element={<Login />} />
        <Route path="/register" element={<Register />} />
        <Route path="/" element={
          <PrivateRoute>
            <OrderForm />
          </PrivateRoute>
        } />
        <Route path="/orders" element={
          <PrivateRoute>
            <PurchasedProducts />
          </PrivateRoute>
        } />
      </Routes>
    </Router>
  );
};
```

## State Management

### Global State (AuthContext)

```javascript
const AuthContext = createContext();

const AuthProvider = ({ children }) => {
  const [user, setUser] = useState(null);
  const [token, setToken] = useState(localStorage.getItem('token'));
  const [loading, setLoading] = useState(true);
  
  // Authentication logic
  
  return (
    <AuthContext.Provider value={{ user, token, login, logout, loading }}>
      {children}
    </AuthContext.Provider>
  );
};
```

### Local Component State

- **Form State**: Managed with useState hooks
- **UI State**: Loading states, error messages, form validation
- **Derived State**: Computed values from props and state

### State Flow

```
User Action → Component State → API Call → Global State Update → UI Re-render
```

## Error Handling

### Frontend Error Handling

#### Component-Level Error Boundaries
```javascript
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }
  
  static getDerivedStateFromError(error) {
    return { hasError: true };
  }
  
  componentDidCatch(error, errorInfo) {
    console.error('Error caught by boundary:', error, errorInfo);
  }
  
  render() {
    if (this.state.hasError) {
      return <h1>Something went wrong.</h1>;
    }
    return this.props.children;
  }
}
```

#### API Error Handling
```javascript
const createOrder = async (orderData) => {
  try {
    const response = await axios.post('/api/orders', orderData, {
      headers: { Authorization: `Bearer ${token}` }
    });
    return response.data;
  } catch (error) {
    if (error.response) {
      // Server responded with error status
      throw new Error(error.response.data.error.message);
    } else if (error.request) {
      // Request was made but no response
      throw new Error('Network error. Please try again.');
    } else {
      // Something else happened
      throw new Error('An unexpected error occurred.');
    }
  }
};
```

### Backend Error Handling

#### Global Error Handler
```javascript
const errorHandler = (err, req, res, next) => {
  let error = { ...err };
  error.message = err.message;
  
  // Log error
  console.error(err);
  
  // Mongoose validation error
  if (err.name === 'ValidationError') {
    const message = Object.values(err.errors).map(val => val.message);
    error = { message: message.join(', '), statusCode: 400 };
  }
  
  // Mongoose duplicate key error
  if (err.code === 11000) {
    const message = 'Resource already exists';
    error = { message, statusCode: 400 };
  }
  
  res.status(error.statusCode || 500).json({
    success: false,
    error: {
      message: error.message || 'Server Error'
    }
  });
};
```

## Performance Considerations

### Frontend Optimization

#### Code Splitting
```javascript
// Lazy loading components
const OrderForm = lazy(() => import('./components/OrderForm'));
const PurchasedProducts = lazy(() => import('./components/PurchasedProducts'));

// Usage with Suspense
<Suspense fallback={<div>Loading...</div>}>
  <OrderForm />
</Suspense>
```

#### Memoization
```javascript
// React.memo for component optimization
const OrderCard = React.memo(({ order, onDelete }) => {
  return (
    // Component JSX
  );
});

// useMemo for expensive computations
const filteredOrders = useMemo(() => {
  return orders.filter(order => 
    order.customerName.toLowerCase().includes(searchTerm.toLowerCase())
  );
}, [orders, searchTerm]);
```

### Backend Optimization

#### Database Query Optimization
```javascript
// Efficient queries with lean()
const orders = await Order.find({ userId })
  .lean() // Returns plain JavaScript objects
  .sort({ createdAt: -1 })
  .limit(50); // Pagination

// Indexed queries for search
const searchResults = await Order.find({
  $text: { $search: query },
  userId
}).lean();
```

#### Caching Strategy
```javascript
// In-memory caching for frequently accessed data
const cache = new Map();

const getCachedData = (key) => {
  if (cache.has(key)) {
    return cache.get(key);
  }
  
  const data = fetchDataFromDatabase(key);
  cache.set(key, data);
  
  // Set expiration
  setTimeout(() => cache.delete(key), 300000); // 5 minutes
  
  return data;
};
```

## Scalability

### Horizontal Scaling Considerations

#### Backend Scaling
- **Stateless Design**: JWT authentication enables multiple server instances
- **Database Connection Pooling**: Efficient database connection management
- **Load Balancing**: Multiple backend instances behind a load balancer
- **Microservices Ready**: Modular design allows service separation

#### Database Scaling
- **Read Replicas**: Separate read and write operations
- **Sharding**: Distribute data across multiple servers
- **Indexing Strategy**: Optimized queries for large datasets
- **Data Archival**: Move old orders to archival storage

#### Frontend Scaling
- **CDN Deployment**: Static assets served via CDN
- **Progressive Web App**: Offline capabilities
- **Component Lazy Loading**: Reduce initial bundle size
- **Service Workers**: Caching strategies

### Performance Monitoring

#### Metrics to Track
- **Response Times**: API endpoint performance
- **Database Query Times**: Slow query identification
- **Error Rates**: Application stability
- **User Engagement**: Frontend performance metrics
- **Resource Usage**: Server resource consumption

#### Monitoring Tools
- **Application Performance Monitoring (APM)**
- **Database Performance Monitoring**
- **Real User Monitoring (RUM)**
- **Error Tracking Services**

This architecture provides a solid foundation for the MERN Order Management System while allowing for future growth and enhancements.
