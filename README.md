# Stage 1 – Backend API

This repository contains **Stage 1** of the assessment: a backend authentication API built with **Node.js**, **Express**, and **MySQL**.

The API provides the following endpoints:

- **POST /api/register** — Accepts `{ username, email, password, address }` in the request body.
- **POST /api/login** — Accepts `{ email, password }` and returns a signed JWT on success.
- **GET /api/users/:id** — A protected route that returns the user record (excluding the password) when a valid JWT is provided.

## Important

**Stage 1 is fully self-contained.** You can completely test and verify that the API is working as intended using **Postman**. A frontend is **not required**.

The [Stage 2 React Frontend](https://github.com/SimpliMinimalist/Stage-2-React-Front-End) repository is provided only as an optional reference to show how this backend API is integrated with a React frontend.

## Getting Started

Clone this repository to your local machine:

```bash
git clone https://github.com/SimpliMinimalist/Stage-1-Backend-API-with-Node.js.git
cd Stage-1-Backend-API-with-Node.js
```

Then follow the steps below to install the required dependencies and configure the environment.

## 1. System Requirements & Installation Guidelines

### Node.js Setup
Node.js (LTS version 18+) is required to run the backend server. 

**Installation & Verification:**
1. **Step 1:** Check if Node is already installed by running `node -v` in your terminal.
2. **Step 2:** If it is not installed, download the **LTS** installer from [https://nodejs.org/](https://nodejs.org/), install it with default settings, and restart your terminal.
3. **Step 3:** Run `node -v` again to verify successful installation.

### MySQL Setup
MySQL is the relational database used to store user accounts.

1. **Step 1:** Download the **Full MSI Installer** from [https://dev.mysql.com/downloads/installer/](https://dev.mysql.com/downloads/installer/).
2. **Step 2:** Run the installer and select the **Full** Setup Type.
3. **Step 3:** Keep everything default and continue until you reach the Accounts and Roles configuration.
4. **Step 4:** Set your MySQL Root Password (e.g., `123456`). Add this to your `.env` file as `DB_PASSWORD=123456`.
5. **Step 5:** Open the MySQL Workbench dashboard. In the bottom left corner, click **Local instance MySQL**.
6. **Step 6:** Create a new query tab and paste the following code:
   ```sql
   CREATE DATABASE IF NOT EXISTS express_auth_db
     CHARACTER SET utf8mb4
     COLLATE utf8mb4_unicode_ci;
   
   USE express_auth_db;
   
   CREATE TABLE IF NOT EXISTS users (
     id          INT             AUTO_INCREMENT PRIMARY KEY,
     username    VARCHAR(50)     NOT NULL UNIQUE,
     email       VARCHAR(100)    NOT NULL UNIQUE,
     password    VARCHAR(255)    NOT NULL,
     address     VARCHAR(255)    NOT NULL,
     created_at  TIMESTAMP       DEFAULT CURRENT_TIMESTAMP,
     updated_at  TIMESTAMP       DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
     INDEX idx_users_email (email),
     INDEX idx_users_username (username)
   ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
   ```
7. **Step 7:** Click the **Run** button (the lightning bolt symbol ⚡) to execute the query.

## 2. Project Setup

### Install Dependencies

Navigate to the project directory and install the required Node modules:

```bash
npm install
```

### Configure Environment

> **Note:** A pre-configured `.env` file has been directly included in this repository to simplify the setup process. **In a real production environment, you should NEVER commit or push your `.env` file to source control.**

**Only one value needs to be changed:** Open the `.env` file and set `DB_PASSWORD` to the MySQL root password you created during Step 4 of the MySQL setup above. All other values can be left as their defaults.

```env
DB_PASSWORD=your_mysql_root_password
```


## Running the Server

Start the API server in development mode:

```bash
npm run dev
```

**Expected Console Output:**
```text
MySQL connected successfully
Server running on port 3000
```

## API Documentation and Verification

To verify the API functionality, we will use **Postman**, a popular API testing tool.

### Postman Setup

1. **Step 1:** Download Postman from [https://www.postman.com/downloads/](https://www.postman.com/downloads/).
2. **Step 2:** Run the installer and open the application. You can create a free account or select "Skip and go to app" at the bottom.
3. **Step 3:** Click the **"+" (New Tab)** button near the top left to create a new HTTP request.
4. **Step 4:** Follow the specific setup instructions for each endpoint below.

### 1. Health Check

**Endpoint:** `GET /health`

**Postman Setup:**
- **Method:** GET
- **URL:** `http://localhost:3000/health`

**Expected Response:**
```json
{
  "success": true,
  "message": "Server is running",
  "timestamp": "2026-07-11T20:00:00.000Z"
}
```

### 2. User Registration

**Endpoint:** `POST /api/register`

**Postman Setup:**
- **Method:** POST
- **URL:** `http://localhost:3000/api/register`
- **Body:** Select `raw` and `JSON`, then paste:
  ```json
  {
    "username": "demouser",
    "email": "demo@example.com",
    "password": "Password123!",
    "address": "123 Tech Ave"
  }
  ```

**Expected Response (201 Created):**
```json
{
  "success": true,
  "message": "User registered successfully",
  "data": {
    "user": {
      "id": 1,
      "username": "demouser",
      "email": "demo@example.com",
      "address": "123 Tech Ave",
      "created_at": "...",
      "updated_at": "..."
    }
  }
}
```

### 3. User Authentication

**Endpoint:** `POST /api/login`

**Postman Setup:**
- **Method:** POST
- **URL:** `http://localhost:3000/api/login`
- **Body:** Select `raw` and `JSON`, then paste:
  ```json
  {
    "email": "demo@example.com",
    "password": "Password123!"
  }
  ```

**Expected Response (200 OK):**
```json
{
  "success": true,
  "message": "Login successful",
  "data": {
    "user": {
      "id": 1,
      "username": "demouser",
      "email": "demo@example.com",
      "address": "123 Tech Ave",
      "created_at": "...",
      "updated_at": "..."
    },
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
  }
}
```

### 4. Access Protected Routes

**Endpoint:** `GET /api/users/:id`
**Requirement:** Requires `Authorization` header with a valid Bearer token.

**Postman Setup:**
- **Method:** GET
- **URL:** `http://localhost:3000/api/users/1`
- **Authorization:** Select `Bearer Token` and paste the token received from the Login response.

**Expected Response (200 OK):**
```json
{
  "success": true,
  "message": "User retrieved successfully",
  "data": {
    "user": {
      "id": 1,
      "username": "demouser",
      "email": "demo@example.com",
      "address": "123 Tech Ave",
      "created_at": "...",
      "updated_at": "..."
    }
  }
}
```

**Unauthorized Response (401 Unauthorized):**
```json
{
  "success": false,
  "status": "fail",
  "message": "Access denied. No token provided"
}
```

---

## Verification Complete

If all four endpoints return the expected responses as shown above — the health check returns a `200 OK`, registration creates a new user, login returns a signed JWT, and the protected route returns the user record when a valid token is provided — then **the backend API is fully functional and Stage 1 is complete**.

You can now proceed to the [Stage 2 React Frontend](https://github.com/SimpliMinimalist/Stage-2-React-Front-End) to see how this API is consumed by a React application.
