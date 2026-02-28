# Deployment Guide

This guide provides comprehensive instructions for deploying the MERN Order Management System to various hosting platforms.

## Table of Contents

- [Prerequisites](#prerequisites)
- [Environment Configuration](#environment-configuration)
- [Backend Deployment](#backend-deployment)
- [Frontend Deployment](#frontend-deployment)
- [Database Setup](#database-setup)
- [SSL/HTTPS Configuration](#sslhttps-configuration)
- [Monitoring and Logging](#monitoring-and-logging)
- [Backup and Recovery](#backup-and-recovery)
- [Troubleshooting](#troubleshooting)

## Prerequisites

### Required Accounts and Services

- **Git Repository**: GitHub, GitLab, or Bitbucket
- **Domain Name**: Custom domain (optional but recommended)
- **Email Service**: For transactional emails (optional)
- **Monitoring Service**: For application monitoring (optional)

### Development Environment

Ensure your application is fully tested and ready for production:

```bash
# Run tests
npm test

# Build frontend
cd frontend
npm run build

# Verify production build locally
npm install -g serve
serve -s build -l 3000
```

## Environment Configuration

### Production Environment Variables

Create a production `.env` file in the backend directory:

```env
# Server Configuration
NODE_ENV=production
PORT=5000

# Database Configuration
MONGODB_URI=mongodb+srv://username:password@cluster.mongodb.net/orderSystemDB?retryWrites=true&w=majority

# JWT Configuration
JWT_SECRET=your-super-secure-jwt-secret-key-for-production
JWT_EXPIRE=7d

# CORS Configuration
FRONTEND_URL=https://your-domain.com

# Email Configuration (Optional)
EMAIL_HOST=smtp.gmail.com
EMAIL_PORT=587
EMAIL_USER=your-email@gmail.com
EMAIL_PASS=your-app-password

# Monitoring (Optional)
SENTRY_DSN=your-sentry-dsn
```

### Security Considerations

- **JWT Secret**: Use a strong, randomly generated secret (minimum 32 characters)
- **Database URI**: Use MongoDB Atlas with IP whitelisting
- **Environment Variables**: Never commit secrets to version control
- **HTTPS**: Always use HTTPS in production

## Backend Deployment

### Option 1: Railway

Railway provides an easy deployment experience for Node.js applications.

#### Step 1: Prepare for Railway

1. Create a `Procfile` in the backend root:
```procfile
web: npm start
```

2. Update `package.json` with engines:
```json
{
  "engines": {
    "node": "18.x",
    "npm": "8.x"
  }
}
```

#### Step 2: Deploy to Railway

1. Sign up at [Railway](https://railway.app)
2. Connect your GitHub repository
3. Configure environment variables in Railway dashboard
4. Deploy automatically on push to main branch

#### Step 3: Verify Deployment

```bash
# Test the deployed API
curl https://your-app.railway.app/api/auth/me
```

### Option 2: Render

Render provides excellent Node.js hosting with automatic deployments.

#### Step 1: Configure for Render

1. Create `render.yaml` in project root:
```yaml
services:
  - type: web
    name: order-management-api
    env: node
    repo: https://github.com/yourusername/mern-order-system
    rootDir: backend
    buildCommand: npm install
    startCommand: npm start
    envVars:
      - key: NODE_ENV
        value: production
      - key: MONGODB_URI
        sync: false
      - key: JWT_SECRET
        sync: false
```

#### Step 2: Deploy to Render

1. Sign up at [Render](https://render.com)
2. Connect your GitHub repository
3. Configure environment variables
4. Deploy automatically

### Option 3: Heroku (Legacy)

Heroku still supports Node.js deployments but requires a paid dyno.

#### Step 1: Prepare for Heroku

1. Create `Procfile`:
```procfile
web: npm start
```

2. Add engines to `package.json`:
```json
{
  "engines": {
    "node": "18.x",
    "npm": "8.x"
  }
}
```

#### Step 2: Deploy to Heroku

```bash
# Install Heroku CLI
npm install -g heroku

# Login to Heroku
heroku login

# Create Heroku app
heroku create your-app-name

# Set environment variables
heroku config:set NODE_ENV=production
heroku config:set MONGODB_URI=your-mongodb-uri
heroku config:set JWT_SECRET=your-jwt-secret

# Deploy
git push heroku main
```

### Option 4: DigitalOcean App Platform

#### Step 1: Configure App Spec

Create `.do/app.yaml`:
```yaml
name: order-management-api
services:
- name: api
  source_dir: backend
  github:
    repo: yourusername/mern-order-system
    branch: main
  run_command: npm start
  environment_slug: node-js
  instance_count: 1
  instance_size_slug: basic-xxs
  envs:
  - key: NODE_ENV
    value: production
  - key: MONGODB_URI
    value: ${DATABASE_URL}
  - key: JWT_SECRET
    value: ${JWT_SECRET}
```

#### Step 2: Deploy

1. Create DigitalOcean account
2. Connect GitHub repository
3. Configure environment variables
4. Deploy

## Frontend Deployment

### Option 1: Vercel

Vercel provides excellent React hosting with automatic deployments.

#### Step 1: Configure Vercel

Create `vercel.json` in frontend directory:
```json
{
  "version": 2,
  "builds": [
    {
      "src": "package.json",
      "use": "@vercel/static-build",
      "config": {
        "distDir": "build"
      }
    }
  ],
  "routes": [
    {
      "src": "/(.*)",
      "dest": "/index.html"
    }
  ],
  "env": {
    "REACT_APP_API_URL": "https://your-backend-url.com/api"
  }
}
```

#### Step 2: Update API Configuration

Modify `src/utils/api.js` or equivalent:
```javascript
const API_BASE_URL = process.env.REACT_APP_API_URL || 'http://localhost:5000/api';

const api = axios.create({
  baseURL: API_BASE_URL,
  headers: {
    'Content-Type': 'application/json'
  }
});
```

#### Step 3: Deploy to Vercel

1. Sign up at [Vercel](https://vercel.com)
2. Connect your GitHub repository
3. Configure environment variables
4. Deploy automatically

### Option 2: Netlify

Netlify provides excellent static site hosting.

#### Step 1: Configure Netlify

Create `netlify.toml` in frontend directory:
```toml
[build]
  publish = "build"
  command = "npm run build"

[build.environment]
  REACT_APP_API_URL = "https://your-backend-url.com/api"

[[redirects]]
  from = "/*"
  to = "/index.html"
  status = 200
```

#### Step 2: Deploy to Netlify

1. Sign up at [Netlify](https://netlify.com)
2. Connect your GitHub repository
3. Configure build settings
4. Deploy automatically

### Option 3: GitHub Pages

#### Step 1: Configure for GitHub Pages

Update `package.json` in frontend:
```json
{
  "homepage": "https://yourusername.github.io/mern-order-system",
  "scripts": {
    "predeploy": "npm run build",
    "deploy": "gh-pages -d build"
  }
}
```

Install gh-pages:
```bash
npm install --save-dev gh-pages
```

#### Step 2: Deploy

```bash
npm run deploy
```

### Option 4: AWS S3 + CloudFront

#### Step 1: Build and Upload

```bash
# Build the application
npm run build

# Install AWS CLI
npm install -g aws-cli

# Configure AWS CLI
aws configure

# Sync to S3
aws s3 sync build/ s3://your-bucket-name --delete
```

#### Step 2: Configure CloudFront

1. Create CloudFront distribution
2. Set origin to S3 bucket
3. Configure error pages for SPA routing
4. Set up SSL certificate

## Database Setup

### MongoDB Atlas (Recommended)

#### Step 1: Create Cluster

1. Sign up at [MongoDB Atlas](https://www.mongodb.com/cloud/atlas)
2. Create a new cluster (M0 free tier for development)
3. Configure database user and password

#### Step 2: Configure Network Access

1. Add IP addresses:
   - Development: Your current IP
   - Production: Backend server IP or 0.0.0.0/0 (all IPs)

#### Step 3: Get Connection String

```env
MONGODB_URI=mongodb+srv://username:password@cluster.mongodb.net/orderSystemDB?retryWrites=true&w=majority
```

#### Step 4: Seed Data (Optional)

Create a seed script `scripts/seed.js`:
```javascript
const mongoose = require('mongoose');
const User = require('../models/User');

const seedData = async () => {
  try {
    await mongoose.connect(process.env.MONGODB_URI);
    
    // Create sample user
    const user = new User({
      shopName: 'Sample Shop',
      ownerName: 'John Doe',
      email: 'john@example.com',
      password: 'password123', // Will be hashed by pre-save hook
      phone: '123-456-7890',
      address: '123 Main St, City, State'
    });
    
    await user.save();
    console.log('Sample user created');
    process.exit(0);
  } catch (error) {
    console.error('Seeding failed:', error);
    process.exit(1);
  }
};

seedData();
```

### Self-Hosted MongoDB

#### Step 1: Server Setup

```bash
# Update system
sudo apt update && sudo apt upgrade -y

# Install MongoDB
wget -qO - https://www.mongodb.org/static/pgp/server-6.0.asc | sudo apt-key add -
echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/6.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-6.0.list

sudo apt-get update
sudo apt-get install -y mongodb-org

# Start and enable MongoDB
sudo systemctl start mongod
sudo systemctl enable mongod
```

#### Step 2: Configure Security

```bash
# Enable authentication
sudo mongo
> use admin
> db.createUser({
    user: "admin",
    pwd: "secure-password",
    roles: ["userAdminAnyDatabase", "dbAdminAnyDatabase"]
  })
> exit

# Edit MongoDB config
sudo nano /etc/mongod.conf
```

Add to config:
```yaml
security:
  authorization: enabled
```

```bash
# Restart MongoDB
sudo systemctl restart mongod
```

## SSL/HTTPS Configuration

### Automatic SSL with Hosting Providers

Most modern hosting providers (Vercel, Netlify, Railway, Render) provide automatic SSL certificates.

### Manual SSL Configuration

#### Step 1: Obtain SSL Certificate

Using Let's Encrypt:
```bash
# Install Certbot
sudo apt install certbot python3-certbot-nginx

# Obtain certificate
sudo certbot --nginx -d your-domain.com -d www.your-domain.com
```

#### Step 2: Configure Nginx

Create `/etc/nginx/sites-available/your-domain`:
```nginx
server {
    listen 80;
    server_name your-domain.com www.your-domain.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl;
    server_name your-domain.com www.your-domain.com;

    ssl_certificate /etc/letsencrypt/live/your-domain.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/your-domain.com/privkey.pem;

    location /api {
        proxy_pass http://localhost:5000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_cache_bypass $http_upgrade;
    }

    location / {
        root /var/www/html;
        try_files $uri $uri/ /index.html;
    }
}
```

## Monitoring and Logging

### Application Monitoring

#### Option 1: Sentry

1. Sign up at [Sentry](https://sentry.io)
2. Install Sentry SDK:
```bash
npm install @sentry/node @sentry/tracing
```

3. Configure in backend:
```javascript
const Sentry = require('@sentry/node');
const Tracing = require('@sentry/tracing');

Sentry.init({
  dsn: process.env.SENTRY_DSN,
  integrations: [
    new Sentry.Integrations.Http({ tracing: true }),
    new Tracing.Integrations.Express({ app }),
  ],
  tracesSampleRate: 1.0,
});

app.use(Sentry.Handlers.requestHandler());
app.use(Sentry.Handlers.tracingHandler());
app.use(Sentry.Handlers.errorHandler());
```

#### Option 2: LogRocket

Frontend error tracking:
```bash
npm install logrocket
```

```javascript
import LogRocket from 'logrocket';

LogRocket.init('your-app-id');
```

### Server Monitoring

#### PM2 Process Manager

```bash
# Install PM2
npm install -g pm2

# Create ecosystem file
pm2 ecosystem
```

`ecosystem.config.js`:
```javascript
module.exports = {
  apps: [{
    name: 'order-management-api',
    script: 'app.js',
    instances: 'max',
    exec_mode: 'cluster',
    env: {
      NODE_ENV: 'production',
      PORT: 5000
    },
    error_file: './logs/err.log',
    out_file: './logs/out.log',
    log_file: './logs/combined.log',
    time: true
  }]
};
```

```bash
# Start with PM2
pm2 start ecosystem.config.js

# Monitor
pm2 monit

# Save process list
pm2 save
pm2 startup
```

## Backup and Recovery

### Database Backup

#### Automated MongoDB Atlas Backups

MongoDB Atlas provides automated backups. Configure in the Atlas dashboard:

1. Go to your cluster
2. Click "Backups" tab
3. Configure backup schedule
4. Set retention period

#### Manual Backup Script

```bash
#!/bin/bash
# backup.sh

DATE=$(date +%Y%m%d_%H%M%S)
BACKUP_DIR="/backups/mongodb"
DB_NAME="orderSystemDB"

# Create backup directory
mkdir -p $BACKUP_DIR

# Create backup
mongodump --uri="$MONGODB_URI" --out="$BACKUP_DIR/backup_$DATE"

# Compress backup
tar -czf "$BACKUP_DIR/backup_$DATE.tar.gz" -C "$BACKUP_DIR" "backup_$DATE"

# Remove uncompressed backup
rm -rf "$BACKUP_DIR/backup_$DATE"

# Keep only last 7 days of backups
find $BACKUP_DIR -name "backup_*.tar.gz" -mtime +7 -delete

echo "Backup completed: backup_$DATE.tar.gz"
```

#### Cron Job for Automated Backups

```bash
# Edit crontab
crontab -e

# Add daily backup at 2 AM
0 2 * * * /path/to/backup.sh
```

### Recovery Procedures

#### Database Recovery

```bash
# Restore from backup
mongorestore --uri="$MONGODB_URI" /path/to/backup/backup_20231201_020000/orderSystemDB
```

#### Application Recovery

```bash
# Restart application
pm2 restart order-management-api

# Check logs
pm2 logs order-management-api

# Verify health
curl https://your-domain.com/api/health
```

## Troubleshooting

### Common Issues

#### 1. Database Connection Issues

**Problem**: Application can't connect to MongoDB

**Solutions**:
- Check MongoDB URI format
- Verify IP whitelisting (MongoDB Atlas)
- Check network connectivity
- Verify database credentials

```bash
# Test connection
mongo "mongodb+srv://username:password@cluster.mongodb.net/orderSystemDB"
```

#### 2. CORS Issues

**Problem**: Frontend can't access backend API

**Solutions**:
- Verify CORS configuration
- Check frontend URL in CORS whitelist
- Ensure HTTPS is used consistently

```javascript
// Backend CORS configuration
const cors = require('cors');
app.use(cors({
  origin: process.env.FRONTEND_URL,
  credentials: true
}));
```

#### 3. JWT Token Issues

**Problem**: Authentication failures

**Solutions**:
- Verify JWT_SECRET is set
- Check token expiration
- Ensure consistent token handling

```bash
# Decode JWT token
node -e "console.log(JSON.parse(Buffer.from('eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VySWQiOiI1MDdmMWY3N2JjZjg2Y2Q3OTk0MzkwMTEiLCJpYXQiOjE2NDA5OTUyMDAsImV4cCI6MTY0MzU4NzIwMH0', 'base64').toString('ascii'))"
```

#### 4. Build Failures

**Problem**: Frontend build fails

**Solutions**:
- Check Node.js version compatibility
- Verify environment variables
- Clear npm cache

```bash
# Clear npm cache
npm cache clean --force

# Delete node_modules and package-lock.json
rm -rf node_modules package-lock.json

# Reinstall dependencies
npm install
```

#### 5. Memory Issues

**Problem**: Application crashes due to memory limits

**Solutions**:
- Increase Node.js memory limit
- Optimize database queries
- Implement pagination

```bash
# Increase memory limit
node --max-old-space-size=4096 app.js
```

### Health Check Endpoint

Add health check to backend:

```javascript
// Health check endpoint
app.get('/api/health', (req, res) => {
  res.json({
    status: 'OK',
    timestamp: new Date().toISOString(),
    uptime: process.uptime(),
    memory: process.memoryUsage(),
    version: process.env.npm_package_version || '1.0.0'
  });
});
```

### Performance Monitoring

Add performance monitoring middleware:

```javascript
// Performance monitoring
app.use((req, res, next) => {
  const start = Date.now();
  
  res.on('finish', () => {
    const duration = Date.now() - start;
    console.log(`${req.method} ${req.path} - ${res.statusCode} - ${duration}ms`);
  });
  
  next();
});
```

This deployment guide provides comprehensive instructions for deploying the MERN Order Management System to production environments with proper security, monitoring, and maintenance procedures.
