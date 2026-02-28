# Contributing to MERN Order Management System

Thank you for your interest in contributing to the MERN Order Management System! This document provides guidelines and information for contributors.

## Table of Contents

- [Getting Started](#getting-started)
- [Development Setup](#development-setup)
- [Code Style Guidelines](#code-style-guidelines)
- [Contribution Types](#contribution-types)
- [Pull Request Process](#pull-request-process)
- [Issue Reporting](#issue-reporting)
- [Testing Guidelines](#testing-guidelines)
- [Documentation](#documentation)

## Getting Started

### Prerequisites

Before contributing, ensure you have:

- Node.js (v14 or higher)
- MongoDB (v4.4 or higher)
- Git
- Basic knowledge of MERN stack (MongoDB, Express.js, React, Node.js)
- Understanding of JWT authentication

### Fork and Clone

1. Fork the repository
2. Clone your fork locally:
   ```bash
   git clone https://github.com/yourusername/mern-order-system.git
   cd mern-order-system
   ```
3. Add the original repository as upstream:
   ```bash
   git remote add upstream https://github.com/original-owner/mern-order-system.git
   ```

## Development Setup

### 1. Install Dependencies

```bash
# Backend dependencies
cd backend
npm install

# Frontend dependencies
cd ../frontend
npm install
```

### 2. Environment Configuration

Create a `.env` file in the `backend` directory:

```env
PORT=5000
MONGODB_URI=mongodb://localhost:27017/orderSystemDB
JWT_SECRET=your-super-secret-jwt-key-here
NODE_ENV=development
```

### 3. Start Development Servers

```bash
# Terminal 1 - Backend
cd backend
npm run dev

# Terminal 2 - Frontend
cd frontend
npm start
```

## Code Style Guidelines

### General Principles

- Write clean, readable, and maintainable code
- Follow consistent naming conventions
- Add meaningful comments where necessary
- Keep functions small and focused

### JavaScript/Node.js Guidelines

- Use `const` and `let` instead of `var`
- Use arrow functions for callbacks
- Use async/await for asynchronous operations
- Handle errors appropriately
- Follow ES6+ syntax

```javascript
// Good
const getUserOrders = async (userId) => {
  try {
    const orders = await Order.find({ userId });
    return orders;
  } catch (error) {
    console.error('Error fetching orders:', error);
    throw error;
  }
};

// Avoid
function getUserOrders(userId) {
  return Order.find({ userId }).then(function(orders) {
    return orders;
  }).catch(function(error) {
    console.log('Error fetching orders:', error);
  });
}
```

### React Guidelines

- Use functional components with hooks
- Keep components small and focused
- Use descriptive prop names
- Implement proper error boundaries
- Use React Context for state management when appropriate

```jsx
// Good
const OrderForm = ({ onSubmit, initialData }) => {
  const [formData, setFormData] = useState({
    customerName: '',
    customerPhone: '',
    customerAddress: '',
    product: ''
  });

  const handleSubmit = (e) => {
    e.preventDefault();
    onSubmit(formData);
  };

  return (
    <form onSubmit={handleSubmit}>
      {/* Form fields */}
    </form>
  );
};
```

### CSS Guidelines

- Use consistent naming conventions (BEM or camelCase)
- Organize styles logically
- Use CSS variables for colors and spacing
- Ensure responsive design
- Follow mobile-first approach

```css
/* Good */
.order-card {
  background: white;
  border-radius: 8px;
  padding: 1rem;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.order-card__header {
  font-weight: 600;
  margin-bottom: 0.5rem;
}

.order-card__content {
  color: #666;
}
```

## Contribution Types

We welcome the following types of contributions:

### 🐛 Bug Fixes

- Fix broken functionality
- Resolve UI/UX issues
- Address performance problems
- Fix security vulnerabilities

### ✨ Features

- Add new functionality
- Improve user experience
- Enhance existing features
- Add integrations

### 📚 Documentation

- Improve README
- Add code comments
- Create tutorials
- Update API documentation

### 🎨 Design

- UI/UX improvements
- Responsive design fixes
- Accessibility enhancements
- Visual design updates

### ⚡ Performance

- Optimize database queries
- Improve frontend performance
- Reduce bundle size
- Enhance loading times

## Pull Request Process

### 1. Create a Branch

```bash
git checkout -b feature/your-feature-name
# or
git checkout -b fix/your-bug-fix
```

### 2. Make Changes

- Write clean, tested code
- Follow the coding guidelines
- Update documentation if needed
- Ensure all tests pass

### 3. Commit Changes

Use conventional commit messages:

```bash
# Features
git commit -m "feat: add order search functionality"

# Bug fixes
git commit -m "fix: resolve login authentication issue"

# Documentation
git commit -m "docs: update API documentation"

# Style changes
git commit -m "style: improve responsive design"

# Refactoring
git commit -m "refactor: optimize database queries"
```

### 4. Push and Create Pull Request

```bash
git push origin feature/your-feature-name
```

Then create a pull request with:

- Clear title and description
- Reference related issues
- Include screenshots for UI changes
- Describe testing performed

### 5. Code Review

- Respond to review comments promptly
- Make requested changes
- Keep the PR updated
- Be respectful and constructive

## Issue Reporting

### Bug Reports

When reporting bugs, include:

- Clear description of the issue
- Steps to reproduce
- Expected vs actual behavior
- Environment details (OS, browser, Node.js version)
- Screenshots if applicable
- Error messages/logs

### Feature Requests

When requesting features, include:

- Clear description of the feature
- Use case and benefits
- Implementation ideas (optional)
- Potential challenges/considerations

### Issue Template

```markdown
## Issue Type
- [ ] Bug
- [ ] Feature Request
- [ ] Documentation
- [ ] Other

## Description
[Clear description of the issue or request]

## Steps to Reproduce (for bugs)
1. Go to...
2. Click on...
3. See error

## Expected Behavior
[What should happen]

## Actual Behavior
[What actually happens]

## Environment
- OS: [e.g., Windows 10, macOS 12.0]
- Browser: [e.g., Chrome 96, Firefox 95]
- Node.js: [e.g., 16.14.0]
- MongoDB: [e.g., 5.0.3]

## Additional Context
[Any additional information, screenshots, etc.]
```

## Testing Guidelines

### Backend Testing

- Test all API endpoints
- Verify authentication middleware
- Test database operations
- Check error handling

```javascript
// Example test structure
describe('Order API', () => {
  test('should create a new order', async () => {
    const orderData = {
      customerName: 'John Doe',
      customerPhone: '123-456-7890',
      customerAddress: '123 Main St',
      product: 'Laptop'
    };
    
    const response = await request(app)
      .post('/api/orders')
      .set('Authorization', `Bearer ${token}`)
      .send(orderData)
      .expect(201);
      
    expect(response.body.success).toBe(true);
  });
});
```

### Frontend Testing

- Test component rendering
- Verify user interactions
- Test form submissions
- Check error states

```javascript
// Example React test
import { render, screen, fireEvent } from '@testing-library/react';
import OrderForm from './OrderForm';

test('should submit order form', () => {
  const mockSubmit = jest.fn();
  render(<OrderForm onSubmit={mockSubmit} />);
  
  fireEvent.change(screen.getByLabelText('Customer Name'), {
    target: { value: 'John Doe' }
  });
  
  fireEvent.click(screen.getByText('Add Order'));
  
  expect(mockSubmit).toHaveBeenCalledWith({
    customerName: 'John Doe',
    // ... other fields
  });
});
```

### Manual Testing

- Test all user flows
- Verify responsive design
- Check cross-browser compatibility
- Test authentication flows

## Documentation

### Code Documentation

- Add JSDoc comments for functions
- Document complex logic
- Explain API endpoints
- Include usage examples

```javascript
/**
 * Creates a new order in the database
 * @param {Object} orderData - Order information
 * @param {string} orderData.customerName - Customer's full name
 * @param {string} orderData.customerPhone - Customer's phone number
 * @param {string} orderData.customerAddress - Customer's address
 * @param {string} orderData.product - Product name
 * @param {string} userId - User ID who created the order
 * @returns {Promise<Object>} Created order object
 */
const createOrder = async (orderData, userId) => {
  // Implementation
};
```

### README Updates

When adding features, update:
- Installation instructions
- Usage examples
- API documentation
- Configuration options

## Community Guidelines

### Code of Conduct

- Be respectful and inclusive
- Welcome newcomers and help them learn
- Focus on constructive feedback
- Avoid personal attacks or criticism

### Communication

- Use clear and professional language
- Ask questions when unsure
- Provide helpful feedback
- Share knowledge and experiences

## Getting Help

If you need help:

1. Check existing documentation
2. Search for similar issues
3. Ask questions in discussions
4. Contact maintainers

## Recognition

Contributors will be:

- Listed in the README
- Mentioned in release notes
- Invited to become maintainers (for significant contributions)

Thank you for contributing to the MERN Order Management System! 🚀
