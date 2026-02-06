# MERN Order Management System

A full-stack web application built with MongoDB, Express.js, React, and Node.js for managing customer orders and purchases.

## Features

- **Order Form**: Create new orders with customer details (name, phone, address, product)
- **Real-time Data**: Orders are saved to MongoDB with exact timestamp
- **Purchased Products Page**: View all orders as bills with complete details
- **Search Functionality**: Search orders by customer name, phone, product, or address
- **Responsive Design**: Beautiful gradient UI that works on all devices
- **Delete Orders**: Remove orders from the database

## Project Structure

```
mern-order-system/
├── backend/
│   ├── models/
│   │   └── Order.js
│   ├── .env
│   ├── package.json
│   └── server.js
└── frontend/
    ├── public/
    │   └── index.html
    ├── src/
    │   ├── components/
    │   │   ├── OrderForm.js
    │   │   └── PurchasedProducts.js
    │   ├── App.js
    │   ├── App.css
    │   ├── index.js
    │   └── index.css
    └── package.json
```

## Prerequisites

Before running this application, make sure you have the following installed:

- Node.js (v14 or higher)
- MongoDB (v4.4 or higher)
- npm or yarn

## Installation

### 1. Clone or Download the Project

Navigate to the project directory:
```bash
cd mern-order-system
```

### 2. Install Backend Dependencies

```bash
cd backend
npm install
```

### 3. Install Frontend Dependencies

```bash
cd ../frontend
npm install
```

## MongoDB Setup

### Option 1: Local MongoDB

1. Install MongoDB on your system: https://www.mongodb.com/try/download/community

2. Start MongoDB service:
   - **Windows**: MongoDB should start automatically as a service
   - **macOS**: `brew services start mongodb-community`
   - **Linux**: `sudo systemctl start mongod`

3. Verify MongoDB is running:
```bash
mongosh
```

The backend will automatically create a database called `orderSystemDB`.

### Option 2: MongoDB Atlas (Cloud)

1. Create a free account at https://www.mongodb.com/cloud/atlas

2. Create a new cluster

3. Get your connection string

4. Update the `.env` file in the backend folder:
```
MONGODB_URI=mongodb+srv://username:password@cluster.mongodb.net/orderSystemDB?retryWrites=true&w=majority
```

## Running the Application

### 1. Start the Backend Server

Open a terminal and navigate to the backend folder:

```bash
cd backend
npm start
```

The server will start on `http://localhost:5000`

You should see:
```
Server is running on port 5000
MongoDB connected successfully
```

### 2. Start the Frontend Development Server

Open a **new terminal** and navigate to the frontend folder:

```bash
cd frontend
npm start
```

The React app will start on `http://localhost:3000` and automatically open in your browser.

## Using the Application

### Creating an Order

1. Navigate to the "New Order" page (home page)
2. Fill in the form:
   - Customer Name
   - Customer Phone Number
   - Customer Address
   - Product
3. Click the "Add Order" button
4. You'll see a success message and the form will reset

### Viewing Orders

1. Click on "Purchased Products" in the navigation
2. You'll see all orders displayed as cards
3. Each card shows:
   - Product name
   - Order date and time
   - Customer name
   - Phone number
   - Address
   - Delete button

### Searching Orders

1. On the "Purchased Products" page
2. Use the search bar at the top
3. Type any part of:
   - Customer name
   - Phone number
   - Product name
   - Address
4. Results will filter in real-time

### Deleting Orders

1. Click the "Delete Order" button on any order card
2. Confirm the deletion
3. The order will be removed from the database

## API Endpoints

### Create Order
- **POST** `/api/orders`
- Body:
```json
{
  "customerName": "John Doe",
  "customerPhone": "123-456-7890",
  "customerAddress": "123 Main St, City, State",
  "product": "Laptop"
}
```

### Get All Orders
- **GET** `/api/orders`

### Search Orders
- **GET** `/api/orders/search?query=searchterm`

### Delete Order
- **DELETE** `/api/orders/:id`

## Technologies Used

### Backend
- **Node.js**: JavaScript runtime
- **Express.js**: Web application framework
- **MongoDB**: NoSQL database
- **Mongoose**: MongoDB object modeling
- **CORS**: Cross-origin resource sharing
- **dotenv**: Environment variable management

### Frontend
- **React**: UI library
- **React Router DOM**: Client-side routing
- **Axios**: HTTP client
- **CSS3**: Styling with gradients and animations

## Features Breakdown

### Order Form Component
- Input validation
- Real-time form state management
- Success/error messaging
- Automatic form reset after submission

### Purchased Products Component
- Real-time data fetching
- Live search filtering
- Date/time formatting
- Responsive grid layout
- Delete functionality with confirmation

## Customization

### Change Colors

Edit `frontend/src/App.css`:

```css
/* Main gradient */
background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);

/* Accent color */
color: #667eea;
```

### Change Port

Backend - Edit `backend/.env`:
```
PORT=5000
```

Frontend - Create `frontend/.env`:
```
PORT=3000
```

### Database Name

Edit `backend/.env`:
```
MONGODB_URI=mongodb://localhost:27017/yourDatabaseName
```

## Troubleshooting

### MongoDB Connection Error

**Problem**: `MongoDB connection error`

**Solution**:
- Ensure MongoDB is running
- Check connection string in `.env`
- Verify network access if using MongoDB Atlas

### CORS Error

**Problem**: `Access to XMLHttpRequest has been blocked by CORS policy`

**Solution**:
- Ensure backend is running on port 5000
- Check CORS configuration in `server.js`

### Port Already in Use

**Problem**: `Port 5000 is already in use`

**Solution**:
- Change port in `backend/.env`
- Or kill the process using the port:
  - Windows: `netstat -ano | findstr :5000` then `taskkill /PID <PID> /F`
  - macOS/Linux: `lsof -ti:5000 | xargs kill`

### npm install errors

**Problem**: Package installation fails

**Solution**:
- Clear npm cache: `npm cache clean --force`
- Delete `node_modules` and `package-lock.json`
- Run `npm install` again

## Production Deployment

### Backend Deployment (Heroku/Railway/Render)

1. Add to `backend/package.json`:
```json
"engines": {
  "node": "18.x"
}
```

2. Create Procfile:
```
web: node server.js
```

3. Set environment variables on your hosting platform

### Frontend Deployment (Vercel/Netlify)

1. Build the app:
```bash
npm run build
```

2. Update API URL in axios calls to your backend URL

3. Deploy the `build` folder

## Future Enhancements

- User authentication
- Order status tracking
- Invoice generation (PDF)
- Email notifications
- Payment integration
- Advanced filtering and sorting
- Order editing capability
- Dashboard with analytics

## License

MIT License - feel free to use this project for learning or commercial purposes.

## Support

For issues or questions, please create an issue in the project repository.
