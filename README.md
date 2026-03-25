#  Telecom Data Warehouse ETL (SSIS)

##  Overview

This project implements an **ETL pipeline using SQL Server Integration Services (SSIS)** for a telecom company.
It processes CSV files generated every 5 minutes, applies data validation and transformation rules, and loads the results into a data warehouse.

---

##  Architecture

**Flow:**

1. CSV files are generated every 5 minutes
2. SSIS package reads files from a folder
3. Data is validated and transformed
4. Valid records → loaded into fact table
5. Invalid records → stored in error table
6. Processed files → moved to archive folder

---

##  Project Structure

```
📁 Telecom_ETL_Project
│
├── 📁 data                # Incoming CSV files
├── 📁 archive             # Processed files
├── 📁 ssis_package        # SSIS project files
├── 📁 sql                 # Database scripts
└── README.md
```

---

##  Database Design

###  Fact Table: `fact_transaction`

Stores valid processed transactions.

| Column         | Description             |
| -------------- | ----------------------- |
| transaction_id | Original transaction ID |
| imsi           | Subscriber IMSI         |
| subscriber_id  | From reference table    |
| tac            | First 8 digits of IMEI  |
| snr            | Last 6 digits of IMEI   |
| imei           | Device IMEI             |
| cell           | Cell ID                 |
| lac            | Location Area Code      |
| event_type     | Event type              |
| event_ts       | Event timestamp         |

---

###  Error Table: `error_destination_output`

Stores rejected records with error details.

---

###  Dimension Table: `dim_imsi_reference`

Maps IMSI → subscriber_id

---

##  ETL Logic

###  Validation Rules

* Reject if:

  * IMSI is NULL
  * CELL is NULL
  * LAC is NULL
  * EVENT_TS is NULL or invalid

---

###  Transformations

* **IMSI → subscriber_id**

  * Lookup from reference table
  * If not found → `-99999`

* **IMEI Processing**

  * TAC = first 8 digits
  * SNR = last 6 digits
  * If invalid → `-99999`

---

## 🔁 SSIS Components Used

* Foreach Loop Container
* Flat File Source
* Conditional Split
* Derived Column
* Lookup Transformation
* OLE DB Destination
* File System Task

---

## ⏱️ Scheduling

The package is designed to run:

* Every 5 minutes using **SQL Server Agent**

---

##  How to Run

1. Create database using SQL scripts in `/sql`
2. Open SSIS project in Visual Studio
3. Configure file paths (CSV + Archive)
4. Execute the package OR schedule via SQL Server Agent

---

---

##  Notes

* Files are moved to archive after processing to avoid duplication
* Invalid records are logged for auditing
* Designed for scalability and automation

---

##  Future Improvements

* Add logging & monitoring
* Implement incremental loading
* Add real-time ingestion
* Integrate with Airflow


