# TransitOps: Developer Summary (PRD)

TransitOps is a centralized fleet and transport operations platform designed to replace manual spreadsheets and logbooks with a digital system. It manages the full lifecycle of vehicles and drivers – from registration to dispatch, maintenance, fuel/expenses, and analytics. The goal is to prevent conflicts and inefficiencies (like double-bookings or expired licenses) and provide real-time operational insights. In practice, TransitOps covers tasks such as vehicle tracking, driver oversight, trip scheduling, maintenance logging, and cost reporting, all in one system.

According to industry sources, modern fleet management platforms must handle all aspects of operations – scheduling, dispatch, maintenance, fuel, and compliance. For example, a fleet management system should be a “centralized platform to manage all aspects of fleet operations, from scheduling and dispatching to maintenance and compliance”. As a developer, think of building TransitOps as delivering that complete solution: each module (Vehicles, Drivers, Trips, etc.) corresponds to real-world fleet processes, backed by business rules (e.g. valid licenses, weight limits) and supporting dashboards and reports.

## Core Features & Business Rules

TransitOps must implement the following modules and rules:

### 1. Authentication & RBAC
* **Secure Login**: Secure login (email/password) with Role-Based Access Control (RBAC).
* **User Roles**: Users have roles like **Fleet Manager**, **Driver**, **Safety Officer**, and **Financial Analyst**.
* **Access Enforcement**: Only authenticated users can use the system; roles determine permitted actions (e.g., only managers/officers can create vehicles or drivers).

### 2. Dashboard
* **KPIs & Charts**: A home screen showing KPIs and charts. Example metrics include *Active Vehicles*, *Available Vehicles*, *Vehicles In Maintenance*, *Active Trips*, *Pending Trips*, *Drivers On Duty*, and *Fleet Utilization (%)*.
* **Filters**: Allow filtering by vehicle type, status, region, etc., to allow managers to drill down.
* **Data Integration**: Gathers data from all modules to give real-time visibility, helping managers make data-driven decisions.

### 3. Vehicle Registry
* **CRUD Operations**: Support creating, reading, updating, and deleting vehicles.
* **Fields**: Each vehicle has a unique registration number, name/model, type (van, truck, etc.), max load capacity, odometer, acquisition cost, and status (e.g., *Available*, *On Trip*, *In Shop*, *Retired*).
* **Business Rules**:
  * Registration numbers must be unique.
  * Vehicles with status *Retired* or *In Shop* are excluded from trip dispatch selection.
  * Vehicles auto-change status based on other actions (see Trip and Maintenance below).
  * The system should notify when maintenance is due (e.g., via odometer thresholds).

### 4. Driver Management
* **CRUD Operations**: Support creating, reading, updating, and deleting drivers.
* **Fields**: Each driver has name, license number, license category, license expiry date, contact details, safety score, and status (*Available*, *On Trip*, *Off Duty*, *Suspended*).
* **Business Rules**:
  * The system stores each driver’s info and tracks compliance. For example, it should verify a valid license and required training before assignment.
  * Drivers with expired or suspended status cannot be assigned to trips.
  * The system may enforce work-hour limits (fatigue management) by tracking time on duty and sending alerts if limits are exceeded.
  * The *Safety Officer* role would use this module to monitor driver qualifications and safety scores.

### 5. Trip (Dispatch) Management
* **Description**: Allows creation and control of trips (deliveries).
* **Fields**: A trip record includes source, destination, cargo weight, planned distance, chosen vehicle, and driver.
* **Lifecycle**: `Draft` &rarr; `Dispatched` &rarr; `Completed` &rarr; `Cancelled`.
* **Key Rules**:
  * Can only dispatch if a driver and vehicle are both *Available*, the driver’s license is valid, and cargo weight &le; vehicle’s max capacity.
  * When a trip is dispatched, mark its vehicle and driver as *On Trip* immediately.
  * A vehicle or driver already *On Trip* cannot be assigned elsewhere simultaneously.
  * Completing a trip (entering final odometer, fuel used, etc.) sets vehicle and driver back to *Available* and records trip data.
  * Cancelling a dispatched trip also reverts vehicle/driver to *Available*.
* **Example flow**: Register Van-05 (capacity 500kg), register Driver Alex. Create a trip for 450kg cargo. System checks weight ($450 < 500$) and licenses, then allows dispatch. Vehicle/Driver go *On Trip*. On completion, both become *Available*.

### 6. Maintenance
* **Description**: Log vehicle maintenance.
* **Lifecycle**: When a maintenance record is opened (e.g., “Oil Change”), the vehicle status automatically changes to *In Shop* (hiding it from dispatch). When the maintenance record is closed, the vehicle returns to *Available* (unless you retire it).
* **Fields**: Maintenance logs capture type of service, date, cost, mechanic, and notes.
* **Rules**: Enforces the rule that vehicles under maintenance cannot be dispatched. Ensures preventive maintenance scheduling; the system can auto-notify when service is due.

### 7. Fuel & Expense Management
* **Description**: Record all fuel and expense entries.
* **Fields**: For each fuel purchase (liters, cost, date) and other expenses (tolls, part replacements, repairs), associate them with a vehicle (and optionally a trip).
* **Aggregation**: The system should automatically sum these to compute each vehicle’s total operational cost.
* **Key Metrics**: Fuel Efficiency (distance per fuel) and Total Cost per km. These data feed into reports.

### 8. Reports & Analytics
Provide charts and tables such as:
* **Fuel Efficiency**: e.g., distance driven / liters consumed per vehicle or per fleet (data visualization).
* **Fleet Utilization**: % of vehicles active vs idle, by period.
* **Operational Cost**: sum of fuel+maintenance costs, and ROI per vehicle:
  $$\text{ROI} = \frac{\text{Revenue} - (\text{Fuel} + \text{Maintenance})}{\text{Acquisition Cost}}$$
* **Visualizations**: Vehicle ROI (profitability) and utilization heatmaps.
* **Data Export**: Support data export (CSV, optional PDF).

---

## System Architecture (Developer View)

As a developer, think of TransitOps as a web application with a layered architecture:

* **Frontend (Web UI)**: A responsive web interface (e.g., React or Angular) for users in all roles. It provides forms and views for each module (Vehicles, Drivers, Trips, etc.), plus the Dashboard and Reports. We’ll implement RBAC on the frontend (hiding or disabling UI elements based on role) and enforce it on the server as well.
* **Backend/API**: A RESTful (or similar) API to handle all data operations. Example endpoints:
  * `POST /login` – authenticate and return JWT/session.
  * `GET/POST/PUT/DELETE /vehicles` – CRUD for vehicles.
  * `GET/POST/PUT/DELETE /drivers` – CRUD for drivers.
  * `GET/POST/PUT/DELETE /trips` – manage trips.
  * `GET/POST/PUT/DELETE /maintenance` – maintenance logs.
  * `GET/POST/PUT/DELETE /expenses` – fuel and other expenses.
  * `GET /reports/overview` – compute and return KPI data for Dashboard.
  * `POST /report/exports` – generate CSV/PDF exports.
* **Database**: A relational DB (e.g., SQLite, PostgreSQL, or MySQL) or a document DB if preferred. Suggested tables/collections:
  * **Users** (id, name, email, password hash, role_id, etc.)
  * **Roles** (Admin, FleetManager, Driver, SafetyOfficer, FinancialAnalyst, etc.)
  * **Vehicles** (id, reg_no, name, type, max_capacity, odometer, cost, status, etc.)
  * **Drivers** (id, name, license_no, category, license_expiry, contact, safety_score, status, etc.)
  * **Trips** (id, vehicle_id, driver_id, source, destination, cargo_weight, distance, status, start_time, end_time, etc.)
  * **MaintenanceLogs** (id, vehicle_id, description, cost, start_date, end_date, notes)
  * **FuelLogs** (id, vehicle_id, liters, cost, date, odometer_reading)
  * **Expenses** (id, trip_id, vehicle_id, description, amount, date)

---

## Development Plan & Checklist

### 1. Setup Project & Authentication
* Scaffold the project (choose stack, e.g., Node.js/Express + React).
* Implement user model and authentication (JWT or sessions).
* Create role-based middleware. Populate roles (Admin, FleetManager, Driver, etc.).
* *Checklist*: Ability to register/login users; RBAC enforcement in API routes.

### 2. Vehicle Module
* Create Vehicle API and UI (forms to add/edit vehicles).
* Enforce unique registration number in the database schema.
* Provide filtering (by type, status) on vehicle listing.
* *Checklist*: Can add/edit/list vehicles; unique reg-no check; status updates propagate correctly (e.g., when dispatch or maintenance actions happen).

### 3. Driver Module
* Create Driver API and UI (add/edit drivers).
* Track license info and expiry dates.
* *Checklist*: Can manage drivers; system prevents using a driver with expired license (backend validation).

### 4. Trip Management
* Implement trip CRUD. Key endpoints to create a trip in “Draft” and to Dispatch it.
* The Dispatch operation should perform validation: check vehicle/driver availability, license, weight &le; capacity.
* On dispatch, update statuses. On complete/cancel, reset statuses.
* *Checklist*: Trips move through their lifecycle. Attempting an invalid dispatch (double-book, overloaded cargo, bad license) should fail with clear error.

### 5. Maintenance Module
* Maintenance logs for vehicles.
* On creating a log, automatically set vehicle status to *In Shop*. (In DB transaction to avoid race conditions.)
* Closing a log sets vehicle to *Available* (unless retired).
* *Checklist*: Vehicle is hidden from dispatch while a maintenance record is active.

### 6. Fuel & Expense Module
* APIs to log fuel purchases and other expenses (tolls, repairs).
* Link fuel logs to trips or vehicles. Sum them per vehicle.
* *Checklist*: Fuel logs and expense logs can be entered and aggregated per vehicle.

### 7. Dashboard & Reports
* Build Dashboard page that queries the backend for KPIs (e.g., `GET /reports/overview` returns counts and summaries).
* Implement charts or tables (e.g., using chart library).
* Provide export buttons (CSV for details, PDF optional).
* *Checklist*: Dashboard shows real data; exports work.

### 8. Testing & Validation
* Write unit tests for business logic (e.g., trip dispatch rules).
* Integration tests for API endpoints (especially for status transitions).
* Test workflows end-to-end: e.g., register vehicle & driver, dispatch trip, complete trip, do maintenance, verify reports.
* *Checklist*: All test cases pass; manual testing of scenario works flawlessly.

### 9. Deployment & Documentation
* Deploy to a server (or just prepare build).
* Prepare README/documentation of APIs and data models.
* Possibly set up database seed data (some example vehicles, drivers).
* *Checklist*: Application can be run from source; documentation covers setup and key endpoints.

### 10. Bonus Features (Time Permitting)
* Email reminders for expiring licenses.
* Vehicle document upload (registration papers, insurance).
* Dark mode or UI polish.

---

## Implementation Checklist
- [ ] **Authentication**: User signup/login working; roles defined; RBAC enforced.
- [ ] **Vehicle Registry**: Add/edit/list vehicles; unique reg. numbers; status field implemented.
- [ ] **Driver Module**: Add/edit/list drivers; license info stored; status field; validation for expiry.
- [ ] **Trip Management**: Trip CRUD; dispatch/cancel/complete flows; weight & availability checks; status updates.
- [ ] **Maintenance**: Create/close logs; auto status changes; exclude from dispatch.
- [ ] **Fuel & Expenses**: Fuel log entries; other expenses; total cost computed.
- [ ] **Dashboard/KPIs**: Metrics calculated & displayed (vehicles, trips, utilization, etc.).
- [ ] **Reports**: Fuel efficiency, ROI, etc.; CSV export works; PDF export (if possible).
- [ ] **Notifications**: At least in-app alerts for key events (e.g., expiration reminders).
- [ ] **Testing**: Automated tests for each module and rule; end-to-end scenario tests.
- [ ] **Documentation**: API spec, DB schema, deployment instructions.
