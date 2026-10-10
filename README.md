# VoltGrid — EV Charging Station Finder

A modern, responsive full-stack **MERN** web application designed to help electric vehicle (EV) drivers discover charging stations, check real-time port availability, filter by connector types and power speed, explore stations on interactive maps, and provide administrators with complete CRUD management capabilities.

---

## ⚡ Key Features

### For EV Drivers & Public Users
- **Find Your Next EV Charging Point**: Hero landing with quick locality search and instant city quick-filters.
- **Dynamic Search & Multi-Criteria Filtering**:
  - Search by station name, city, locality, or street address.
  - Filter by Charger Type (`Ultra Fast DC`, `DC Fast`, `AC Type 2`, `AC Slow`).
  - Filter by Connector Standard (`CCS2`, `CHAdeMO`, `Type 2 AC`, `GB/T`, `Bharat DC-001`, `Bharat AC-001`).
  - Filter by Availability Status (`Available`, `Occupied`, `Out of Service`).
  - Maximum tariff filter (₹/kWh) and sort by tariff, name, or recency.
- **Interactive OpenStreetMap with Leaflet**:
  - Live color-coded marker pins reflecting availability states (Green = Available, Amber = Busy, Red = Fault/Offline).
  - Marker popups displaying port counts, max power rating in kW, connector tags, pricing, and 1-click details link.
  - "Locate My Position" GPS geocoding button with fallback to manual search.
  - Responsive split-view and mobile slide-out drawer.
- **Detailed Station Specs Page**:
  - Full address, operating hours, and customer desk phone.
  - Real-time connector inventory with individual bay status breakdown.
  - Transparent visual availability bar (`available + occupied + offline = total`).
  - Direct turn-by-turn navigation link to Google Maps / OpenStreetMap.
  - Station amenities checklist (Restrooms, Café, Wi-Fi, Lounge, Security, ATM).

### For Administrators
- **Secured Authentication**: JWT-based session security with bcrypt password hashing and IP rate limiting.
- **Comprehensive Command Dashboard**:
  - Real-time aggregation of total stations, sockets, available slots, busy bays, and offline ports.
  - City cluster breakdown and connector distribution percentages.
- **Station Management CRUD**:
  - **Create**: Register new stations with geographic coordinates and multi-charger groups.
  - **Read**: Search, filter, and paginate through all registered stations.
  - **Update**: Edit station metadata, operating schedules, and connector ratings.
  - **Quick Availability Manager**: Update live available, busy, and out-of-service slot counts with real-time sum validation.
  - **Delete**: Safe station deletion with confirmation dialog modal.
- **Strict Data Integrity Enforcement**:
  - MongoDB GeoJSON `Point` coordinates validated to `[-90, 90]` latitude and `[-180, 180]` longitude.
  - Mathematical constraint: `availablePoints + occupiedPoints + outOfServicePoints === totalPoints`.

---

## 🛠️ Technology Stack

| Layer | Technologies |
| :--- | :--- |
| **Frontend** | React 18 (Vite), JavaScript ES6+, Tailwind CSS, React Router DOM, React Leaflet, OpenStreetMap, Lucide React, Axios |
| **Backend** | Node.js, Express.js (REST architecture), express-validator, jsonwebtoken, bcryptjs, cors, dotenv, express-rate-limit, morgan |
| **Database** | MongoDB (Community Server / Atlas), Mongoose ORM, GeoJSON `Point` with `2dsphere` spatial indexing |
| **Testing** | Vitest, Supertest (15 backend integration tests) |

---

## 📁 Project Structure

```text
ev-charging-station-finder/
├── client/                     # Vite React Frontend
│   ├── src/
│   │   ├── components/         # Reusable UI components (Navbar, StationCard, StationMap, etc.)
│   │   ├── context/            # AuthContext (JWT session state)
│   │   ├── pages/              # Route pages (HomePage, StationsPage, StationDetailPage, MapPage, Admin...)
│   │   ├── services/           # Axios API services (stationService, authService, api client)
│   │   ├── utils/              # Helper formatters and color badge utilities
│   │   ├── App.jsx             # React Router routing configuration
│   │   ├── main.jsx            # React root mount
│   │   └── index.css           # Tailwind base styles and Leaflet customization
│   ├── public/                 # Static assets
│   ├── index.html              # HTML shell with Google Fonts & Leaflet CSS
│   ├── package.json
│   ├── tailwind.config.js
│   ├── vite.config.js
│   └── .env.example
├── server/                     # Express.js REST API Backend
│   ├── src/
│   │   ├── config/             # MongoDB database connection
│   │   ├── controllers/        # Request handlers (stationController, authController, statsController)
│   │   ├── middleware/         # Auth guard, error handler, rate limiter
│   │   ├── models/             # Mongoose schemas (ChargingStation, Admin)
│   │   ├── routes/             # Express routers (/api/stations, /api/auth, /api/stats)
│   │   ├── validators/         # express-validator schemas
│   │   ├── app.js              # Express app setup with CORS and middlewares
│   │   └── server.js           # HTTP server bootstrap
│   ├── scripts/
│   │   ├── seed.js             # 12 sample Indian stations seed + admin setup
│   │   └── createAdmin.js      # Dedicated admin creation script
│   ├── tests/
│   │   └── api.test.js         # Vitest + Supertest automated test suite
│   ├── package.json
│   └── .env.example
├── package.json                # Monorepo root scripts
├── .gitignore
└── README.md
```

---

## 🚀 Quick Start Guide

### Prerequisites
- **Node.js**: Version 18.x, 20.x, or 24.x installed
- **MongoDB**: Local MongoDB Community Server running on port `27017` **or** a MongoDB Atlas connection string

---

### Step 1: Clone and Configure Environment

```bash
# Clone the repository
git clone <repository-url>
cd "EV charging station finder"
```

#### Backend Environment (`server/.env`)
Create `server/.env` (or copy from `server/.env.example`):
```env
PORT=5000
MONGODB_URI=mongodb://127.0.0.1:27017/ev_charging_finder
JWT_SECRET=super_secret_ev_charge_jwt_token_key_2026_xyz
JWT_EXPIRES_IN=24h
CLIENT_URL=http://localhost:5173
NODE_ENV=development
```

> **Using MongoDB Atlas?** Replace `MONGODB_URI` with your connection string:  
> `MONGODB_URI=mongodb+srv://<username>:<password>@cluster0.mongodb.net/ev_charging_finder?retryWrites=true&w=majority`

#### Frontend Environment (`client/.env`)
Create `client/.env` (or copy from `client/.env.example`):
```env
VITE_API_BASE_URL=http://localhost:5000/api
```

---

### Step 2: Install Dependencies & Seed Database

```bash
# 1. Install Server Dependencies
cd server
npm install

# 2. Seed Database with 12 Demonstration Stations across Indian Metros
npm run seed
```

Output:
```text
[MongoDB Connected for Data Seeding]
[Cleared existing ChargingStations]
[Successfully seeded 12 realistic demonstration stations]
[Admin seeded] Username: admin | Password: Admin@12345
[MongoDB Disconnected. Seeding Complete]
```

```bash
# 3. Install Client Dependencies
cd ../client
npm install
cd ..
```

---

### Step 3: Run the Application

Open two terminal windows:

#### Terminal 1 — Backend API Server:
```bash
cd server
npm run dev
```
> Server runs at: `http://localhost:5000`  
> Health check: `http://localhost:5000/api/health`

#### Terminal 2 — Frontend Client:
```bash
cd client
npm run dev
```
> Frontend application runs at: `http://localhost:5173`

---

## 🔑 Default Administrator Credentials

| Username | Password | Role | Console URL |
| :--- | :--- | :--- | :--- |
| `admin` | `Admin@12345` | Superadmin | `http://localhost:5173/admin/login` |

*(A 1-click **"Autofill Demo Admin Credentials"** button is also available on the Admin Login page for fast testing).*

---

## 📡 REST API Reference

Base URL: `http://localhost:5000/api`

### Public Station Endpoints
| Method | Endpoint | Description | Query Parameters |
| :--- | :--- | :--- | :--- |
| `GET` | `/api/health` | Health check | None |
| `GET` | `/api/stats` | Network aggregated metrics | None |
| `GET` | `/api/stations` | List stations with filters | `search`, `city`, `chargerType`, `connectorType`, `availability`, `maxPrice`, `sortBy`, `order`, `page`, `limit` |
| `GET` | `/api/stations/:id` | Single station details | None |
| `GET` | `/api/stations/nearby` | Geospatial nearby search | `latitude`, `longitude`, `radius` (km) |

### Admin Endpoints (Requires `Authorization: Bearer <token>`)
| Method | Endpoint | Description | Request Body |
| :--- | :--- | :--- | :--- |
| `POST` | `/api/auth/login` | Admin login | `{ username, password }` |
| `GET` | `/api/auth/me` | Current admin session | None |
| `POST` | `/api/stations` | Create new station | Station fields & chargers array |
| `PUT` | `/api/stations/:id` | Update station | Updated station fields |
| `PATCH` | `/api/stations/:id/availability` | Update slot availability | `{ chargers: [...], status }` |
| `DELETE` | `/api/stations/:id` | Delete station record | None |

---

## 🧪 Automated Testing

Backend integration tests verify health, public station discovery, filtering, admin login, token guards, charger sum enforcement, and station CRUD operations:

```bash
cd server
npm test
```

Expected result:
```text
✓ tests/api.test.js (15 tests)
  ✓ 1. Health and Public Stats APIs > GET /api/health should return online status
  ✓ 1. Health and Public Stats APIs > GET /api/stats should return aggregated charging statistics
  ✓ 2. Stations Public APIs > GET /api/stations should list stations with pagination
  ✓ 2. Stations Public APIs > GET /api/stations with search and filter should return filtered results
  ✓ 2. Stations Public APIs > GET /api/stations/:id should return single station details
  ✓ 2. Stations Public APIs > GET /api/stations/:id with invalid ID should return 404
  ✓ 3. Admin Authentication APIs > POST /api/auth/login with incorrect credentials should fail
  ✓ 3. Admin Authentication APIs > POST /api/auth/login with valid credentials should return JWT token
  ✓ 3. Admin Authentication APIs > GET /api/auth/me should return admin profile when authenticated
  ✓ 3. Admin Authentication APIs > GET /api/auth/me without token should return 401
  ✓ 4. Station Management CRUD and Availability Enforcement > POST /api/stations without auth should be rejected with 401
  ✓ 4. Station Management CRUD and Availability Enforcement > POST /api/stations with invalid charger count sum should fail with 400
  ✓ 4. Station Management CRUD and Availability Enforcement > POST /api/stations with valid data should create station
  ✓ 4. Station Management CRUD and Availability Enforcement > PATCH /api/stations/:id/availability should update charger counts
  ✓ 4. Station Management CRUD and Availability Enforcement > DELETE /api/stations/:id should delete station

Test Files  1 passed (1)
Tests       15 passed (15)
```

---

## 🔍 Demonstration Data Note
The 12 seeded charging stations represent realistic mock locations across major corridors in **Hyderabad**, **Bengaluru**, **Chennai**, **Mumbai**, and **Delhi NCR** with valid geographic coordinates. They are intended for demonstration, academic, and hackathon presentation purposes.

---

## 🔮 Future Enhancements
- Integration with live OCPP (Open Charge Point Protocol) 2.0.1 charge point management systems.
- Automated route charging planner estimating battery drain between stops.
- User reviews, driver rating badges, and uploaded station photos.
- Slot reservation and advance bay booking mechanism.
