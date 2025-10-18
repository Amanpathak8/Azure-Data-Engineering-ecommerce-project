# Azure Data Engineering Ecommerce Project

<img src="Architecture Diagram.png" alt="Architecture Diagram" />

## 🧾 Project Overview  
This repository presents an **end-to-end Azure Data Engineering project** built for an **E-commerce analytics scenario**, following the **Medallion Architecture (Bronze–Silver–Gold)** pattern.  

The goal of this project is to demonstrate how to **ingest, clean, transform, and analyze large-scale data** using **Azure cloud services**, **SQL/NoSQL databases**, and **modern data engineering principles**.

---

## ✅ Key Features  
- Implementation of the **Medallion Architecture** (Bronze → Silver → Gold)  
- Data ingestion from **CSV files to SQL (Filess.io)** and **NoSQL (Cosmos DB/MongoDB)**  
- **Azure Synapse Analytics** used for querying and reporting in the Gold layer  
- Orchestrated pipelines using **Azure Data Factory**  
- Layered storage using **Azure Data Lake (ADLS Gen2)**  
- Visual diagrams for architecture, pipeline, and service integration  

---

## 🪙 Medallion Architecture Design  

### 🥉 **Bronze Layer — Raw Data**
- Ingested raw CSV files (e.g., order, payment, product datasets)
- Stored in Azure Data Lake (Raw zone)
- No transformations applied; serves as the immutable source of truth

### 🥈 **Silver Layer — Cleaned Data**
- Data from Bronze is **validated, cleansed, and standardized**
- Transformation includes removing duplicates, handling nulls, and applying business logic
- Stored back into Azure Data Lake or SQL for structured querying

### 🥇 **Gold Layer — Aggregated / Analytics Data**
- Processed data from Silver is aggregated for analytics
- Loaded into **Azure Synapse Analytics** for dashboarding and insights
- Enables business intelligence and reporting through tools like **Power BI**

---

## 🧩 Data Ingestion to SQL & NoSQL (Using Filess.io)

The notebook [`DataIngestionToDB.ipynb`](./DataIngestionToDB.ipynb) handles **data ingestion from CSV files into MySQL (Filess.io)** and optionally into **NoSQL databases** like Cosmos DB.

### 🧠 Process Flow:
1. Connect to **MySQL server on Filess.io**
2. Read e-commerce datasets (e.g., `olist_order_payments_dataset.csv`)
3. Insert the data into **SQL tables** (Bronze layer)
4. Clean and transform data for **Silver layer ingestion**
5. Optionally, push semi-structured data into **NoSQL collections**
6. Feed aggregated results into **Azure Synapse (Gold layer)**

---

### 💻 Example SQL Ingestion Code
```python
import mysql.connector
import pandas as pd
from mysql.connector import Error

# Connection details (Filess.io)
hostname = "6bnvj.h.filess.io"
database = "olistproject_slopelack"
port = "3307"
username = "olistproject_slopelack"
password = "YOUR_PASSWORD_HERE"

try:
    connection = mysql.connector.connect(
        host=hostname,
        database=database,
        user=username,
        password=password,
        port=port
    )
    if connection.is_connected():
        print("✅ Connected to MySQL Server")

        # Load CSV data
        df = pd.read_csv("olist_order_payments_dataset.csv")

        # Push data to SQL table
        cursor = connection.cursor()
        for _, row in df.iterrows():
            cursor.execute(
                "INSERT INTO olist_order_payments VALUES (%s, %s, %s, %s, %s)",
                tuple(row)
            )
        connection.commit()
        print("✅ Data uploaded successfully!")

except Error as e:
    print("❌ Error while connecting to MySQL:", e)
finally:
    if connection.is_connected():
        connection.close()
        print("🔒 MySQL connection is closed")
