# Airbnb Clone Backend – Requirements Specifications

## Overview
This document outlines the technical and functional requirements for three key backend features of the Airbnb Clone: **User Authentication**, **Property Management**, and **Booking System**. Each section includes API endpoints, input/output specifications, validation rules, and performance criteria.

---

## 1. User Authentication

**Objective:** Securely register, login, and manage users (Guests, Hosts, Admins).

### API Endpoints
- **POST /users/register/** – Register a new user
- **POST /users/login/** – Authenticate user and generate JWT
- **GET /users/{user_id}/** – Retrieve user profile
- **PUT /users/{user_id}/** – Update user profile
- **DELETE /users/{user_id}/** – Delete user account

### Input / Output Specifications
- **Register**
  - Input: `{ "name": "string", "email": "string", "password": "string", "role": "guest|host" }`
  - Output: `{ "user_id": "uuid", "name": "string", "email": "string", "role": "string", "token": "JWT" }`
- **Login**
  - Input: `{ "email": "string", "password": "string" }`
  - Output: `{ "user_id": "uuid", "token": "JWT" }`

### Validation Rules
- Email must be unique and valid format.
- Password minimum length: 8 characters, must include letters and numbers.
- Role must be either `guest` or `host`.

### Performance Criteria
- Response time < 200ms for login/register.
- JWT token validity: 1 hour (configurable).
- Handle 1000 concurrent login requests.

---

## 2. Property Management

**Objective:** Allow hosts to create, update, retrieve, and delete property listings.

### API Endpoints
- **POST /properties/** – Add a new property
- **GET /properties/** – List all properties
- **GET /properties/{property_id}/** – Retrieve a specific property
- **PUT /properties/{property_id}/** – Update property details
- **DELETE /properties/{property_id}/** – Delete a property

### Input / Output Specifications
- **Add Property**
  - Input: 
    ```json
    {
      "title": "string",
      "description": "string",
      "location": "string",
      "price_per_night": "number",
      "amenities": ["WiFi", "Pool"],
      "available_dates": ["YYYY-MM-DD"]
    }
    ```
  - Output: `{ "property_id": "uuid", "title": "string", "status": "created" }`
- **Retrieve Property**
  - Output: Full property details including host info and availability.

### Validation Rules
- Price must be a positive number.
- Dates must follow ISO format (YYYY-MM-DD).
- Mandatory fields: title, location, price, host_id.

### Performance Criteria
- Retrieve property list within 300ms for 1000+ entries.
- Support up to 500 concurrent property creation requests.
- Image uploads ≤ 5MB per file.

---

## 3. Booking System

**Objective:** Enable guests to book properties and manage bookings.

### API Endpoints
- **POST /bookings/** – Create a new booking
- **GET /bookings/** – List all bookings
- **GET /bookings/{booking_id}/** – Retrieve a specific booking
- **PUT /bookings/{booking_id}/** – Update booking (change dates, status)
- **DELETE /bookings/{booking_id}/** – Cancel booking

### Input / Output Specifications
- **Create Booking**
  - Input:
    ```json
    {
      "user_id": "uuid",
      "property_id": "uuid",
      "check_in": "YYYY-MM-DD",
      "check_out": "YYYY-MM-DD",
      "guests": 2
    }
    ```
  - Output: `{ "booking_id": "uuid", "status": "confirmed", "total_amount": 200.00 }`

### Validation Rules
- Check-in date < check-out date.
- Booking dates must not overlap existing confirmed bookings.
- Guests cannot book their own property.

### Performance Criteria
- Booking confirmation < 500ms.
- Handle 200 simultaneous booking requests.
- Prevent double booking for same property/date.

---

## Notes
- All endpoints require JWT authentication except `/users/register/` and `/users/login/`.
- Errors should return standardized format:
  ```json
  { "error_code": "string", "message": "string" }
