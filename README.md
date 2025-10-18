# Azure Data Engineering Ecommerce Project

<img src="Architecture Diagram.png" alt="Architecture Diagram" />

## 🧾 Project Overview  
This repository showcases a **real-world Azure Data Engineering project** for an **E-commerce analytics use case**, implemented using the **Medallion Architecture (Bronze–Silver–Gold)**.  

It demonstrates how to **ingest, clean, transform, and analyze data** from multiple sources — **SQL (Filess.io)**, **MongoDB**, and **GitHub (via Azure Data Factory)** — using modern Azure services for scalable and reliable data engineering.

---

## ✅ Key Highlights  
- Built on **Medallion Architecture** (Bronze → Silver → Gold)  
- **Multiple data sources**:
  - 1 dataset in **SQL Database (Filess.io)**
  - 1 dataset in **MongoDB**
  - 7 datasets pulled via **HTTP from GitHub** into Azure  
- Ingestion, transformation, and orchestration with **Azure Data Factory**  
- Layered data storage in **Azure Data Lake (ADLS Gen2)** containers  
- Analytical layer powered by **Azure Synapse Analytics**  
- Visual diagrams of architecture, layers, and pipelines  

---

## 🪙 Medallion Architecture Implementation  

### 🥉 **Bronze Layer — Raw Data**
- Ingested raw files from multiple sources:
  - 1 file from **SQL (Filess.io)**
  - 1 file from **MongoDB**
  - 7 files from **GitHub HTTP connector (Azure Data Factory)**
- Stored these files inside a **Bronze container** in **Azure Data Lake Storage**
- Raw and unprocessed data — acts as a single landing zone

### 🥈 **Silver Layer — Cleaned Data**
- Data from Bronze is **transformed and cleaned**
- Performed operations like:
  - Removing duplicates  
  - Handling missing values  
  - Standardizing schema and data types  
- Stored inside the **Silver folder** in the same Data Lake container

### 🥇 **Gold Layer — Aggregated / Analytics Data**
- Data from Silver is **aggregated and modeled** for analytics  
- Loaded into **Azure Synapse Analytics** for querying, BI, and dashboards  
- Used **Power BI / Synapse Studio** to visualize KPIs like:
  - Sales trends
  - Payment distribution
  - Product performance
  - Customer activity

---

## 🧩 Data Source Details  

| Source | Location | Purpose |
|---------|-----------|----------|
| SQL (Filess.io) | MySQL cloud instance | 1 dataset uploaded manually (order payments) |
| MongoDB | Cloud collection | 1 dataset for unstructured / NoSQL data |
| GitHub (HTTP) | Azure Data Factory linked service | 7 CSV files ingested into Bronze container |

All datasets were combined, cleaned, and processed through Azure pipelines to create structured and analytics-ready tables.

---

## 💻 Data Ingestion Examples  

### 📥 Ingestion to SQL (Filess.io)
```python
import mysql.connector
import pandas as pd
from mysql.connector import Error

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

        # Load CSV and upload to table
        df = pd.read_csv("olist_order_payments_dataset.csv")
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

from pymongo import MongoClient
import pandas as pd

client = MongoClient("mongodb+srv://<username>:<password>@cluster.mongodb.net/")
db = client["ecommerce"]
collection = db["order_reviews"]

df = pd.read_csv("olist_order_reviews_dataset.csv")
records = df.to_dict(orient='records')
collection.insert_many(records)

print("✅ Data inserted into MongoDB successfully!")
🌐 Ingestion from GitHub (HTTP Connector in Azure Data Factory)
Created a Linked Service (HTTP) in ADF connected to your GitHub repo.

Imported 7 remaining CSV files directly using the HTTP dataset path.

Pipeline activities:

Copy activity: pulls CSV → stores into Azure Data Lake (Bronze container).

Mapping Data Flow: cleans and transforms → writes into Silver container.

Sink activity: pushes aggregated tables → Gold container → Azure Synapse.

🏗 Azure Services Used
Layer	Service	Purpose
Bronze	Azure Data Lake Storage (ADLS Gen2)	Raw data storage
Silver	Azure Data Factory / Databricks	Data cleaning and transformation
Gold	Azure Synapse Analytics	Final reporting and analytics
Ingestion	Filess.io (MySQL), MongoDB, GitHub (HTTP)	Source systems
Visualization	Power BI / Synapse Studio	Data dashboards
Management	Azure Key Vault / Monitor	Security and monitoring

See also:

Architecture Diagram.png

Data Pipeline.png

Layers.png

Linked service.png

nosql-sqldb.png

🚀 How to Run the Project
Clone the repository

bash
Copy code
git clone https://github.com/Amanpathak8/Azure-Data-Engineering-ecommerce-project.git
cd Azure-Data-Engineering-ecommerce-project
Install required libraries

bash
Copy code
pip install mysql-connector-python pandas pymongo
Update credentials

Filess.io MySQL credentials

MongoDB connection string

GitHub repo HTTP path in ADF

Run ingestion notebook

Execute DataIngestionToDB.ipynb for SQL & MongoDB ingestion

Use ADF pipeline for HTTP-based ingestion

Data Processing in Azure

Bronze → Silver → Gold transformation

Query Gold layer via Azure Synapse Analytics

Visualize

Connect Power BI or Synapse Studio for dashboarding

📊 Business Insights Enabled
Total orders, payments, and delivery metrics

Product performance by category

Customer segmentation and retention rates

Payment method distribution

Sales and revenue forecasting

📌 Future Enhancements
Add streaming ingestion via Azure Event Hub

Implement real-time dashboards with Power BI DirectQuery

Introduce data governance using Azure Purview

Add machine learning models for recommendations and churn prediction

👩‍💻 Author
Aman Pathak
GitHub: Amanpathak8

📄 License
This project is released under the MIT License.
See the LICENSE file for more details.

yaml
Copy code

---

Would you like me to add a **“Dataset Overview”** section at the end (brief description of all 9 files — which one in SQL, which in MongoDB, which 7 from GitHub)?  
That makes your README look even more complete and helps people understand the data flow in
