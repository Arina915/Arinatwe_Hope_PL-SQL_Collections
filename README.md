#  Project Overview

This repository demonstrates comprehensive implementation of **PL/SQL Collections, Records, and GOTO statements** using a real-world **Hotel Reservation Management System**.

**Course:** Database Development with PL/SQL (INSY 8311)  
**Instructor:** Eric Maniraguha  
**Institution:** Adventist University of Central Africa (AUCA)  
**Date:** 25 November 2025

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

```Hotel_PL_SQL_Project/
│
├── sql_scripts/                         # Executable SQL & PL/SQL files
│   ├── 01_create_tables.sql             # Database schema & sample data
│   ├── 02_associative_arrays.sql        # Associative array demo (room rates)
│   ├── 03_varray_demo.sql               # VARRAY demo (fixed services list)
│   ├── 04_nested_table_demo.sql         # Nested table demo (reservations)
│   ├── 05_user_defined_records.sql      # User-defined hotel reservation record
│   ├── 06_table_based_records.sql       # %ROWTYPE demonstration
│   └── 07_comprehensive_demo.sql        # Full integration of all features
│
├── documentation/
│   └── Hotel_PL_SQL_Complete_Documentation.docx
│
└── screenshots/
    └── [Outputs from all script demonstrations]
```

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
# plsql_collections_records_goto_hotel_package.sql
`-- plsql_types_and_package.sql
CREATE OR REPLACE PACKAGE hotel_pkg AS
    -- Record types
    TYPE t_reservation_rec IS RECORD (
        reservation_id NUMBER,
        guest_id NUMBER,
        room_id NUMBER,
        checkin_date DATE,
        checkout_date DATE,
        total_amount NUMBER,
        status VARCHAR2(20)
    );

    -- Collection types (nested table) for bulk operations
    TYPE t_reservation_table IS TABLE OF t_reservation_rec;

    -- Simple table of numbers (e.g. room ids)
    TYPE t_num_table IS TABLE OF NUMBER;

    -- Procedures
    PROCEDURE bulk_create_reservations(p_res_list IN t_reservation_table);
    PROCEDURE calculate_reservation_total(p_res_id IN NUMBER);
    PROCEDURE demo_goto_example;
END hotel_pkg;
/

CREATE OR REPLACE PACKAGE BODY hotel_pkg AS

    PROCEDURE bulk_create_reservations(p_res_list IN t_reservation_table) IS
    BEGIN
        -- Use FORALL with collections for performance
        FOR i IN 1 .. p_res_list.COUNT LOOP
            INSERT INTO reservation (
                reservation_id, 
                guest_id, 
                room_id, 
                check_in_date, 
                check_out_date, 
                total_amount, 
                status
            ) VALUES (
                seq_reservation_service.NEXTVAL,  -- Using existing sequence
                p_res_list(i).guest_id,
                p_res_list(i).room_id,
                p_res_list(i).checkin_date,
                p_res_list(i).checkout_date,
                NVL(p_res_list(i).total_amount, 0),
                NVL(p_res_list(i).status, 'CONFIRMED')  -- Changed to match table default
            );
        END LOOP;
        COMMIT;
    EXCEPTION
        WHEN OTHERS THEN
            ROLLBACK;
            RAISE;  -- Re-raise the exception to notify caller
    END bulk_create_reservations;

    PROCEDURE calculate_reservation_total(p_res_id IN NUMBER) IS
        v_room_rate NUMBER;
        v_nights NUMBER;
        v_total NUMBER := 0;
    BEGIN
        -- Calculate number of nights
        SELECT (r.check_out_date - r.check_in_date) 
        INTO v_nights
        FROM reservation r
        WHERE r.reservation_id = p_res_id;
        
        -- Get room rate
        SELECT rr.price
        INTO v_room_rate
        FROM room_rate rr
        JOIN reservation r ON rr.room_type = (
            SELECT room_type FROM room WHERE room_id = r.room_id
        )
        WHERE r.reservation_id = p_res_id
        AND rr.rate_date = TRUNC(SYSDATE);
        
        -- Calculate base room cost
        v_total := v_nights * v_room_rate;
        
        -- Add service costs
        FOR service_rec IN (
            SELECT rs.quantity * s.price as service_cost
            FROM reservation_service rs
            JOIN service s ON rs.service_id = s.service_id
            WHERE rs.reservation_id = p_res_id
        ) LOOP
            v_total := v_total + service_rec.service_cost;
        END LOOP;
        
        -- Update reservation total
        UPDATE reservation 
        SET total_amount = v_total
        WHERE reservation_id = p_res_id;
        
        COMMIT;
        
    EXCEPTION
        WHEN NO_DATA_FOUND THEN
            DBMS_OUTPUT.PUT_LINE('Reservation not found or missing data');
        WHEN OTHERS THEN
            ROLLBACK;
            RAISE;
    END calculate_reservation_total;

    PROCEDURE demo_goto_example IS
        v_counter NUMBER := 0;
    BEGIN
        <<start_point>>
        v_counter := v_counter + 1;
        
        IF v_counter < 5 THEN
            DBMS_OUTPUT.PUT_LINE('Counter: ' || v_counter);
            GOTO start_point;
        END IF;
        
        DBMS_OUTPUT.PUT_LINE('GOTO demo completed');
    END demo_goto_example;

END hotel_pkg;
/`


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
    

* Verification Steps: 
✔ Execute each script  
✔ Compare expected vs actual output  
✔ Validate nightly rate calculations  
✔ Validate reservation totals  
✔ Trace GOTO paths using `DBMS_OUTPUT`

Reference:
Oracle Corporation. (2024). PL/SQL Collections and Records. Oracle Database 21c Documentation.
https://docs.oracle.com/en/database/oracle/oracle-database/21/lnpls/plsql-collections-and-records.html

Feuerstein, S., & Pellerite, B. (2023). Oracle PL/SQL Programming (7th ed.). O’Reilly Media. (Chapters 9–10, 20)

-----

Arinatwe Hope_28313
