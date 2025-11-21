# ecommerce-lakehouse
# 🛍️ E-Commerce Analytics Lakehouse (Azure Databricks + Delta + ADF)

A production-grade data engineering project that builds a scalable Lakehouse platform for a fictional e-commerce company. The system processes batch + streaming data from multiple sources using Medallion Architecture (Bronze → Silver → Gold), with enterprise-grade security, orchestration, and performance optimization.

---

## 🚀 **Key Features**

- Medallion Architecture (Bronze / Silver / Gold)
- Batch + Streaming data ingestion
- Incremental loads (Merge, CDC, SCD1/2)
- Delta Lake (ACID, Z-Order, Time travel, Schema evolution)
- Data Quality & Validation
- Azure AD Service Principal + Key Vault + OAuth Authentication
- Orchestration with Azure Data Factory / Databricks Jobs
- Power BI Integration for reporting
- Stored in Unity Catalog with External Locations

---

## 🏗 **Tech Stack**

| Category | Tools Used |
|----------|------------|
| Cloud Storage | Azure Data Lake Storage Gen2 (ADLS) |
| Compute | Azure Databricks (PySpark + SQL) |
| Data Format | Delta Lake |
| Orchestration | Azure Data Factory / Databricks Jobs |
| Security | Service Principal + OAuth + Key Vault |
| Governance | Unity Catalog |
| Streaming | Auto Loader / Event Hub (Optional) |
| BI | Power BI |

---

## 📂 **Project Structure**

