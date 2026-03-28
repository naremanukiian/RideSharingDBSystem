# 🚗 Ride-Sharing Service Database (SQL Server)

![SQL Server](https://img.shields.io/badge/SQL%20Server-T--SQL-blue)
![Database](https://img.shields.io/badge/Database-Relational-green)
![Status](https://img.shields.io/badge/Status-Complete-brightgreen)
![Project Type](https://img.shields.io/badge/Project-Academic-orange)

---

## 📌 Overview
This project implements a **complete backend database system** for a ride-sharing application (similar to Uber or Bolt) using **Microsoft SQL Server (T-SQL)**.

The system manages the **entire lifecycle of a ride**, from user registration to post-ride feedback, ensuring **data integrity, automation, and reliability** at the database level.

---

## 📱 System Description
The database is designed for a ride-sharing **mobile or web application**, handling:

- User registration  
- Ride requests  
- Driver and vehicle assignment  
- Fare calculation  
- Payment processing  
- Ratings and feedback  

All operations are enforced directly in the database to guarantee **consistency, accuracy, and integrity**, even if the application layer is bypassed.

---

## 🎯 Business Problem
The system addresses the challenge of:

> Matching **real-time demand (users)** with **supply (drivers & vehicles)** while ensuring:
- Valid ride state transitions  
- Accurate financial records  
- Automatic enforcement of business rules  

This is achieved using **constraints, triggers, and stored procedures**.

---

## 🧱 Database Structure

### 📂 Tables (8 total)
| Table | Description |
|------|------------|
| `users` | Registered customers |
| `drivers` | Driver profiles and availability |
| `vehicles` | Vehicles assigned to drivers |
| `locations` | Ride start/end locations |
| `promocodes` | Discount codes |
| `rides` | Core ride transactions |
| `payments` | Payment records |
| `ratings` | User feedback |

### ✅ Key Features
- Primary Keys & Foreign Keys  
- Unique constraints (email, license number, plate number)  
- Check constraints (ratings, statuses, valid ranges)  
- Cascading relationships  

---

## 📊 Data Population
- **42 records per table** (ratings: 40)  
- Includes realistic data:
  - Multiple cities  
  - Completed, pending, and cancelled rides  
  - Payments and promo code usage  

---

## ⚡ Performance Optimization
- **15 indexes** implemented  
- Improves:
  - Query performance  
  - Join efficiency  
  - Filtering and aggregation  

---

## 👁️ Views (8 total)
Predefined views for analytics and reporting:

- `vw_ride_details` – full ride information  
- `vw_driver_summary` – driver performance metrics  
- `vw_user_activity` – user spending analysis  
- `vw_revenue_by_city` – revenue insights  
- `vw_payment_overview` – payment tracking  
- `vw_active_promos` – valid promo codes  
- `vw_top_drivers` – high-rated drivers  
- `vw_pending_rides` – active rides  

---

## ⚙️ Triggers (7 total)
Automated business logic:

- Auto-calculate ride duration  
- Update driver ratings dynamically  
- Manage driver availability  
- Prevent:
  - Multiple active rides per user  
  - Payments for cancelled rides  
  - Deletion of completed rides  

---

## 🧠 Stored Procedures (8 total)
Reusable database operations:

- `sp_get_user_rides` – retrieve user ride history  
- `sp_available_drivers` – list available drivers  
- `sp_complete_ride` – complete a ride  
- `sp_apply_promo` – apply discount codes  
- `sp_monthly_revenue` – revenue statistics  
- `sp_driver_earnings` – driver income analysis  
- `sp_register_user` – register new users  
- `sp_cancel_ride` – cancel pending rides  

---

## 🔍 Queries (DQL)
Includes **30 advanced SQL queries** demonstrating:

- Selection (σ)  
- Projection (π)  
- Joins (⋈)  
- Aggregation (γ)  
- Union, Intersection, Difference  
- Subqueries  

---

## 🔐 Security (DCL)

| Role | Permissions |
|------|------------|
| `ride_app` | SELECT, INSERT, UPDATE |
| `ride_report` | Read-only access |
| `ride_dba` | Full access (db_owner) |

---

## ▶️ How to Run

1. Open **SQL Server Management Studio (SSMS)**  
2. Copy the SQL script  
3. Execute the script (`F5`)  

```sql
-- Example
USE master;
GO
-- Paste full script here
```

✔️ The script will:

* Create the database
* Insert all data
* Build indexes, views, triggers, and procedures
* Execute verification queries

---
## 📈 Final Summary

| Component | Count |
|----------|------|
| Tables | 8 |
| Rows | 42 per table (ratings: 40) |
| Indexes | 15 |
| Views | 8 |
| Triggers | 7 |
| Stored Procedures | 8 |
| Queries | 30 |
| User Roles | 3 |

---

## 🎯 Key Concepts Demonstrated
- Relational database design  
- Data integrity enforcement  
- Business logic implementation in SQL Server  
- Performance optimization  
- Role-based access control  
- Real-world system modeling  

---

## 📝 Notes
- Built using **T-SQL (SQL Server)**  
- Uses `GO` batch execution  
- Fully self-contained script  
- No external dependencies  
