# HCL_HACKATHON
HOSPITALITY 
# Hospitality ETL Pipeline – Daily Occupancy & Revenue Analysis

## Project Overview

This project implements an ETL (Extract, Transform, Load) solution for the Hospitality domain using Informatica. The objective is to process daily hotel operational data, calculate room occupancy and revenue metrics, and load the transformed data into a database for reporting and analysis.

The system reads hotel guest, room, and stay information from CSV files, performs data validation and transformation, and stores the processed data in staging and reporting tables.

---

# Business Requirement

Hotels require daily insights into:

* Number of guests checked in
* Number of guests checked out
* Rooms occupied today
* Revenue generated
* Guest stay patterns
* Room-type performance

To achieve this, daily operational data is extracted from source files and processed through an ETL workflow.

---

# Source Files

## 1. Guest_Master_Hospitality.csv

Contains guest information.

| Column Name      |
| ---------------- |
| GuestID          |
| FirstName        |
| LastName         |
| Gender           |
| DateOfBirth      |
| Email            |
| PhoneNumber      |
| AddressLine1     |
| AddressLine2     |
| City             |
| State            |
| Country          |
| IDProofType      |
| IDProofNumber    |
| RegistrationDate |

---

## 2. Checkin_Checkout_Hospitality.csv

Contains guest stay details.

| Column Name      |
| ---------------- |
| StayID           |
| GuestID          |
| RoomID           |
| CheckinDateTime  |
| CheckoutDateTime |
| BookingSource    |
| NumberOfGuests   |
| RoomRate         |
| ExtraCharges     |
| DiscountAmount   |
| TotalAmount      |
| PaymentMode      |
| Status           |
| LastUpdated      |

---

## 3. Room_Master_Hospitality.csv

Contains room information.

| Column Name     |
| --------------- |
| RoomID          |
| RoomType        |
| FloorNumber     |
| BedType         |
| BaseRate        |
| RoomStatus      |
| MaxOccupancy    |
| Amenities       |
| LastCleanedDate |

---

# Technology Stack

* Informatica Intelligent Cloud Services (IICS)
* Oracle Database
* SQL
* CSV Files
* Stored Procedures
* Database Triggers

---

# ETL Workflow

## Step 1: Data Extraction

Source files are imported into Informatica.

### Extraction Logic

Process only newly added or modified records:

```sql
WHERE LastUpdated > $Last_Run_Time
```

---

## Step 2: Data Transformation

### Guest Lookup

Fetch guest details from Guest_Master using:

```sql
GuestID
```

### Room Lookup

Fetch room details from Room_Master using:

```sql
RoomID
```

### Stay Duration Calculation

```sql
Stay_Duration =
TRUNC(CheckoutDateTime) - TRUNC(CheckinDateTime)
```

### Total Revenue Calculation

```sql
Calculated_Total_Amount =
(RoomRate * Stay_Duration)
+ ExtraCharges
- DiscountAmount
```

### Data Validation

Validate:

* GuestID is not null
* RoomID is not null
* Check-in date exists
* Room Rate > 0

Invalid records are redirected to an error table.

---

## Step 3: Data Loading

Load transformed records into:

### GUEST_STAY Table

| Column           |
| ---------------- |
| StayID           |
| GuestID          |
| RoomID           |
| GuestName        |
| RoomType         |
| CheckinDateTime  |
| CheckoutDateTime |
| StayDuration     |
| RoomRate         |
| ExtraCharges     |
| DiscountAmount   |
| TotalAmount      |
| Status           |
| LastUpdated      |

---

# Database Objects

## Audit Table

Stores all insert and update activities.

### STAY_AUDIT_LOG

| Column       |
| ------------ |
| AuditID      |
| StayID       |
| ActionType   |
| OldValue     |
| NewValue     |
| ModifiedDate |

---

## Trigger

Purpose:

* Capture inserts
* Capture updates
* Maintain audit history

Trigger fires whenever data is inserted or updated in GUEST_STAY.

---

## Stored Procedure

### PROC_DAILY_OCCUPANCY_REVENUE

Calculates:

* Total Rooms Available
* Rooms Occupied Today
* Occupancy Percentage
* Total Revenue
* Average Revenue Per Occupied Room

---

# Error Handling

The ETL process includes:

* Null value validation
* Invalid RoomID detection
* Invalid GuestID detection
* Duplicate StayID handling
* Error logging table

Rejected records are stored separately for review.

---

# Stretch Features

### Email Notification

Daily email summary containing:

* Total records processed
* Successful records
* Failed records
* Revenue generated
* Occupancy rate

### Audit Notification

Send alerts whenever stay records are modified.

---

# Analytical Use Cases

## Use Case 1: Daily Occupancy & Revenue Summary

### KPIs

* Total Rooms Available
* Rooms Occupied Today
* Occupancy Rate (%)
* Total Revenue Generated
* Average Revenue Per Occupied Room

---

## Use Case 2: Room-Type Wise Performance Analysis

### KPIs

* Total Bookings by Room Type
* Occupancy Rate by Room Type
* Revenue by Room Type
* Average Daily Rate (ADR)

---

## Use Case 3: Guest Stay Duration Analysis

### KPIs

* Average Length of Stay
* Minimum Stay Duration
* Maximum Stay Duration
* Total Guest Nights

---

## Use Case 4: High-Value Guest Identification

### KPIs

* Total Revenue per Guest
* Number of Visits
* Average Spend per Stay

---

# Project Benefits

* Automated daily data processing
* Accurate occupancy tracking
* Revenue monitoring
* Guest behavior analysis
* Room performance insights
* Audit and compliance tracking
* Improved hotel operational reporting

---

# Expected Outcome

The solution provides hotel management with a reliable daily reporting system that tracks occupancy, revenue, guest activity, and room performance through an automated Informatica ETL pipeline and SQL-based analytics framework.
