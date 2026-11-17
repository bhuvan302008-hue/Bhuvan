1. # DriveSphere VRMS — Vehicle Rental Management System (v3.0)

**DriveSphere VRMS** is a self-contained, single-file web application for managing two-wheeler and four-wheeler vehicle rentals across Bengaluru hubs. Built with zero external runtime dependencies and an offline-safe storage engine, it simulates a complete Role-Based Access Control (RBAC) platform featuring a **Customer Booking Portal** and an **Admin Fleet & Reservation Dashboard**.

---

## Key Features

### 1\. Customer Booking Portal

* **Bengaluru Multi-Hub Search & Filtering:** Search and filter a 12-vehicle fleet across campus and transit hubs (*Koramangala Campus Hub, Indiranagar Metro Station, Electronic City Phase 1, Yelahanka New Town Hub, Whitefield ITPL Gate*).  
* **Category & Keyword Filtering:** Filter by *Bikes & Scooters*, *Basic Cars*, *Sedan*, *SUV*, and *Electric*, or search directly by model, fuel type, or category.  
* **RTO License Class Validation:** Displays required Indian driving license classes (`MCWG / Gearless`, `MCWG (Geared)`, `LMV (4-Wheeler)`) and captures customer KYC details during checkout.  
* **Dynamic Tariff & Add-On Calculator:**  
  * Automatically calculates rental duration from pickup and return dates.  
  * Offers vehicle-type-aware add-ons (e.g., *Extra Pillion ISI Helmet* and *Handlebar Phone Mount* for two-wheelers; *GPS & FastTag Pack* and *ISOFIX Child Seat* for four-wheelers).  
  * Supports promotional discount codes:  
    * `STUDENT15` — **15% off** subtotal (College Student Discount)  
    * `FIRST100` — **₹100 flat off** subtotal  
  * Computes **18% GST** (9% CGST \+ 9% SGST) and waives security deposits for students.  
* **Printable GST Tax Invoice / Gate Pass:** Generates an itemized tax invoice upon confirmation with one-click print/PDF export support (`@media print`).  
* **My Bookings Drawer:** View active and historical trips, check status badges, and cancel active bookings with automatic fleet availability restoration.

### 2\. Admin Control Center (RBAC)

* **Live Operational KPIs:** Tracks **Total Gross Revenue** (incl. GST), **Active Rentals**, **Available Fleet Count**, and **Fleet Utilization Percentage**.  
* **Reservation Ledger (`BOOKINGS`):** Inspect all customer reservations, verify RTO license numbers, and check in returned vehicles (`Mark Returned`), atomically transitioning vehicle status back to `AVAILABLE`.  
* **Fleet Master CRUD (`VEHICLES`):** Add new vehicles, edit tariffs/specifications/images, delete unbooked units, or reset the entire database to its default 12-vehicle dataset.

### 3\. Project Docs & 3NF Schema Inspector

* Built-in **Project Docs / 3NF Schema** modal designed for software engineering and DBMS evaluations, showcasing the normalized relational table definitions alongside a live JSON state inspector.

---

## Database Architecture (3NF Relational Schema)

DriveSphere persists data using versioned `localStorage` keys (`drivesphere_vrms_v3_*`) with an automatic in-memory fallback so it runs seamlessly even when opened directly via `file://`.

### `VEHICLES` Table

| Column | Type | Constraints / Notes |
| :---- | :---- | :---- |
| `vehicle_id` | `VARCHAR(12)` | **Primary Key** (e.g., `VEH-101`) |
| `model_name` | `VARCHAR(80)` | `NOT NULL` |
| `category` | `ENUM` | `'Bikes & Scooters'`, `'Basic Cars'`, `'Sedan'`, `'SUV'`, `'Electric'` |
| `daily_rate` | `DECIMAL(10,2)` | `CHECK (daily_rate > 0)` (INR per day) |
| `license_class` | `VARCHAR(30)` | `MCWG / Gearless`, `MCWG (Geared)`, `LMV (4-Wheeler)` |
| `status` | `ENUM` | `'AVAILABLE'`, `'BOOKED'`, `'MAINTENANCE'` |

### `BOOKINGS` Table

| Column | Type | Constraints / Notes |
| :---- | :---- | :---- |
| `booking_id` | `VARCHAR(16)` | **Primary Key** (e.g., `BKG-4821`) |
| `vehicle_id` | `VARCHAR(12)` | **Foreign Key** $\rightarrow$ `VEHICLES(vehicle_id)` |
| `customer_name` | `VARCHAR(80)` | Customer full name |
| `dl_number` | `VARCHAR(25)` | Indian RTO Driving License number |
| `pickup_date` | `DATE` | Rental start date |
| `return_date` | `DATE` | Rental end date |
| `gst_amount` | `DECIMAL(10,2)` | 18% GST computed on taxable subtotal |
| `grand_total` | `DECIMAL(10,2)` | Final payable amount in INR |
| `booking_status` | `ENUM` | `'ACTIVE'`, `'COMPLETED'`, `'CANCELLED'` |

---

## Preloaded Fleet Inventory (12 Units)

| ID | Model | Category | Rate / Day | Fuel & Transmission | License Class |
| :---- | :---- | :---- | :---- | :---- | :---- |
| `VEH-101` | Honda Activa 6G | Bikes & Scooters | ₹450 | Petrol • CVT | MCWG / Gearless |
| `VEH-102` | TVS Jupiter 125 | Bikes & Scooters | ₹490 | Petrol • CVT | MCWG / Gearless |
| `VEH-103` | Hero Splendor Plus | Bikes & Scooters | ₹399 | Petrol • 4-Speed Manual | MCWG (Geared) |
| `VEH-104` | Bajaj Pulsar 150 | Bikes & Scooters | ₹699 | Petrol • 5-Speed Manual | MCWG (Geared) |
| `VEH-105` | Ola S1 Electric | Electric | ₹549 | Electric • Direct Drive | MCWG / Gearless |
| `VEH-106` | Maruti Suzuki Alto K10 | Basic Cars | ₹999 | Petrol • 5-Speed Manual | LMV (4-Wheeler) |
| `VEH-107` | Maruti Suzuki Wagon R | Basic Cars | ₹1,199 | Petrol • AMT Automatic | LMV (4-Wheeler) |
| `VEH-108` | Tata Tiago XT | Basic Cars | ₹1,299 | Petrol • 5-Speed Manual | LMV (4-Wheeler) |
| `VEH-109` | Maruti Suzuki Swift VXi | Basic Cars | ₹1,499 | Petrol • AMT Automatic | LMV (4-Wheeler) |
| `VEH-110` | Maruti Suzuki Dzire ZXi | Sedan | ₹1,799 | Petrol • AMT Automatic | LMV (4-Wheeler) |
| `VEH-111` | Tata Nexon EV Max | Electric | ₹2,499 | Electric • Automatic | LMV (4-Wheeler) |
| `VEH-112` | Mahindra XUV700 AX7 | SUV | ₹3,499 | Diesel • 6-AT Automatic | LMV (4-Wheeler) |

---

## Getting Started

1. **No Build Tools Required:** Download or clone the repository containing `index.html`.  
2. **Launch:** Open `index.html` directly in any modern web browser (Chrome, Edge, Firefox, Safari) or serve it via any static server (e.g., VS Code Live Server, `python3 -m http.server`).  
3. **Offline Resilience:** Includes built-in CSS structural fallbacks, SVG vehicle image placeholders, and an in-memory storage fallback if browser `localStorage` is restricted.

---

## Tech Stack

* **Frontend:** Semantic HTML5, Vanilla JavaScript (ES6+), Tailwind CSS (CDN with embedded offline CSS fallback)  
* **Persistence:** Browser `localStorage` API with in-memory state fallback  
* **Printing:** Native CSS `@media print` stylesheet for clean GST invoice generation
