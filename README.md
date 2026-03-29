<div align="center">

<img src="https://img.shields.io/badge/SQL%20Server-2019%2B-CC2927?style=for-the-badge&logo=microsoftsqlserver&logoColor=white"/>
<img src="https://img.shields.io/badge/T--SQL-Complete-0078D4?style=for-the-badge&logo=microsoftsqlserver&logoColor=white"/>
<img src="https://img.shields.io/badge/Lines-1%2C279-6366f1?style=for-the-badge"/>
<img src="https://img.shields.io/badge/Errors-Zero-22c55e?style=for-the-badge"/>
<img src="https://img.shields.io/badge/SSMS-22%20Compatible-f59e0b?style=for-the-badge"/>

<br/><br/>

# 🗄️ RideSharingDB System

### Complete SQL Server database for a ride-sharing service
### with full lifecycle management, automation, and data integrity

<br/>

**[🌐 View Interactive Dashboard →](https://naremanukiian.github.io/RideShareDBWeb)**&nbsp;&nbsp;&nbsp;|&nbsp;&nbsp;&nbsp;**[💻 Dashboard Repository →](https://github.com/naremanukiian/RideShareDBWeb)**

<br/>

> The live dashboard lets you explore all data from this SQL script through four role-based views —
> Passenger, Driver, Analyst, and DBA — running entirely in the browser. No installation needed.

</div>

---

## 📋 Table of Contents

- [Overview](#overview)
- [Quick Start](#quick-start)
- [Project Report & Documentation](#project-report--documentation)
- [Database Schema](#database-schema)
- [What's Inside the Script](#whats-inside-the-script)
- [DDL — Tables](#section-1-ddl--tables)
- [DML — Sample Data](#section-2-dml--sample-data)
- [Indexes](#section-3-indexes)
- [Views](#section-4-views)
- [Triggers](#section-5-triggers)
- [Stored Procedures](#section-6-stored-procedures)
- [DQL — Relational Algebra Queries](#section-7-dql--relational-algebra-queries)
- [DCL — Access Control](#section-8-dcl--access-control)
- [Live Dashboard](#live-dashboard)

---

## Overview

`ride_sharing.sql` is a single, self-contained, idempotent SQL Server script that builds and populates a complete ride-sharing database from scratch. It runs in one execution — no manual steps, no partial runs.

```
1,279 lines  ·  8 sections  ·  zero errors  ·  SQL Server 2019+  ·  SSMS 22
```

**What it builds:**

| Component | Count |
|-----------|-------|
| Tables | 8 |
| Rows inserted | 336 total (42 per table, 40 for ratings) |
| Foreign Key constraints | 9 |
| CHECK constraints | 12 |
| UNIQUE constraints | 6 |
| DEFAULT values | 8 |
| Non-clustered Indexes | 15 |
| Views | 8 |
| Triggers | 7 |
| Stored Procedures | 8 |
| DQL Queries | 30 |
| DCL User Roles | 3 |

---

## Quick Start

1. Open **SQL Server Management Studio (SSMS) 22**
2. Connect to your SQL Server 2019+ instance
3. Open `ride_sharing.sql` via **File → Open → SQL Script**
4. Press **Ctrl + Shift + Enter** to execute the full script
5. Watch the **Messages tab** — every section prints a confirmation

The script is **idempotent** — it drops and recreates `RideSharingDB` on every run using:

```sql
ALTER DATABASE RideSharingDB SET SINGLE_USER WITH ROLLBACK IMMEDIATE;
DROP DATABASE RideSharingDB;
```

This means it is safe to run multiple times without manual cleanup.

**Execution order:**
```
DDL → DML → Indexes → Views → Triggers → Stored Procedures → DQL → DCL
```

---

## 📄 Project Report & Documentation

A full technical report is included in this repository:

**[📥 Download project_report.docx](project_report.docx)**

The report is a complete structured document covering all four stages of the database development process:

| Section | Contents |
|---------|----------|
| **1 — Conceptual Design** | Problem description and requirements analysis · main data objects and business rules · use-case, activity, and sequence diagrams |
| **2 — Logical Design** | Entity-Relationship (ER) diagram · entities, attributes, and relationships · cardinalities and constraints · relational schema transformation · normalisation (1NF, 2NF, 3NF) · 30 relational algebra query demonstrations with formal notation |
| **3 — Physical Implementation** | Full DDL, DML, DQL, and DCL documentation · views, indexes, triggers, and stored procedures documented with code samples and explanations · database deployment description |
| **4 — Conclusions** | Key technical decisions with justifications · requirements coverage summary · project results |

**Document stats:** 1,632 paragraphs · syntax-highlighted code frames · embedded ER diagram · formal algebra expressions (σ π ⋈ γ ∪ ∩ −)

---

## Database Schema

```
┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│    users     │────<│    rides     │>────│   drivers    │
│──────────────│     │──────────────│     │──────────────│
│ UserID  PK   │     │ RideID   PK  │     │ DriverID PK  │
│ FirstName    │     │ UserID   FK  │     │ FirstName    │
│ LastName     │     │ DriverID FK  │     │ LastName     │
│ Email  UQ+CHK│     │ VehicleID FK │     │ LicenseNo UQ │
│ Phone  NULL  │     │ StartLoc FK  │     │ Rating   CHK │
│ RegDate DEF  │     │ EndLoc   FK  │     │ Status   CHK │
└──────────────┘     │ StartTime    │     └──────┬───────┘
                     │ EndTime NULL │            │ 1:1
┌──────────────┐     │ Fare     CHK │     ┌──────▼───────┐
│  locations   │────<│ Status   CHK │     │   vehicles   │
│──────────────│     │ Duration TRG │     │──────────────│
│ LocationID PK│     │ PromoID  FK? │     │ VehicleID PK │
│ Name         │     └──────┬───────┘     │ DriverID FK  │
│ City         │            │             │ PlateNo  UQ  │
└──────────────┘      ┌─────┴──────┐      │ Model        │
                      │            │      │ Year     CHK │
               ┌──────▼───┐ ┌─────▼────┐ │ Capacity CHK │
               │ payments │ │ ratings  │ └──────────────┘
               │──────────│ │──────────│
               │PaymentID │ │RatingID  │ ┌──────────────┐
               │RideID FK │ │RideID FK │ │  promocodes  │
               │Amount CHK│ │    +UQ   │ │──────────────│
               │Method CHK│ │UserRat ? │ │ PromoID  PK  │
               │Status CHK│ │DrvRat NN │ │ Code     UQ  │
               └──────────┘ │Comment ? │ │ Discount CHK │
                            └──────────┘ │ ExpiryDate   │
                                         └──────────────┘
```

**Legend:** `PK` = Primary Key · `FK` = Foreign Key · `UQ` = Unique · `CHK` = Check constraint · `DEF` = Default · `TRG` = Derived by trigger · `?` = Nullable · `NN` = Not Null

---

## What's Inside the Script

---

## Section 1: DDL — Tables

Eight tables with full constraint coverage. All primary keys use `IDENTITY(1,1)` — no natural key risks.

| Table | PK | Key Constraints |
|-------|-----|----------------|
| `users` | UserID | UNIQUE(Email), CHECK(Email LIKE '%@%.%'), DEFAULT GETDATE() |
| `drivers` | DriverID | UNIQUE(LicenseNumber), CHECK(Rating BETWEEN 0 AND 5), CHECK(Status IN enum) |
| `vehicles` | VehicleID | FK→drivers CASCADE, UNIQUE(PlateNumber), CHECK(Year 1990–2030), CHECK(Capacity 1–20) |
| `locations` | LocationID | — |
| `promocodes` | PromoID | UNIQUE(Code), CHECK(Discount BETWEEN 0 AND 100) |
| `rides` | RideID | 6 FK constraints, CHECK(Status enum), CHECK(Fare >= 0) |
| `payments` | PaymentID | FK→rides CASCADE, CHECK(Method enum), CHECK(Status enum), CHECK(Amount >= 0) |
| `ratings` | RatingID | FK→rides CASCADE, UNIQUE(RideID), CHECK(DriverRating 1–5) |

The `rides` table is the central fact table connecting all other entities through 6 foreign keys.

---

## Section 2: DML — Sample Data

336 realistic records across all 8 tables — enough to demonstrate every query, trigger, and view meaningfully.

| Table | Rows | Coverage |
|-------|------|----------|
| `users` | 42 | Jan–Nov 2023 registrations · mix of phone NULL and filled |
| `drivers` | 42 | Available / Busy / Offline statuses · ratings 4.1–4.9 |
| `vehicles` | 42 | Sedans, SUVs, Vans, EVs · 2018–2022 · 4–8 seat capacity |
| `locations` | 42 | 10 locations each in New York, Chicago, Los Angeles, San Francisco |
| `promocodes` | 42 | Discounts 5–50% · mix of active and expired for realistic testing |
| `rides` | 42 | **40 Completed · 1 Pending · 1 Cancelled** · fares $8.50–$40.00 |
| `payments` | 42 | **40 Paid · 1 Pending · 1 Failed** · Cash / Card / Online |
| `ratings` | 40 | Completed rides only · DriverRating mandatory · UserRating optional |

**Key stats from the data:**
- Total revenue: **$940.75**
- Average fare: **$23.52**
- Average ride duration: **31.4 minutes**
- Cities covered: New York · Chicago · Los Angeles · San Francisco

---

## Section 3: Indexes

15 non-clustered indexes placed on the highest-traffic columns to optimise the most common query patterns.

| Index | Table | Column(s) | Optimises |
|-------|-------|-----------|-----------|
| `idx_rides_user` | rides | UserID | Passenger ride history lookup |
| `idx_rides_driver` | rides | DriverID | Driver dashboard and earnings |
| `idx_rides_status` | rides | Status | Filter by Pending / Completed / Cancelled |
| `idx_rides_starttime` | rides | StartTime | Date-range and monthly revenue reports |
| `idx_rides_drv_status` | rides | DriverID, Status | **Composite** — driver-specific dispatch queries |
| `idx_rides_fare` | rides | Fare | Revenue aggregation and fare distribution |
| `idx_rides_startloc` | rides | StartLocationID | City-level revenue grouping |
| `idx_rides_endloc` | rides | EndLocationID | Destination frequency analysis |
| `idx_payments_status` | payments | Status | Filter Paid / Pending / Failed |
| `idx_payments_ride` | payments | RideID | JOIN payments to rides |
| `idx_ratings_ride` | ratings | RideID | JOIN ratings to rides for average calculation |
| `idx_vehicles_driver` | vehicles | DriverID | Find a driver's vehicle instantly |
| `idx_drivers_status` | drivers | Status | Dispatch — find all Available drivers |
| `idx_promo_expiry` | promocodes | ExpiryDate | Real-time promo code validation |
| `idx_locations_city` | locations | City | Filter locations by city |

---

## Section 4: Views

8 pre-built views that resolve all FK references into human-readable names, ready for reporting and application queries.

| View | Purpose | Tables Joined |
|------|---------|---------------|
| `vw_ride_details` | Full ride info — all IDs resolved to names | rides + users + drivers + vehicles + locations (×2) + promocodes |
| `vw_driver_summary` | Per-driver KPIs aggregated over completed rides | drivers + rides + ratings |
| `vw_user_activity` | Per-user spending and rating summary | users + rides + ratings |
| `vw_revenue_by_city` | City-level revenue analytics | rides + locations |
| `vw_payment_overview` | Payment ledger with passenger names | payments + rides + users |
| `vw_active_promos` | Non-expired promo codes only | promocodes (filtered by ExpiryDate > GETDATE()) |
| `vw_top_drivers` | Drivers with Rating ≥ 4.5 | drivers (filtered) |
| `vw_pending_rides` | Active rides awaiting completion | rides + users + drivers |

**Sample usage:**
```sql
SELECT * FROM vw_revenue_by_city ORDER BY TotalRevenue DESC;
SELECT * FROM vw_top_drivers     WHERE Status = 'Available';
SELECT * FROM vw_ride_details    WHERE City = 'New York';
```

---

## Section 5: Triggers

7 triggers enforce 7 distinct business rules at the SQL Server engine level — independently of application code. Rules fire even if the database is accessed directly via SSMS.

| Trigger | Table | Type | Business Rule |
|---------|-------|------|---------------|
| `trg_calc_duration` | rides | AFTER UPDATE | Sets `RideDuration = DATEDIFF(MINUTE, StartTime, EndTime)` when EndTime is written. Keeps derived column accurate automatically. |
| `trg_update_driver_rating` | ratings | AFTER INSERT | Recalculates `driver.Rating = ROUND(AVG(DriverRating), 2)` across all that driver's rides after every new rating. |
| `trg_driver_busy_on_ride` | rides | AFTER INSERT | Sets `driver.Status = 'Busy'` when a new Pending ride is inserted. |
| `trg_driver_available_on_complete` | rides | AFTER UPDATE | Resets `driver.Status = 'Available'` when a ride transitions Pending → Completed or Cancelled. |
| `trg_no_concurrent_rides` | rides | **INSTEAD OF INSERT** | `RAISERROR` + `RETURN` if the user already has a Pending ride. Blocks the INSERT before the storage engine writes anything. |
| `trg_validate_payment_ride` | payments | **INSTEAD OF INSERT** | `RAISERROR` + `RETURN` if the associated ride is Cancelled. Prevents financial records on void rides. |
| `trg_prevent_delete_completed` | rides | AFTER DELETE | `RAISERROR` + `ROLLBACK TRANSACTION` if any Completed ride appears in the deleted set. Protects audit trail. |

**Why INSTEAD OF for BR-1 and BR-3?**
AFTER triggers fire after the row is already written — they cannot prevent an INSERT. INSTEAD OF fires before the storage engine writes anything, allowing clean rejection with zero side effects.

**Why AFTER DELETE + ROLLBACK for BR-2?**
SQL Server prohibits `INSTEAD OF DELETE` on tables with cascading FK children. `AFTER DELETE + ROLLBACK TRANSACTION` achieves the same result — the DELETE and all its cascades are rolled back atomically.

---

## Section 6: Stored Procedures

8 parameterised stored procedures covering all major application operations. Each uses `SET NOCOUNT ON` and proper T-SQL error handling.

| Procedure | Parameters | Returns | Purpose |
|-----------|------------|---------|---------|
| `sp_get_user_rides` | `@UserID INT` | Result set | All rides for a user via `vw_ride_details` — names already resolved |
| `sp_available_drivers` | `@City VARCHAR(50) = NULL` | Result set | Available drivers with vehicle details — ready for dispatch |
| `sp_complete_ride` | `@RideID INT, @EndTime DATETIME, @Fare FLOAT` | Confirmation | Marks ride Completed — fires `trg_calc_duration` and `trg_driver_available` |
| `sp_apply_promo` | `@RideID INT, @PromoID INT, @NewFare FLOAT OUTPUT` | OUTPUT param | Validates expiry, computes `Fare × (1 − Discount/100)`, updates ride |
| `sp_monthly_revenue` | `@Year INT, @Month INT` | Aggregate row | TotalRides, TotalRevenue, AvgFare, MinFare, MaxFare for the month |
| `sp_driver_earnings` | `@DriverID INT, @StartDate DATE, @EndDate DATE` | Aggregate row | Total completed rides and earnings in a date range |
| `sp_register_user` | `@First, @Last VARCHAR, @Email, @Phone` | NewUserID | Inserts user, returns `SCOPE_IDENTITY()` — safe for concurrent inserts |
| `sp_cancel_ride` | `@RideID INT` | RowsUpdated | Cancels a Pending ride — fires `trg_driver_available`. Returns 0 if not found |

**Sample calls:**
```sql
EXEC sp_get_user_rides      @UserID = 1;
EXEC sp_monthly_revenue     @Year = 2024, @Month = 1;
EXEC sp_available_drivers   @City = 'New York';

DECLARE @newFare FLOAT;
EXEC sp_apply_promo @RideID = 41, @PromoID = 1, @NewFare = @newFare OUTPUT;
SELECT @newFare AS DiscountedFare;

EXEC sp_driver_earnings @DriverID = 1, @StartDate = '2024-01-01', @EndDate = '2024-12-31';
```

---

## Section 7: DQL — Relational Algebra Queries

30 SELECT queries demonstrating all 7 relational algebra operations, each labelled with its formal algebra expression in the PRINT statement before it.

| Operation | Symbol | Queries | What They Demonstrate |
|-----------|--------|---------|----------------------|
| Selection | σ | Q1–Q7 | Filter rows by condition (status, fare, date, method) |
| Projection | π | Q8–Q10 | Return specific columns only |
| Natural Join | ⋈ | Q11–Q15 | Combine related tables via FK (including self-join on locations) |
| Aggregation | γ | Q16–Q20 | GROUP BY with SUM, AVG, COUNT, TOP 5 |
| Union | ∪ | Q21 | All people (users UNION drivers) |
| Intersection | ∩ | Q22 | First names shared between users and drivers (INTERSECT) |
| Difference | − | Q23–Q24 | Users with no rides · Drivers with no completed ride (NOT IN) |
| Subqueries | nested | Q25–Q30 | Above-average fare · Max rating · HAVING · nested aggregation |

---

## Section 8: DCL — Access Control

3 database logins following the **principle of least privilege** — each role can only do exactly what its use case requires.

```sql
-- ── Application user: read/write, no delete ──────────────────
CREATE LOGIN ride_app    WITH PASSWORD = 'App@Secure123!';
CREATE USER  ride_app    FOR LOGIN ride_app;
GRANT SELECT, INSERT, UPDATE ON SCHEMA::dbo TO ride_app;
DENY  DELETE               ON SCHEMA::dbo TO ride_app;

-- ── Report user: read only ────────────────────────────────────
CREATE LOGIN ride_report WITH PASSWORD = 'Report@Secure123!';
CREATE USER  ride_report FOR LOGIN ride_report;
GRANT SELECT ON SCHEMA::dbo TO ride_report;

-- ── DBA: full control ─────────────────────────────────────────
CREATE LOGIN ride_dba    WITH PASSWORD = 'DBA@Secure123!';
CREATE USER  ride_dba    FOR LOGIN ride_dba;
ALTER ROLE db_owner ADD MEMBER ride_dba;
```

| Login | Role | SELECT | INSERT | UPDATE | DELETE | Use Case |
|-------|------|--------|--------|--------|--------|----------|
| `ride_app` | App user | ✅ | ✅ | ✅ | ❌ DENY | Backend API — no accidental mass deletions |
| `ride_report` | Read-only | ✅ | ❌ | ❌ | ❌ | BI dashboards, analytics, exports |
| `ride_dba` | db_owner | ✅ | ✅ | ✅ | ✅ | Schema changes, maintenance, deployment |

All `CREATE LOGIN` and `CREATE USER` statements use `IF NOT EXISTS` guards — safe to run the script multiple times without errors.

**Verify permissions in SSMS:**
```sql
SELECT dp.name AS LoginName, p.permission_name, p.state_desc
FROM   sys.database_permissions p
JOIN   sys.database_principals  dp ON p.grantee_principal_id = dp.principal_id
WHERE  dp.name IN ('ride_app', 'ride_report', 'ride_dba')
ORDER  BY dp.name, p.permission_name;
```

---

## Live Dashboard

All data from this SQL script is available interactively at:

**[https://naremanukiian.github.io/RideShareDBWeb](https://naremanukiian.github.io/RideShareDBWeb)**

The dashboard runs entirely in the browser. Select one of four role-based views — Passenger, Driver, Analyst, or DBA — to explore the database from that login's perspective. No SQL Server connection needed.

**Dashboard repository:** [naremanukiian/RideShareDBWeb](https://github.com/naremanukiian/RideShareDBWeb)

---

<div align="center">

*SQL Server 2019+ · T-SQL · SSMS 22 · Full 3NF · 1,279 lines · Zero errors*

</div>
