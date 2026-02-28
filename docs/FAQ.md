# Frequently Asked Questions (FAQ)

This document addresses common questions about the MERN Order Management System.

## Table of Contents

- [General Questions](#general-questions)
- [Installation and Setup](#installation-and-setup)
- [Authentication and Security](#authentication-and-security)
- [Order Management](#order-management)
- [Database and Data](#database-and-data)
- [Technical Issues](#technical-issues)
- [Deployment and Hosting](#deployment-and-hosting)
- [Customization and Development](#customization-and-development)
- [Troubleshooting](#troubleshooting)

## General Questions

### Q: What is the MERN Order Management System?

**A:** The MERN Order Management System is a full-stack web application built with MongoDB, Express.js, React, and Node.js. It's designed specifically for shopkeepers to manage customer orders and purchases with features like user authentication, order tracking, search functionality, and responsive design.

### Q: Who is this system for?

**A:** This system is designed for:
- Small to medium-sized retail shops
- Individual shopkeepers
- Service providers who need order tracking
- Businesses requiring simple order management

### Q: What are the main features?

**A:** Key features include:
- Secure user authentication and registration
- Order creation and management
- Real-time search across orders
- User-specific data isolation
- Responsive design for all devices
- Modern, intuitive interface

### Q: Is this system free to use?

**A:** Yes, the system is open-source and free to use. You can deploy it on your own infrastructure without any licensing fees. However, you'll need to pay for hosting services if you don't use free tiers.

### Q: Can I use this for multiple shops?

**A:** The current version supports one shop per user account. For multiple shops, you would need to create separate user accounts or wait for the multi-shop feature planned in version 2.0.0.

## Installation and Setup

### Q: What are the system requirements?

**A:** Minimum requirements:
- **Node.js**: Version 14 or higher
- **MongoDB**: Version 4.4 or higher
- **npm**: Version 6 or higher
- **RAM**: Minimum 2GB (4GB recommended)
- **Storage**: Minimum 1GB free space

### Q: How do I install the system locally?

**A:** Follow these steps:

1. **Clone the repository:**
   ```bash
   git clone https://github.com/yourusername/mern-order-system.git
   cd mern-order-system
   ```

2. **Install backend dependencies:**
   ```bash
   cd backend
   npm install
   ```

3. **Install frontend dependencies:**
   ```bash
   cd ../frontend
   npm install
   ```

4. **Set up environment variables:**
   ```bash
   cd ../backend
   cp .env.example .env
   # Edit .env with your configuration
   ```

5. **Start the application:**
   ```bash
   # Terminal 1 - Backend
   cd backend
   npm start

   # Terminal 2 - Frontend
   cd frontend
   npm start
   ```

### Q: Do I need to install MongoDB locally?

**A:** No, you can use MongoDB Atlas (cloud version) which has a free tier. However, for local development, installing MongoDB locally is recommended for faster development and offline work.

### Q: What should I put in the .env file?

**A:** Your `.env` file should contain:
```env
PORT=5000
MONGODB_URI=mongodb://localhost:27017/orderSystemDB
JWT_SECRET=your-super-secret-jwt-key-here
NODE_ENV=development
```

### Q: The installation failed. What should I do?

**A:** Try these troubleshooting steps:

1. **Clear npm cache:**
   ```bash
   npm cache clean --force
   ```

2. **Delete node_modules and package-lock.json:**
   ```bash
   rm -rf node_modules package-lock.json
   ```

3. **Reinstall dependencies:**
   ```bash
   npm install
   ```

4. **Check Node.js version:**
   ```bash
   node --version  # Should be 14 or higher
   ```

## Authentication and Security

### Q: How does the authentication work?

**A:** The system uses JWT (JSON Web Tokens) for authentication:
1. User registers/logs in with email and password
2. Server generates a JWT token containing user information
3. Token is stored in browser localStorage
4. Subsequent requests include the token in Authorization header
5. Server verifies token before processing requests

### Q: Is my data secure?

**A:** The system implements several security measures:
- Passwords are hashed using bcryptjs
- JWT tokens are used for authentication
- Input validation and sanitization
- CORS configuration to prevent cross-origin attacks
- Environment variables for sensitive data
- User-specific data isolation

### Q: How long do login sessions last?

**A:** By default:
- **Development**: 30 days
- **Production**: 7 days (recommended)

You can change this by modifying the `JWT_EXPIRE` environment variable.

### Q: Can I change my password?

**A:** The current version doesn't include a password change feature. This is planned for a future release. For now, you would need to:
1. Delete your user account from the database
2. Register again with a new password

### Q: What happens if I forget my password?

**A:** Currently, there's no password reset feature. You would need to:
1. Access the database directly
2. Delete your user account
3. Register again

This feature is planned for version 1.1.0.

### Q: Can multiple users access the same shop data?

**A:** No. Each user account is completely isolated. Users can only see and manage their own orders. This ensures data privacy and security.

## Order Management

### Q: What information can I store for each order?

**A:** Each order includes:
- Customer name
- Customer phone number
- Customer address
- Product name/description
- Order timestamp (automatically added)
- User ID (automatically linked)

### Q: Can I edit an order after creating it?

**A:** The current version only allows order deletion, not editing. Order editing is planned for version 1.1.0.

### Q: How does the search functionality work?

**A:** The search feature looks through:
- Customer name
- Customer phone number
- Product name
- Customer address

Search is case-insensitive and works in real-time as you type.

### Q: Can I export my orders?

**A:** Export functionality (PDF, CSV, Excel) is planned for version 1.1.0. Currently, you can manually copy the data from the interface.

### Q: Is there a limit to the number of orders I can store?

**A:** There's no software limit, but your hosting provider may have storage limits. MongoDB Atlas free tier allows up to 512MB of storage, which can hold thousands of orders.

### Q: Can I track order status (pending, completed, etc.)?

**A:** Order status tracking is planned for version 1.1.0. Currently, all orders are treated equally without status tracking.

## Database and Data

### Q: What database does the system use?

**A:** The system uses MongoDB, a NoSQL document database. It's accessed through Mongoose, which provides schema validation and convenient query methods.

### Q: Where is my data stored?

**A:** Your data is stored in MongoDB. If using MongoDB Atlas, it's stored in the cloud. If using local MongoDB, it's stored on your server/computer.

### Q: How do I backup my data?

**A:** For MongoDB Atlas:
1. Go to your cluster in Atlas dashboard
2. Click "Backups" tab
3. Configure automated backups
4. Or create manual backups

For local MongoDB:
```bash
# Create backup
mongodump --db orderSystemDB --out /path/to/backup

# Restore backup
mongorestore --db orderSystemDB /path/to/backup/orderSystemDB
```

### Q: Can I migrate my data to another system?

**A:** Yes, since MongoDB is a standard database, you can export your data and import it into another system. You can use tools like MongoDB Compass or command-line tools for data export.

### Q: What happens if I lose my data?

**A:** Data loss can occur if:
- Database server fails
- Accidental deletion
- Corruption

To prevent this:
- Set up regular backups
- Use MongoDB Atlas (which includes automated backups)
- Monitor your database health

## Technical Issues

### Q: I'm getting a CORS error. What should I do?

**A:** CORS errors occur when the frontend and backend are on different domains/ports. Solutions:

1. **Check backend CORS configuration:**
   ```javascript
   app.use(cors({
     origin: process.env.FRONTEND_URL || 'http://localhost:3000',
     credentials: true
   }));
   ```

2. **Ensure frontend URL is correct in environment variables**

3. **Make sure both frontend and backend are running**

### Q: The application is running slowly. How can I improve performance?

**A:** Performance optimization tips:

1. **Database optimization:**
   - Add indexes to frequently queried fields
   - Use pagination for large datasets
   - Optimize query performance

2. **Frontend optimization:**
   - Implement code splitting
   - Use React.memo for expensive components
   - Optimize images and assets

3. **Backend optimization:**
   - Use caching for frequently accessed data
   - Implement rate limiting
   - Monitor and optimize slow queries

### Q: I'm getting "Token expired" errors frequently.

**A:** This happens when:
- JWT token has expired
- System time is incorrect
- Token is corrupted

Solutions:
1. **Check JWT_EXPIRE environment variable**
2. **Ensure system time is correct**
3. **Clear browser localStorage and login again**

### Q: The application crashes when I have many orders.

**A:** This could be due to:
- Memory limitations
- Inefficient database queries
- Large data rendering

Solutions:
1. **Implement pagination**
2. **Add database indexes**
3. **Optimize frontend rendering**
4. **Increase server memory if needed**

### Q: My search is not working correctly.

**A:** Check these items:
1. **Database indexes are created:**
   ```javascript
   db.orders.createIndex({
     customerName: "text",
     customerPhone: "text",
     product: "text",
     customerAddress: "text"
   });
   ```

2. **Search query is properly formatted**
3. **Backend search endpoint is accessible**

## Deployment and Hosting

### Q: Can I deploy this for free?

**A:** Yes, you can deploy for free using:
- **Frontend**: Vercel, Netlify, GitHub Pages
- **Backend**: Railway, Render (free tiers)
- **Database**: MongoDB Atlas (free tier)

### Q: Which hosting platform do you recommend?

**A:** Recommended combinations:
- **Beginner**: Vercel (frontend) + Railway (backend) + MongoDB Atlas
- **Intermediate**: Netlify (frontend) + Render (backend) + MongoDB Atlas
- **Advanced**: AWS S3/CloudFront + EC2 + MongoDB Atlas

### Q: Do I need to buy a domain?

**A:** No, you can use the provided subdomains:
- your-app.vercel.app
- your-app.railway.app

However, a custom domain is recommended for professional use.

### Q: How do I set up HTTPS/SSL?

**A:** Most modern hosting providers provide automatic SSL certificates:
- Vercel, Netlify: Automatic HTTPS
- Railway, Render: Automatic HTTPS
- Custom setup: Use Let's Encrypt certificates

### Q: Can I use this on my own server?

**A:** Yes, you can self-host on your own server:
1. Install Node.js and MongoDB
2. Configure Nginx as reverse proxy
3. Set up SSL certificates
4. Configure firewall and security

## Customization and Development

### Q: Can I customize the design?

**A:** Yes, the design is fully customizable:
1. **Colors**: Edit `frontend/src/App.css`
2. **Layout**: Modify React components
3. **Typography**: Update CSS variables
4. **Branding**: Replace logos and colors

### Q: Can I add new features?

**A:** Absolutely! The system is open-source. You can:
1. Fork the repository
2. Create a new branch
3. Implement your features
4. Submit a pull request

### Q: How do I add new fields to orders?

**A:** To add new fields:

1. **Update the Order model** (`backend/models/Order.js`):
   ```javascript
   const orderSchema = new mongoose.Schema({
     // existing fields...
     newField: {
       type: String,
       required: true
     }
   });
   ```

2. **Update the frontend form** (`frontend/src/components/OrderForm.js`)

3. **Update the display component** (`frontend/src/components/PurchasedProducts.js`)

### Q: Can I integrate with other services?

**A:** Yes, you can integrate with:
- Payment gateways (Stripe, PayPal)
- Email services (SendGrid, Mailgun)
- SMS services (Twilio)
- Analytics (Google Analytics)
- CRM systems

### Q: How do I add API endpoints?

**A:** To add new endpoints:

1. **Create route handler** in `backend/app.js`:
   ```javascript
   app.get('/api/new-endpoint', authenticateToken, (req, res) => {
     // Your logic here
   });
   ```

2. **Add authentication middleware** if needed

3. **Test the endpoint** with tools like Postman

## Troubleshooting

### Q: The application won't start. What should I check?

**A:** Check these items in order:

1. **Node.js is installed:**
   ```bash
   node --version
   ```

2. **Dependencies are installed:**
   ```bash
   npm list
   ```

3. **Environment variables are set:**
   ```bash
   cat .env
   ```

4. **MongoDB is running:**
   ```bash
   mongo --eval "db.adminCommand('ismaster')"
   ```

5. **Ports are available:**
   ```bash
   netstat -an | grep :3000
   netstat -an | grep :5000
   ```

### Q: I'm getting database connection errors.

**A:** Troubleshoot database connections:

1. **Check MongoDB URI format:**
   ```
   mongodb://localhost:27017/orderSystemDB
   ```

2. **Verify MongoDB is running:**
   ```bash
   sudo systemctl status mongod
   ```

3. **Check network connectivity**
4. **Verify database credentials**
5. **Check IP whitelisting (MongoDB Atlas)**

### Q: My changes aren't showing up in the browser.

**A:** Try these solutions:

1. **Clear browser cache**
2. **Hard refresh** (Ctrl+F5 or Cmd+Shift+R)
3. **Check if backend is restarted**
4. **Verify build process completed**
5. **Check browser console for errors**

### Q: The mobile version looks broken.

**A:** Check responsive design:

1. **Test with browser dev tools** (mobile device simulation)
2. **Check CSS media queries**
3. **Verify viewport meta tag** in `public/index.html`
4. **Test on actual mobile devices**

### Q: I need help with a specific issue.

**A:** Get help through:

1. **Check the documentation** in the `docs/` folder
2. **Search existing issues** on GitHub
3. **Create a new issue** with detailed information
4. **Join the community** (Discord, forums)

---

## Still Have Questions?

If your question isn't answered here, please:

1. **Check the comprehensive documentation** in the `docs/` folder
2. **Search the GitHub issues** for similar problems
3. **Create a new issue** with:
   - Detailed description of the problem
   - Steps to reproduce
   - Your environment details
   - Error messages/screenshots

4. **Join our community** for real-time help:
   - Discord: [Community Server]
   - GitHub Discussions: [Project Discussions]

We're here to help you succeed with the MERN Order Management System!
