# 🚛 TransitOps – Smart Transport Operations Platform for BUSINESS  

## 📌 Overview

**TransitOps** is a transport and fleet management platform developed for the **Odoo Hackathon 2026**. It helps logistics companies digitize and streamline their day-to-day transport operations by replacing spreadsheets and manual records with a centralized, intelligent management system.

The platform manages the complete transportation lifecycle—from vehicle registration and driver management to trip dispatching, maintenance scheduling, fuel tracking, expense monitoring, and operational analytics.

---

# 🚀 Business Problem

Many logistics companies still rely on manual processes such as spreadsheets, paper logbooks, and disconnected systems to manage their fleet. These practices often result in:

* Vehicle scheduling conflicts
* Poor fleet utilization
* Missed maintenance schedules
* Expired driver licenses going unnoticed
* Inaccurate fuel and expense tracking
* Limited operational visibility

**TransitOps** solves these challenges by providing a single platform that automates transport operations and enforces business rules to reduce human error.

---

# 👥 Target Users

| Role                  | Responsibilities                                                                             |
| --------------------- | -------------------------------------------------------------------------------------------- |
| **Fleet Manager**     | Manages fleet assets, maintenance schedules, vehicle lifecycle, and operational efficiency.  |
| **Dispatcher**        | Creates trips, assigns vehicles and drivers, and monitors active deliveries.                 |
| **Safety Officer**    | Tracks driver compliance, monitors license validity, and reviews safety scores.              |
| **Financial Analyst** | Analyzes fuel consumption, maintenance expenses, operational costs, and fleet profitability. |

---

# ✨ Features

## 🔐 Authentication

* Secure email and password login
* Role-Based Access Control (RBAC)
* Protected routes for authenticated users

---

## 📊 Dashboard

A real-time overview of transport operations, including:

* Active Vehicles
* Available Vehicles
* Vehicles Under Maintenance
* Active Trips
* Pending Trips
* Drivers On Duty
* Fleet Utilization (%)

### Filters

* Vehicle Type
* Vehicle Status
* Region

---

## 🚚 Vehicle Management

Manage the complete vehicle registry.

### Vehicle Details

* Registration Number *(Unique)*
* Vehicle Name / Model
* Vehicle Type
* Maximum Load Capacity
* Odometer Reading
* Acquisition Cost
* Current Status

### Vehicle Status

* Available
* On Trip
* In Shop
* Retired

---

## 👨‍✈️ Driver Management

Maintain complete driver profiles.

### Driver Details

* Name
* License Number
* License Category
* License Expiry Date
* Contact Number
* Safety Score
* Status

### Driver Status

* Available
* On Trip
* Off Duty
* Suspended

---

## 🗺️ Trip Management

Create and manage transport trips.

Each trip contains:

* Source
* Destination
* Assigned Vehicle
* Assigned Driver
* Cargo Weight
* Planned Distance

### Trip Lifecycle

```
Draft
   ↓
Dispatched
   ↓
Completed

OR

Cancelled
```

---

## 🔧 Maintenance Management

Track vehicle servicing and repairs.

Features include:

* Create maintenance records
* Automatic vehicle status update to **In Shop**
* Automatic restoration to **Available** when maintenance is completed
* Vehicles under maintenance are unavailable for dispatch

---

## ⛽ Fuel & Expense Tracking

Monitor operational costs efficiently.

### Fuel Logs

* Fuel Quantity (Liters)
* Fuel Cost
* Date

### Expense Categories

* Fuel
* Maintenance
* Toll Charges
* Other Operational Expenses

### Automatic Calculations

* Total Fuel Cost
* Total Maintenance Cost
* Total Operational Cost per Vehicle

---

## 📈 Reports & Analytics

Generate valuable operational insights.

### Metrics

* Fuel Efficiency *(Distance ÷ Fuel Consumed)*
* Fleet Utilization
* Operational Cost
* Vehicle Return on Investment (ROI)

```
ROI =
(Revenue − (Fuel Cost + Maintenance Cost))
÷ Acquisition Cost
```

### Export Options

* CSV Export
* PDF Export *(Optional)*

---

# ⚖️ Business Rules

The application automatically enforces the following validations:

* Vehicle registration numbers must be unique.
* Retired or In Shop vehicles cannot be assigned to trips.
* Drivers with expired licenses cannot be dispatched.
* Suspended drivers cannot be assigned to trips.
* A vehicle already on a trip cannot be assigned again.
* A driver already on a trip cannot be assigned again.
* Cargo weight must not exceed the vehicle's maximum load capacity.
* Dispatching a trip automatically changes the vehicle and driver status to **On Trip**.
* Completing a trip automatically restores both statuses to **Available**.
* Cancelling a dispatched trip restores both statuses to **Available**.
* Creating an active maintenance record changes the vehicle status to **In Shop**.
* Closing maintenance restores the vehicle status to **Available**, unless the vehicle is retired.

---

# 🔄 Example Workflow

### Step 1

Register a vehicle:

* Vehicle: **Van-05**
* Maximum Capacity: **500 kg**
* Status: **Available**

### Step 2

Register a driver with a valid driving license.

### Step 3

Create a trip with:

* Cargo Weight: **450 kg**

The system validates:

```
450 kg ≤ 500 kg
```

Trip is successfully dispatched.

### Step 4

Vehicle and Driver statuses automatically change to:

```
On Trip
```

### Step 5

Complete the trip by entering:

* Final Odometer Reading
* Fuel Consumed

Statuses automatically return to:

```
Available
```

### Step 6

Create a maintenance record (e.g., Oil Change).

Vehicle status becomes:

```
In Shop
```

The vehicle is automatically removed from dispatch selection.

### Step 7

Reports update automatically with:

* Fuel Efficiency
* Operational Cost
* Vehicle Analytics

---

# 🗄️ Database Entities

* Users
* Roles
* Vehicles
* Drivers
* Trips
* Maintenance Logs
* Fuel Logs
* Expenses

---

# ✅ Core Deliverables

* Responsive Web Interface
* Secure Authentication
* Role-Based Access Control (RBAC)
* Vehicle CRUD Operations
* Driver CRUD Operations
* Trip Management
* Automatic Status Transitions
* Maintenance Workflow
* Fuel & Expense Tracking
* Dashboard with KPIs

---

# 🌟 Bonus Features

* Interactive Charts & Analytics
* PDF Report Export
* Email Notifications for Expiring Licenses
* Vehicle Document Management
* Advanced Search & Filters
* Sorting
* Dark Mode

---

# 🎨 UI Design

UI Mockups are designed in **Excalidraw**.

---

# 🛠️ Tech Stack

> Update this section after finalizing your implementation.

* Frontend:
* Backend:
* Database:
* Authentication:
* Deployment:

---

# ⚙️ Installation

```bash
# Clone the repository
git clone <repository-url>

# Navigate into the project
cd transitops

# Install dependencies
npm install

# Configure environment variables
cp .env.example .env

# Start the development server
npm start
```

---

# 📂 Project Structure

```
TransitOps
│
├── frontend
├── backend
├── database
├── public
├── src
├── components
├── pages
├── services
├── utils
└── README.md
```

---

# 🤝 Contributing

Contributions are welcome.

1. Fork the repository.
2. Create a new feature branch.
3. Commit your changes.
4. Push your branch.
5. Open a Pull Request.

---

# 📄 License

This project was developed as part of the **Odoo Hackathon 2026** for educational and demonstration purposes.
