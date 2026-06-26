# 🚌 Smart Bus Ticket Booking System

A web-based bus ticket booking platform built with Node.js, Express, and MySQL. The system allows passengers to search for bus routes, book tickets, and receive QR-coded tickets — while conductors can scan and validate those tickets on board.

---

## Features

**Passenger Side**
- User registration and login (session-based authentication)
- Search buses by source and destination with autocomplete suggestions
- View available bus numbers, routes, and distances
- Book tickets and proceed to payment
- Receive a QR-coded ticket upon booking
- View profile and booking history
- Password recovery (forgot password flow)

**Conductor Side**
- Separate conductor login portal
- Conductor dashboard to manage and monitor trips
- QR code scanner to validate passenger tickets in real time
- Map view showing bus route from origin to destination (via Leaflet.js)

---

## Tech Stack

| Layer       | Technology                          |
|-------------|-------------------------------------|
| Backend     | Node.js, Express.js                 |
| Database    | MySQL                               |
| Frontend    | HTML, CSS, Vanilla JavaScript       |
| Maps        | Leaflet.js                          |
| QR Codes    | `qrcode` npm package                |
| Sessions    | `express-session`                   |
| HTTP Client | `axios`                             |
| Excel Data  | `xlsx` (SheetJS)                    |

---

## Project Structure

```
smart-bus-ticket-booking-system-main/
│
├── index.js                     # Main Express server (entry point)
├── index1.js                    # Utility script: import areas lat/lon from Excel to DB
├── index3.js                    # Alternate/dev server file
│
├── public/
│   ├── views/                   # HTML pages
│   │   ├── index1.html          # Home / landing page
│   │   ├── login.html           # Passenger login
│   │   ├── signup.html          # Passenger registration
│   │   ├── signupsuccess.html   # Registration success
│   │   ├── loginsuccess.html    # Login success / dashboard
│   │   ├── loginbookbus.html    # Bus search and booking
│   │   ├── loginbookbus1.html   # Booking step 2
│   │   ├── loginbookbus3.html   # Booking step 3
│   │   ├── paymentdetails.html  # Ticket & QR code display
│   │   ├── profile.html         # User profile
│   │   ├── forgot.html          # Forgot password
│   │   ├── conductor.html       # Conductor login
│   │   ├── conductor_dashboard.html  # Conductor dashboard
│   │   ├── validateQRCode.html  # QR code scanner for conductors
│   │   ├── service.html         # Services page
│   │   └── contact.html         # Contact page
│   │
│   ├── js/
│   │   ├── db.js                # MySQL database query functions
│   │   ├── findbus.js           # Frontend bus search logic
│   │   ├── index1.js            # Frontend scripts
│   │   ├── profile.js           # Profile page scripts
│   │   ├── readExcel.js         # Excel reading utilities
│   │   ├── readExcel1.js        # Excel reading utilities (alt)
│   │   ├── signupscript.js      # Signup form scripts
│   │   └── utils.js             # Shared utility functions
│   │
│   ├── css/                     # Stylesheets per page
│   ├── images/                  # Static image assets
│   └── demos/                   # Demo/prototype files
│
└── bus info/                    # Source data (Excel files)
    ├── bus route details.xlsx   # Bus routes with from/to/distance
    ├── areas latlon.xlsx        # Area coordinates (lat/lon)
    ├── timings.xlsm             # Bus timing data
    ├── timings_detail.xlsm      # Detailed timing schedules
    └── list.xlsm                # Bus listing data
```

---

## Database Schema

The application uses a MySQL database named `bussystem` with the following tables:

**`passengers`**
| Column       | Type    |
|--------------|---------|
| id           | INT (PK)|
| fullname     | VARCHAR |
| email        | VARCHAR |
| mobilenumber | VARCHAR |
| password     | VARCHAR |

**`conductors`**
| Column       | Type    |
|--------------|---------|
| id           | VARCHAR |
| name         | VARCHAR |
| mobilenumber | VARCHAR |
| password     | VARCHAR |

**`bus_details`**
| Column        | Type    |
|---------------|---------|
| bus_numbers   | VARCHAR |
| from_location | VARCHAR |
| destination   | VARCHAR |
| distance      | VARCHAR |

**`areaslatlon`**
| Column    | Type    |
|-----------|---------|
| area      | VARCHAR |
| latitude  | VARCHAR |
| longitude | VARCHAR |

> ⚠️ **Note:** Passwords are stored in plain text. For production use, implement hashing (e.g., bcrypt).

---

## Prerequisites

- [Node.js](https://nodejs.org/) (v14 or higher)
- [MySQL](https://www.mysql.com/) (v5.7 or higher)
- npm

---

## Installation & Setup

### 1. Clone the repository

```bash
git clone https://github.com/your-username/smart-bus-ticket-booking-system.git
cd smart-bus-ticket-booking-system
```

### 2. Install dependencies

```bash
npm install express express-session qrcode axios querystring mysql xlsx
```

### 3. Set up the MySQL database

```sql
CREATE DATABASE bussystem;

USE bussystem;

CREATE TABLE passengers (
  id INT AUTO_INCREMENT PRIMARY KEY,
  fullname VARCHAR(255),
  email VARCHAR(255),
  mobilenumber VARCHAR(20),
  password VARCHAR(255)
);

CREATE TABLE conductors (
  id VARCHAR(50) PRIMARY KEY,
  name VARCHAR(255),
  mobilenumber VARCHAR(20),
  password VARCHAR(255)
);

CREATE TABLE bus_details (
  bus_numbers VARCHAR(50),
  from_location VARCHAR(255),
  destination VARCHAR(255),
  distance VARCHAR(50)
);

CREATE TABLE areaslatlon (
  area VARCHAR(255),
  latitude VARCHAR(50),
  longitude VARCHAR(50)
);
```

### 4. Configure database credentials

Open `public/js/db.js` and update the connection settings:

```js
const pool = mysql.createPool({
  connectionLimit: 10,
  host: 'localhost',
  user: 'root',          // your MySQL username
  password: 'yourpassword',  // your MySQL password
  database: 'bussystem'
});
```

### 5. Seed the database from Excel files

Bus route and coordinate data is stored in Excel files under `bus info/`. Use `index1.js` to import the area coordinates into the database:

```bash
node index1.js
# Then visit: http://localhost:3000/api/locations
```

Populate `bus_details` manually or via a MySQL import from the provided `.xlsx` files using a tool like MySQL Workbench or `LOAD DATA`.

### 6. Start the server

```bash
node index.js
```

The application will be running at `http://localhost:3000`.

---

## API Endpoints

| Method | Endpoint                  | Description                                  |
|--------|---------------------------|----------------------------------------------|
| GET    | `/`                       | Home page                                    |
| GET    | `/signup`                 | Registration page                            |
| POST   | `/signup`                 | Register a new passenger                     |
| GET    | `/login`                  | Login page                                   |
| POST   | `/login`                  | Authenticate a passenger                     |
| GET    | `/logout`                 | Destroy session and redirect home            |
| GET    | `/profile`                | View logged-in user profile (auth required)  |
| GET    | `/user-details`           | Get session user info as JSON                |
| GET    | `/bookbus`                | Bus booking page (auth required)             |
| GET    | `/paymentdetails`         | Ticket and QR code display                   |
| GET    | `/conductor`              | Conductor login page                         |
| POST   | `/conductor-dashboard`    | Authenticate conductor                       |
| GET    | `/validateQRCode`         | QR code scanner for conductors               |
| GET    | `/api/from-options`       | Autocomplete for source location             |
| GET    | `/api/to-options`         | Autocomplete for destination location        |
| POST   | `/api/find-buses`         | Find buses between two locations             |
| GET    | `/api/get-coordinates`    | Get lat/lon for a location name              |
| GET    | `/api/bus-options`        | Autocomplete for bus numbers                 |
| GET    | `/api/get-map-data`       | Get route map data for a bus number          |

---

## Known Issues & Limitations

- Passwords are stored in **plain text** — add bcrypt hashing before deploying.
- Some SQL queries use **string interpolation** instead of parameterized queries, making them vulnerable to SQL injection (e.g., in `db.js` `get()` and `getto()` functions).
- The session secret (`'209617'`) is hardcoded — move it to an environment variable.
- No `package.json` is included in the repository; dependencies must be installed manually.
- `index1.js` and `index3.js` appear to be development/utility scripts and should not be run as the primary server.


This project is open-source and available under the [MIT License](LICENSE).
