# 🅿️ ParkVille

> A Smart City Parking Management System

ParkVille is a full-stack smart parking platform that enables drivers to find, reserve, and manage parking spots in real time. It features a **Java Spring Boot** REST backend, a **JavaScript** frontend, and a **Python** simulation/detection module — all backed by a **MySQL** relational database.

---

## 🛠️ Tech Stack

| Layer      | Technology                          |
|------------|-------------------------------------|
| Backend    | Java 23, Spring Boot, Maven         |
| Frontend   | JavaScript, CSS, HTML (Vite/npm)    |
| Database   | MySQL                               |
| Simulation | Python                              |

---

## 📁 Project Structure

```
ParkVille/
├── backend/        # Spring Boot REST API
├── frontend/       # JS frontend (Vite)
├── .gitignore
├── LICENSE
└── README.md
```

---

## ⚙️ Setup

### Prerequisites

Make sure you have the following installed:

- **Java 23**
- **MySQL**
- **Node.js & npm**
- **Maven**
- **Python 3** (for simulation module)

Verify your installations:

```bash
java --version
mysql --version
node --version
npm --version
python3 --version
```

---

### 1. Database Setup

Open your MySQL shell and run:

```sql
CREATE DATABASE parkdb;

CREATE USER 'park_admin'@'localhost' IDENTIFIED BY 'Admin@123';

GRANT ALL PRIVILEGES ON parkdb.* TO 'park_admin'@'localhost';

FLUSH PRIVILEGES;
```

> Spring Boot will automatically create and manage all tables on first startup.

---

### 2. Backend Setup

```bash
cd backend
mvn clean install
mvn spring-boot:run
```

The backend API will start at `http://localhost:8080`.

---

### 3. Frontend Setup

```bash
cd frontend
npm install
npm run dev
```

The frontend dev server will start at `http://localhost:5173`.

---

## 📡 API Documentation

All endpoints are prefixed with `/api`. The backend runs on `http://localhost:8080` by default.

---

### 🔐 Authentication

| Method | Endpoint              | Description              | Auth Required |
|--------|-----------------------|--------------------------|---------------|
| POST   | `/api/auth/register`  | Register a new user      | No            |
| POST   | `/api/auth/login`     | Login and get a token    | No            |
| POST   | `/api/auth/logout`    | Logout current session   | Yes           |

**Register — Request Body:**
```json
{
  "name": "John Doe",
  "email": "john@example.com",
  "password": "SecurePass@123",
  "role": "DRIVER"
}
```

**Login — Request Body:**
```json
{
  "email": "john@example.com",
  "password": "SecurePass@123"
}
```

**Login — Response:**
```json
{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "userId": 1,
  "role": "DRIVER"
}
```

---

### 🅿️ Parking Lots

| Method | Endpoint                      | Description                      | Auth Required |
|--------|-------------------------------|----------------------------------|---------------|
| GET    | `/api/lots`                   | Get all parking lots             | No            |
| GET    | `/api/lots/{id}`              | Get a specific parking lot       | No            |
| GET    | `/api/lots/{id}/availability` | Get real-time spot availability  | No            |
| POST   | `/api/lots`                   | Create a new parking lot         | Yes (Admin)   |
| PUT    | `/api/lots/{id}`              | Update parking lot details       | Yes (Admin)   |
| DELETE | `/api/lots/{id}`              | Delete a parking lot             | Yes (Admin)   |

**Parking Lot Object:**
```json
{
  "id": 1,
  "name": "Downtown Garage A",
  "location": "123 Main St",
  "totalSpots": 100,
  "availableSpots": 42,
  "pricePerHour": 5.00
}
```

---

### 🔲 Parking Spots

| Method | Endpoint                           | Description              | Auth Required |
|--------|------------------------------------|--------------------------|---------------|
| GET    | `/api/lots/{lotId}/spots`          | Get all spots in a lot   | No            |
| GET    | `/api/lots/{lotId}/spots/{spotId}` | Get a specific spot      | No            |
| PATCH  | `/api/lots/{lotId}/spots/{spotId}` | Update spot status       | Yes (Admin)   |

**Spot Object:**
```json
{
  "id": 5,
  "lotId": 1,
  "spotNumber": "A-05",
  "type": "STANDARD",
  "status": "AVAILABLE"
}
```

> `status` can be: `AVAILABLE`, `OCCUPIED`, `RESERVED`, `DISABLED`  
> `type` can be: `STANDARD`, `DISABLED`, `EV_CHARGING`, `MOTORCYCLE`

---

### 📅 Reservations

| Method | Endpoint                         | Description                        | Auth Required |
|--------|----------------------------------|------------------------------------|---------------|
| GET    | `/api/reservations`              | Get all reservations (Admin)       | Yes (Admin)   |
| GET    | `/api/reservations/{id}`         | Get a specific reservation         | Yes           |
| GET    | `/api/reservations/user/{userId}`| Get all reservations for a user    | Yes           |
| POST   | `/api/reservations`              | Create a new reservation           | Yes           |
| DELETE | `/api/reservations/{id}`         | Cancel a reservation               | Yes           |

**Create Reservation — Request Body:**
```json
{
  "userId": 1,
  "spotId": 5,
  "startTime": "2025-06-01T10:00:00",
  "endTime": "2025-06-01T12:00:00"
}
```

**Reservation Response:**
```json
{
  "id": 101,
  "userId": 1,
  "spotId": 5,
  "lotName": "Downtown Garage A",
  "spotNumber": "A-05",
  "startTime": "2025-06-01T10:00:00",
  "endTime": "2025-06-01T12:00:00",
  "status": "CONFIRMED",
  "totalCost": 10.00
}
```

---

### 🚗 Vehicles

| Method | Endpoint                      | Description               | Auth Required |
|--------|-------------------------------|---------------------------|---------------|
| GET    | `/api/vehicles/user/{userId}` | Get vehicles for a user   | Yes           |
| POST   | `/api/vehicles`               | Register a new vehicle    | Yes           |
| DELETE | `/api/vehicles/{id}`          | Remove a vehicle          | Yes           |

**Vehicle Object:**
```json
{
  "id": 3,
  "userId": 1,
  "plateNumber": "ABC-1234",
  "type": "STANDARD",
  "model": "Toyota Camry"
}
```

---

### 💳 Payments

| Method | Endpoint                      | Description                    | Auth Required |
|--------|-------------------------------|--------------------------------|---------------|
| GET    | `/api/payments/user/{userId}` | Get payment history for a user | Yes           |
| POST   | `/api/payments`               | Process a payment              | Yes           |
| GET    | `/api/payments/{id}`          | Get payment details            | Yes           |

**Payment Request Body:**
```json
{
  "reservationId": 101,
  "userId": 1,
  "amount": 10.00,
  "method": "CARD"
}
```

**Payment Response:**
```json
{
  "id": 55,
  "reservationId": 101,
  "amount": 10.00,
  "status": "SUCCESS",
  "timestamp": "2025-06-01T09:55:00"
}
```

---

### 👤 Users (Admin)

| Method | Endpoint          | Description           | Auth Required |
|--------|-------------------|-----------------------|---------------|
| GET    | `/api/users`      | Get all users         | Yes (Admin)   |
| GET    | `/api/users/{id}` | Get a specific user   | Yes (Admin)   |
| PUT    | `/api/users/{id}` | Update user details   | Yes           |
| DELETE | `/api/users/{id}` | Delete a user         | Yes (Admin)   |

---

### ❗ Error Responses

All errors follow this structure:

```json
{
  "status": 404,
  "error": "Not Found",
  "message": "Parking lot with ID 99 not found",
  "timestamp": "2025-06-01T10:00:00"
}
```

| Status Code | Meaning               |
|-------------|-----------------------|
| 200         | OK                    |
| 201         | Created               |
| 400         | Bad Request           |
| 401         | Unauthorized          |
| 403         | Forbidden             |
| 404         | Not Found             |
| 500         | Internal Server Error |

---

## 📝 Important Notes

- Spring Boot auto-creates all database tables on startup.
- If you make a structural change to an entity, drop the table and restart:

  ```sql
  DROP TABLE IF EXISTS <table_name>;
  ```

- To inspect the database:

  ```sql
  SHOW TABLES;
  DESCRIBE <table_name>;
  ```

---

## 📄 License

This project is licensed under the [MIT License](./LICENSE).
