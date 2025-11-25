E-Commerce Customer 360 Lakehouse – Architecture

1. Overview
The solution implements an end-to-end Lakehouse architecture on Azure to process e-commerce transactional and behavioral data.  
The platform supports batch ingestion, micro-batch streaming ingestion, change data capture (CDC), and scalable transformations using the Medallion architecture (Bronze → Silver → Gold).

Core technologies:
- Azure Data Lake Storage Gen2
- Azure Databricks (Delta Lake)
- Azure Data Factory
- Azure Event Hubs (streaming)
- Azure Key Vault
- Unity Catalog for governance
- Power BI for reporting

---

2. High-Level Architecture

```
Sources
 ├─ Batch files (Orders, OrderItems)
 ├─ RDBMS (Customer Master)
 ├─ REST API (Product Catalog)
 └─ Event Hubs (Cart Events)

Landing (ADLS)
 └─ Raw immutable storage for all incoming data

Bronze (Delta)
 └─ Ingested data with schema, audit columns, and minimal transformations

Silver (Delta)
 └─ Cleaned, validated, deduplicated, and conformed data
    Supports SCD1/SCD2, enrichment, reference joins

Gold (Delta)
 └─ Business serving tables:
       - fact_orders
       - fact_order_items
       - dim_customer
       - dim_product
       - aggregated metrics

Orchestration
 ├─ Azure Data Factory
 └─ Databricks Jobs

Governance
 └─ Unity Catalog (catalogs, schemas, tables, ACLs)

Serving
 └─ Databricks SQL / Power BI
```

---

3. Data Flow Summary

3.1 Ingestion
- **ADF** copies batch data (files / database tables / API results) into the ADLS **Landing** zone.
- **Databricks Auto Loader** ingests streaming data from Event Hubs into Bronze.

3.2 Bronze Layer
- Convert data into Delta format.
- Apply initial schema and audit fields.
- Store raw-but-queryable data.
- Maintain ingestion logs and file audit information.

3.3 Silver Layer
- Enforce schema and data types.
- Deduplicate records.
- Apply business rules and data quality validations.
- Implement SCD Type 1 and Type 2 for master entities.
- Join with reference tables.
- Produce conformed dimensional entities.

3.4 Gold Layer
- Build analytical data models optimized for BI.
- Construct fact and dimension tables.
- Use Z-ORDER and partitioning where applicable.
- Expose views/tables through Databricks SQL.

---

4. Storage Layout (ADLS)

```
/landing/<source>/<date>/file.*
/bronze/<entity>/...
/silver/<entity>/...
/gold/<entity>/...
/quarantine/<entity>/...
/logs/pipeline_runs/
/logs/dq_results/
```

---

5. Transformation Layer (Databricks)

- Source ingestion logic implemented as parameterized notebooks.
- Reusable utilities for schema loading, mount paths, configs, and DQ checks.
- Silver transformations implemented using Delta MERGE for incremental upserts.
- Gold transformations materialize fact/dimension models.

---

6. Streaming Ingestion (Event Hub → Bronze)

- Using Auto Loader (`cloudFiles`) to read streaming events.
- Schema evolution enabled.
- Checkpointing ensures exactly-once processing.
- Stored as Delta in Bronze for downstream transformations.

---

7. Orchestration (ADF + Databricks Jobs)

- **ADF Pipelines** trigger Databricks Jobs with parameters (e.g., file date, batch ID).
- Job clusters are used for production workloads.
- Sequential and conditional activities implemented for Bronze → Silver → Gold processing.

---

8. Security & Access Control

- Sensitive credentials stored in **Azure Key Vault**.
- Databricks uses Key-Vault–backed secret scopes.
- **Service Principal** authenticates to ADLS via OAuth.
- Unity Catalog manages:
  - Catalog → Schema → Table ownership
  - Table-level ACLs
  - Data governance policies

---

9. CI/CD & Deployment (Lightweight)

The project includes:
- GitHub repository for version control.
- GitHub Actions workflow for:
  - Notebook/code validation and linting
  - Deploying notebooks to Databricks Repos
  - Updating job definitions through Databricks Jobs API
- Terraform/ARM templates (minimal) for provisioning:
  - Resource group
  - ADLS
  - Key Vault
  - ADF
  - Databricks workspace (optional)

---

10. Monitoring & Data Quality

- Ingestion and transformation logs stored in Delta tables:
  - `/logs/pipeline_runs/`
  - `/logs/dq_results/`
- ADF alerting (via Logic App or Action Group) on pipeline failures.
- Optional dashboards in Databricks SQL or Power BI for pipeline health.

---

11. Serving Layer

- Gold tables registered in Unity Catalog.
- Consumed by:
  - Databricks SQL Analytics
  - Power BI Direct Lake / DirectQuery / Import mode.

---
 12. Summary

This Lakehouse architecture provides:
- Scalable ingestion (batch + streaming)
- Reliable transformations with Delta Lake
- High-quality curated datasets in a medallion layout
- Governed and secure data access
- Modern deployment and monitoring patterns

