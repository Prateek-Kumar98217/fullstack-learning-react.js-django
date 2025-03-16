# React-Django E-commerce Platform

This project demonstrates an e-commerce platform built with a React frontend and Django REST Framework backend.

## Features

- Product catalog with categories and filters
- Shopping cart functionality
- User authentication and profiles
- Order management system
- Payment processing integration
- Admin dashboard for inventory management
- Product reviews and ratings

## Project Structure

```
ecommerce/
├── frontend/              # React frontend
│   ├── public/            # Static files
│   └── src/               # React source code
│       ├── components/    # Reusable components
│       ├── pages/         # Page components
│       ├── context/       # React context API
│       ├── redux/         # Redux state management
│       ├── services/      # API services
│       └── utils/         # Utility functions
└── backend/               # Django backend
    ├── ecommerce/         # Django project
    ├── products/          # Products app
    ├── orders/            # Orders app
    ├── accounts/          # User accounts app
    ├── payments/          # Payment processing app
    └── api/               # API app
```

## Technology Stack

### Frontend
- React
- Redux or Context API for state management
- React Router
- Axios
- Styled Components or Material-UI

### Backend
- Django
- Django REST Framework
- Simple JWT for authentication
- Stripe for payment processing
- PostgreSQL (recommended for production)

## Setup Instructions

### Backend Setup
1. Navigate to the backend directory:
   ```
   cd backend
   ```

2. Create a virtual environment:
   ```
   python -m venv venv
   source venv/bin/activate  # On Windows: venv\Scripts\activate
   ```

3. Install dependencies:
   ```
   pip install -r requirements.txt
   ```

4. Set up environment variables:
   - Create a `.env` file with necessary configurations
   - Include database settings, secret key, and Stripe API keys

5. Run migrations:
   ```
   python manage.py migrate
   ```

6. Create a superuser:
   ```
   python manage.py createsuperuser
   ```

7. Load sample data (optional):
   ```
   python manage.py loaddata sample_products
   ```

8. Start the development server:
   ```
   python manage.py runserver
   ```

### Frontend Setup
1. Navigate to the frontend directory:
   ```
   cd frontend
   ```

2. Install dependencies:
   ```
   npm install
   ```

3. Set up environment variables:
   - Create a `.env` file with API URL and Stripe public key

4. Start the development server:
   ```
   npm start
   ```

5. Access the application at `http://localhost:3000`

## API Endpoints

### Products
- `GET /api/products/`: List all products
- `GET /api/products/{id}/`: Get a specific product
- `GET /api/categories/`: List all categories
- `GET /api/products/search/?query=term`: Search products

### Cart and Orders
- `GET /api/cart/`: Get the current user's cart
- `POST /api/cart/add/`: Add item to cart
- `DELETE /api/cart/items/{id}/`: Remove item from cart
- `POST /api/orders/`: Create a new order
- `GET /api/orders/`: List user's orders
- `GET /api/orders/{id}/`: Get specific order details

### Authentication
- `POST /api/token/`: Get authentication token
- `POST /api/token/refresh/`: Refresh authentication token
- `POST /api/users/register/`: Register a new user
- `GET /api/users/profile/`: Get user profile
- `PUT /api/users/profile/`: Update user profile

## Learning Objectives

- Building complex data models with relationships
- Managing shopping cart state
- Implementing secure payment processing
- Handling user authentication and authorization
- Building reusable React components
- Managing complex application state

## Next Steps

After completing this project, you can extend it with:
- Inventory management
- Discount codes and promotions
- Wishlist functionality
- Product comparisons
- Advanced search with filters
- Email notifications
- Analytics dashboard 