## 🧠 Data Warehousing Explained

### 📦 What is a Data Warehouse?
A **Data Warehouse (DW)** is a centralized system designed to **store, integrate, and analyze large volumes of structured data** from multiple sources. It's optimized for **querying and reporting**, not transactional processing.

---

### 🔍 Key Characteristics

| Feature          | Description                                        |
|------------------|----------------------------------------------------|
| Subject-Oriented | Focused on business areas like sales, finance      |
| Integrated       | Combines data from diverse sources                 |
| Time-Variant     | Maintains historical data                          |
| Non-Volatile     | Stable, rarely updated after entry                 |

---

### 🏗️ Architecture Overview

1. **Data Sources** – ERP, CRM, APIs, logs, files  
2. **ETL/ELT** – Extract, Transform, Load  
3. **Warehouse Storage** – Snowflake, Redshift, BigQuery  
4. **Data Marts** (optional) – Dept-specific subsets  
5. **BI Tools** – Tableau, Power BI, Looker  

Notes: 
---

### 🗃️ Schema Types

#### ⭐ Star Schema
- Central **fact table** with multiple **dimension tables**  
- Denormalized: faster reads, simpler joins  

#### ❄️ Snowflake Schema
- Normalized dimensions (split into sub-dimensions)  
- More joins, less redundancy  

---

### 🚀 Performance & Usage: Star vs Snowflake

| Feature          | Star Schema                         | Snowflake Schema                          |
|------------------|--------------------------------------|-------------------------------------------|
| **Structure**    | Denormalized                        | Normalized                                |
| **Query Speed**  | Faster (fewer joins)                | Slower (more joins required)              |
| **Storage Size** | Larger                              | Smaller                                   |
| **Ease of Use**  | Easier for analysts & BI            | More complex, better for large datasets   |
| **Use Case**     | Simple, high-performance analytics  | Complex data modeling, space optimization |

---

### 🧰 Tools & Technologies

| Layer         | Examples                                   |
|---------------|--------------------------------------------|
| ETL/ELT       | Apache Airflow, dbt, Talend, Informatica   |
| Warehouse     | Snowflake, Redshift, BigQuery, Synapse     |
| Query Engines | SQL, Spark SQL, Presto                     |
| BI Tools      | Power BI, Tableau, Looker, Superset        |
