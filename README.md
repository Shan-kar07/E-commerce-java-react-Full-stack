# E-commerce-java-react-Full-stack
# E-Commerce Application

This project is an E-Commerce application built with a Spring Boot backend using a microservices architecture and a React frontend. The backend is composed of four distinct services:
- **Order Service**
- **Payment Service**
- **Inventory Service**
- **User Service**

The frontend provides a colorful, simple, and responsive UI for user authentication, product browsing, cart management, and payment processing.

---

## Table of Contents
- [Overview](#overview)
- [Architecture & Flow](#architecture--flow)
- [API Endpoints](#api-endpoints)
  - [Order Service](#order-service)
  - [Payment Service](#payment-service)
  - [Inventory Service](#inventory-service)
  - [User Service](#user-service)
- [Frontend Details](#frontend-details)
- [Installation](#installation)
- [Technologies Used](#technologies-used)
- [Contributing](#contributing)
- [License](#license)

---

## Overview

This E-commerce Application enables:
- **User Management:** New users can register, and existing users can log in and be authenticated (with token generation).
- **Order Processing:** Users can add orders (which act as items in their cart) and view all orders by user ID. Orders include details such as product, quantity, and price.
- **Payment Handling:** When an order is created, a matching payment record is inserted with a "PENDING" status. Users can then finalize the payment (updating the status to `COMPLETED` or `FAILED`).
- **Inventory Management:** Available products can be searched, added, and updated (both increasing and decreasing stock) via the Inventory Service.

---

## Architecture & Flow

1. **User Places an Order:**  
   - When a user adds an order through  
     `POST localhost:8080/orders/addorders`  
     with a request body like:
     ```json
     {
       "userId": "2",
       "orderId": "2",
       "productId": "7",
       "quantity": "5",
       "price": "20"
     }
     ```
   - The Order Service registers the order, and (internally) a pending payment is added in the Payment Service.

2. **Payment Processing:**  
   - The total payable amount (calculated by summing pending orders for that order ID) can be retrieved through the endpoint:  
     `POST localhost:8080/orders/makePayment/2`  
   - To complete the payment, the payment status is updated via:  
     `PUT localhost:8080/payments/2/status`  
     with a body of `"COMPLETED"` (or `"FAILED"` if the payment did not go through).

3. **Inventory Update:**  
   - Upon a completed payment, the Inventory Service may update the stock (decrement the product quantity) to reflect the purchased items.
   - If payment fails, the product stock remains unchanged.

4. **Frontend Interaction:**  
   - The user logs in (role-based) and browses products.
   - Each product displays a quantity selector (using “+” and “-” buttons) and an “Add to Cart” button.
   - The cart page retrieves orders for the user using  
     `GET localhost:8080/orders/byuserId/2`.
   - At checkout, the user either confirms payment (using correct inputs: `COD` or `CARD`) or cancels, updating the payment status accordingly.

---

## API Endpoints

### Order Service
- **Get All Orders:**  
  `GET localhost:8080/orders/allorders`

- **Get Orders by User ID:**  
  `GET localhost:8080/orders/byuserId/2`  
  *(Replace `2` with the desired user ID.)*

- **Add Order:**  
  `POST localhost:8080/orders/addorders`  
  **Request Body:**
  ```json
  {
    "userId": "2",
    "orderId": "2",
    "productId": "7",
    "quantity": "5",
    "price": "20"
  }
