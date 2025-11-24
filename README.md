#  Project Overview

This repository demonstrates comprehensive implementation of **PL/SQL Collections, Records, and GOTO statements** using a real-world **Hotel Reservation Management System**.

**Course:** Database Development with PL/SQL (INSY 8311)  
**Instructor:** Eric Maniraguha  
**Institution:** Adventist University of Central Africa (AUCA)  
**Date:** November 2025

---

#  Learning Objectives

This project demonstrates mastery of:

## PL/SQL Collections

- **Associative Arrays** (dynamic key-value storage)
    
- **Nested Tables** (unbounded, flexible collections)
    
- **VARRAYs** (fixed-size ordered collections)
    

## PL/SQL Record Types

- **User-Defined Records**
    
- **Table-Based Records** (`%ROWTYPE`)
    

## GOTO Statements

- Sequential processing
    
- Conditional branching
    
- Loop simulation
    
- Error-handling jumps
    

---

#  Repository Structure

`Hotel_PL_SQL_Project/ │ ├── sql_scripts/                         # Executable SQL & PL/SQL files │   ├── 01_create_tables.sql             # Database schema & sample data │   ├── 02_associative_arrays.sql        # Associative array demo (room rates) │   ├── 03_varray_demo.sql               # VARRAY demo (fixed services list) │   ├── 04_nested_table_demo.sql         # Nested table demo (reservations) │   ├── 05_user_defined_records.sql      # User-defined hotel reservation record │   ├── 06_table_based_records.sql       # %ROWTYPE demonstration │   └── 07_comprehensive_demo.sql        # Full integration of all features │ ├── documentation/ │   └── Hotel_PL_SQL_Complete_Documentation.docx │ └── screenshots/     └── [Outputs from all script demonstrations]`

---

#  Quick Start Guide

## Prerequisites

- Oracle Database Express Edition 21c+
    
- SQL Developer / SQL*Plus
    
- Basic PL/SQL knowledge
    

## Installation & Execution

1. Clone this repository
    
2. Open SQL Developer and connect to your Oracle database
    
3. Enable output:
    
    `SET SERVEROUTPUT ON;`
    
4. Execute scripts **in order** (01 → 07)
    
5. Check output and capture screenshots
    

---

#  Database Schema

## **Tables**

### **guests**

|Column|Description|
|---|---|
|guest_id (PK)|Unique ID|
|first_name|Guest first name|
|last_name|Guest last name|
|phone|Telephone|
|email|Contact email|

### **rooms**

|Column|Description|
|---|---|
|room_id (PK)|Room identifier|
|room_number|Hotel room number|
|room_type|Single / Double / Suite|
|status|AVAILABLE / OCCUPIED|

### **room_rate**

|Column|Description|
|---|---|
|rate_id (PK)|Rate identifier|
|room_type|Type of room|
|rate_date|Date rate applies|
|price|Price per night|

### **reservation**

|Column|Description|
|---|---|
|reservation_id (PK)|Unique reservation ID|
|guest_id (FK)|Link to guest|
|room_id (FK)|Link to room|
|checkin_date|Check-in|
|checkout_date|Check-out|
|total_amount|Computed amount|

### **reservation_service**

|Column|Description|
|---|---|
|reservation_service_id (PK)|Unique ID|
|reservation_id (FK)|Reservation|
|service_id (FK)|Additional services|
|qty|Quantity|

---

#  Key Demonstrations

## **1. Associative Arrays (Script 02)**

**Purpose:** Store & retrieve dynamic room rates indexed by room type  
**Demonstrates:**

- Key-value storage
    
- Dynamic growth
    
- Lookup operations
    
- GOTO warning system
    

**Output:**

- Room type → Rate listing
    
- Alerts for missing rate entries
    

---

## **2. VARRAYs (Script 03)**

**Purpose:** Store fixed hotel service lists (max size known)  
**Demonstrates:**

- Ordered fixed-size collection
    
- Price calculations
    
- Reservation service summaries
    

---

## **3. Nested Tables (Script 04)**

**Purpose:** Manage unlimited customer reservations  
**Demonstrates:**

- Unbounded size
    
- DELETE operations
    
- EXISTS checks
    
- Flexible indexing
    

---

## **4. User-Defined Records (Script 05)**

**Purpose:** Assemble reservation summaries  
**Demonstrates:**

- Multi-field aggregation
    
- Custom structure
    
- Rate + nights + services total calculation
    

---

## **5. Table-Based Records (Script 06)**

**Purpose:** Load full rows using `%ROWTYPE`  
**Benefits:**

- Safe, schema-matching structure
    
- Auto field binding
    

---

## **6. Comprehensive Integration (Script 07)**

**Purpose:** Combine all PL/SQL concepts  
**Includes:**

- Collections (all 3 types)
    
- Records
    
- GOTO flow orchestration
    
- Reservation analytics
    

**Output:**

- Full hotel performance summary
    
- Total bookings
    
- Occupancy percentages
    
- Revenue calculations
    

---

#  GOTO Statement Patterns Used

### **Pattern 1: Sequential Processing**

`GOTO step1 GOTO step2 GOTO end_process`

### **Pattern 2: Conditional Jump**

`IF missing_rate THEN GOTO error_block;`

### **Pattern 3: Loop Simulation**

`<<loop_start>>   process;   IF more_items THEN GOTO loop_start; <<loop_end>>`

### **Pattern 4: Error Handling**

`IF invalid_data THEN GOTO handle_error;`

---

#  Sample Output

### **Associative Array Demo**

`=== ROOM RATE LOOKUP === Loading room rates... Single  - $30 Double  - $50 Suite   - NOT FOUND ⚠`

### **Comprehensive Demo**

`╔═════════════════════════════════════════════╗ ║      HOTEL RESERVATION SUMMARY REPORT       ║ ╚═════════════════════════════════════════════╝  Reservation #1:   Guest: Alice Kamanzi   Room: 101 (Single)   Nights: 2   Total: $65.00  ═══ HOTEL STATISTICS ═══ Total Reservations: 5 Total Revenue: $350.00 Occupancy Rate: 72%`

---

#  Testing

All demonstrations tested under:

- Oracle Database 21c XE
    
- Sample dataset (5 guests, 6 rooms, 8 reservations)
    
- Multi-path execution
    
- Error simulation for GOTO testing
    

**Verification Steps:**  
✔ Execute each script  
✔ Compare expected vs actual output  
✔ Validate nightly rate calculations  
✔ Validate reservation totals  
✔ Trace GOTO paths using `DBMS_OUTPUT`
