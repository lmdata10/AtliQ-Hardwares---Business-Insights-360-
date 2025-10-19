# Data Storage Architectures

### 1. Data Warehouse

**Definition:**  
A central repository storing **structured, historical data** from multiple sources for analytics and reporting.

**Key Features:**

- Cleaned and integrated data
- Optimized for read-heavy operations
- Supports OLAP-style queries
- Built using ETL/ELT pipelines

**Real-World Example:**  
A bank consolidates customer transactions, loan history, and credit scores into Snowflake to enable risk analysts to detect fraud and assess credit risk.

**Popular Tools:**

- Snowflake
- Amazon Redshift
- Google BigQuery
- Azure Synapse Analytics

---
### 2. Data Lake

**Definition:**  
A storage repository that holds **vast amounts of raw data** in its **native format** (structured, semi-structured, and unstructured) until needed.

**Key Features:**

- Stores all data types (JSON, CSV, images, videos, logs)
- Schema-on-read approach
- Cost-effective storage at scale
- Supports batch and streaming data

**Real-World Example:**  
A streaming platform stores user clickstream data, video metadata, and application logs in AWS S3 Data Lake. Data scientists can later extract and analyze viewing patterns without predefined schemas.

**Popular Tools:**

- AWS S3
- Azure Data Lake Storage (ADLS)
- Google Cloud Storage
- Hadoop HDFS

**Challenges:**

- Can become a "data swamp" without proper governance
- Requires robust metadata management
- Performance issues with complex queries

---
### 3. Data Lakehouse

**Definition:**  
A modern architecture combining the **flexibility of Data Lakes** with the **structure and performance of Data Warehouses**.

**Key Features:**

- Supports both structured and unstructured data
- ACID transaction support
- Schema enforcement and evolution
- Direct BI tool connectivity
- Cost-effective storage with warehouse-like performance

**Real-World Example:**  
A fintech company uses Databricks Lakehouse to store raw transaction logs alongside processed financial reports. Data analysts can query the same platform using SQL, while data scientists run ML models on raw data—all without data duplication.

**Popular Tools:**

- Databricks (Delta Lake)
- Apache Iceberg
- Apache Hudi
- Dremio

**Advantages over Traditional Approaches:**

- Eliminates data duplication between lake and warehouse
- Single source of truth for all teams
- Reduces data movement and costs
- Supports both analytics and AI/ML workloads

---

## 🔄 OLTP vs OLAP

### OLTP – Online Transaction Processing

**Purpose:** Handles **real-time transactions** (insert, update, delete)

**Example:**  
A user transfers money via banking app—OLTP records it instantly.

**Characteristics:**

- Fast, real-time operations
- Current/live data
- Optimized for speed and consistency
- High volume of short transactions

**Systems:** PostgreSQL, MySQL, Oracle, SQL Server

---

### OLAP – Online Analytical Processing

**Purpose:** Used for **analysis and decision-making**

**Example:**  
A credit card executive uses Power BI to identify the **top 10 fraud-prone locations** over the past 6 months.

**Characteristics:**

- Read-heavy, complex queries
- Historical data
- Optimized for insights and reporting
- Aggregations and trend analysis

**Tools:** Snowflake, BigQuery, Power BI, Tableau

![](/assets/OLTP-OLAP.png)
### Quick Comparison

|Aspect|OLTP|OLAP|
|:--|:--|:--|
|**Purpose**|Running the business|Understanding the business|
|**Data**|Current, live|Historical|
|**Operations**|Insert, update, delete|Select, aggregate|
|**Users**|Operational staff|Analysts, executives|
|**Query Speed**|Milliseconds|Seconds to minutes|

---

## 📚 Data Catalog

**Definition:**  
A **searchable index** that helps users discover, understand, and trust data stored across systems.

**Key Features:**

- Metadata management
- Data lineage tracking
- Data ownership and glossary
- Search and discovery
- Data governance support

**Real-World Example:**  
A data analyst at a digital wallet company searches the catalog to find the "user_refunds" table, checks who owns it, and verifies when it was last updated.

**Popular Tools:**

- Alation
- Google Data Catalog
- AWS Glue Data Catalog
- Apache Atlas
- Collibra

---

## Concept Comparison

|Concept|Purpose|Use Case|
|:--|:--|:--|
|**Data Warehouse**|Historical data for analytics|Business reporting, fraud detection|
|**Data Lake**|Raw data storage at scale|Log storage, ML training data|
|**Data Lakehouse**|Unified analytics platform|BI + ML on single platform|
|**OLTP**|Real-time transactions|Banking transfers, payment processing|
|**OLAP**|Historical analysis|Sales trends, fraud pattern analysis|
|**Data Catalog**|Data discovery & governance|Finding trusted datasets across teams|

---

## Architecture Evolution

```
OLTP Systems (1970s)
    ↓
Data Warehouses (1990s)
    ↓
Data Lakes (2010s)
    ↓
Data Lakehouses (2020s) ← Current Best Practice
```

**Modern Trend:** Organizations are migrating from separate data lakes and warehouses to unified lakehouse architectures for better governance, reduced costs, and simplified data management.

---

## Getting Started

1. **For Transactional Workloads:** Start with OLTP databases (PostgreSQL, MySQL)
2. **For Analytics:** Evaluate Data Warehouse (Snowflake) or Lakehouse (Databricks)
3. **For Diverse Data Types:** Consider Data Lake architecture initially
4. **For Governance:** Implement Data Catalog from day one

---

## 📖 Additional Resources

- [Snowflake Documentation](https://docs.snowflake.com/)
- [Databricks Lakehouse Platform](https://databricks.com/product/data-lakehouse)
- [AWS Data Analytics](https://aws.amazon.com/big-data/datalakes-and-analytics/)
- [Data Engineering Fundamentals](https://datadoghq.com/knowledge-center/data-engineering/)

---