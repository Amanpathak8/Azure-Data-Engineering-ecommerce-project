# Azure Data Engineering Ecommerce Project

<img src="Architecture Diagram.png" alt="Architecture Diagram" />

## 🧾 Project Overview  
This repository demonstrates an **end-to-end Azure Data Engineering pipeline** for an **E-commerce analytics project**, built using the **Medallion Architecture (Bronze–Silver–Gold)**.  
Data is ingested from multiple sources — **Filess.io (SQL)**, **MongoDB**, and **GitHub (HTTP)** — processed using **Azure Data Factory**, and stored in **Azure Data Lake (ADLS Gen2)** containers for transformation and analytics through **Azure Synapse Analytics** and **Power BI**.

---

## 🪙 Medallion Architecture Overview  
- **🥉 Bronze Layer:** Raw data from SQL, MongoDB, and GitHub stored in the *Bronze container* on ADLS.  
- **🥈 Silver Layer:** Cleaned and transformed data created using Azure Data Factory data flows (duplicates removed, schema fixed, nulls handled).  
- **🥇 Gold Layer:** Aggregated analytical data loaded into **Azure Synapse Analytics**, visualized with **Power BI** for business insights.  

---

## 📦 Data Sources & Flow  
- **1 file** uploaded into **SQL (Filess.io)** → MySQL cloud DB  
- **1 file** uploaded into **MongoDB** → for unstructured/review data  
- **7 CSV files** pulled from **GitHub via HTTP connector in Azure Data Factory**  
→ all datasets moved to **Bronze → Silver → Gold** layers inside **Azure Data Lake container**.  

---

## 💻 Data Ingestion Examples  

### 📥 SQL (Filess.io)
```python
import mysql.connector, pandas as pd
from mysql.connector import Error

hostname, database, port = "6bnvj.h.filess.io", "olistproject_slopelack", "3307"
username, password = "olistproject_slopelack", "YOUR_PASSWORD_HERE"

try:
    conn = mysql.connector.connect(host=hostname, database=database, user=username, password=password, port=port)
    if conn.is_connected():
        print("✅ Connected to MySQL Server")
        df = pd.read_csv("olist_order_payments_dataset.csv")
        cur = conn.cursor()
        for _, r in df.iterrows():
            cur.execute("INSERT INTO olist_order_payments VALUES (%s,%s,%s,%s,%s)", tuple(r))
        conn.commit()
        print("✅ Data uploaded successfully!")
finally:
    if conn.is_connected(): conn.close(); print("🔒 MySQL connection closed")
```

### 📤 MongoDB
```python
from pymongo import MongoClient
import pandas as pd

client = MongoClient("mongodb+srv://<username>:<password>@cluster.mongodb.net/")
db, coll = client["ecommerce"], db["order_reviews"]
df = pd.read_csv("olist_order_reviews_dataset.csv")
coll.insert_many(df.to_dict(orient='records'))
print("✅ Data inserted into MongoDB successfully!")
```

### 🌐 GitHub → Azure (HTTP Connector in ADF)
Used Azure Data Factory HTTP linked service to pull 7 CSVs directly from GitHub, loaded them into the **Bronze container** on ADLS, cleaned with **Mapping Data Flows** (Silver layer), and wrote aggregated outputs to the **Gold layer**, then loaded into **Azure Synapse Analytics** for reporting.

---

## 🏗 Azure Services Used  
| Layer | Service | Purpose |
|-------|----------|----------|
| Bronze | Azure Data Lake Storage (ADLS Gen2) | Raw data storage |
| Silver | Azure Data Factory / Databricks | Data cleaning & transformation |
| Gold | Azure Synapse Analytics | Final analytics & reporting |
| Ingestion | Filess.io (MySQL), MongoDB, GitHub (HTTP) | Source systems |
| Visualization | Power BI / Synapse Studio | Dashboards & BI |
| Security | Azure Key Vault | Secrets management |
| Monitoring | Azure Monitor | Pipeline tracking |

Reference diagrams: `Architecture Diagram.png`, `Data Pipeline.png`, `Layers.png`, `Linked service.png`, `nosql-sqldb.png`

---

## 🚀 How to Run the Project  
```bash
# 1. Clone the repo
git clone https://github.com/Amanpathak8/Azure-Data-Engineering-ecommerce-project.git
cd Azure-Data-Engineering-ecommerce-project

# 2. Install required libraries
pip install mysql-connector-python pandas pymongo
```
**Update credentials:**  
- Add Filess.io MySQL username/password  
- Add MongoDB URI  
- Configure GitHub HTTP path in Azure Data Factory  

**Run:**  
- Execute `DataIngestionToDB.ipynb` for SQL & MongoDB ingestion  
- Use Azure Data Factory pipeline for HTTP ingestion  
- Transform Bronze → Silver → Gold using ADF or Databricks  
- Query final data in Azure Synapse and visualize in Power BI  

---

## 📊 Business Insights Delivered  
- Total orders, payments & revenue trends  
- Product performance by category  
- Customer segmentation & repeat purchase patterns  
- Payment method breakdown  
- Review sentiment & delivery analytics  

---

## 📈 Dataset Overview (9 Files)
| Source | File Example | Purpose |
|---------|---------------|----------|
| SQL (Filess.io) | olist_order_payments_dataset.csv | Transaction & payment data |
| MongoDB | olist_order_reviews_dataset.csv | Customer reviews & ratings |
| GitHub (HTTP → ADF) | 7 CSVs (orders, customers, products, sellers, items, etc.) | E-commerce operational data |

---

## 📌 Future Enhancements  
- Add **real-time ingestion** with Azure Event Hub  
- Enable **streaming analytics** using Azure Stream Analytics  
- Implement **data governance** with Azure Purview  
- Build **live Power BI dashboards**  
- Integrate **machine learning models** for prediction  

---

##  Author  
**Aman Pathak**  
GitHub: [Amanpathak8](https://github.com/Amanpathak8)

---

