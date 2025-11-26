
📄 DATA DICTIONARY — E-Commerce Analytics Lakehouse
Purpose

Defines all entities, schemas, and data contracts across Bronze → Silver → Gold layers for the E-Commerce Lakehouse project.
Used by data engineers, analysts, governance teams, and interviewers to understand schema design.


# 📘 E-Commerce Lakehouse – Data Dictionary

This document describes all raw → bronze → silver → gold tables used in the project.

---

# 🟫 BRONZE LAYER (Raw Ingested Data)

## 1. bronze_customers_raw
| Column | Type | Description |
|--------|------|-------------|
| customer_id | STRING | Unique customer ID |
| first_name | STRING | First name |
| last_name | STRING | Last name |
| email | STRING | Email address |
| phone | STRING | Phone number |
| country | STRING | Country |
| created_at | TIMESTAMP | Customer creation timestamp |
| ingestion_ts | TIMESTAMP | Audit timestamp |
| source_file | STRING | Input file name |
| batch_id | STRING | Batch identifier |
| row_hash | STRING | Hash used for incremental detection |

---

## 2. bronze_orders_raw
| Column | Type |
|--------|------|
| order_id | STRING |
| customer_id | STRING |
| order_status | STRING |
| order_timestamp | TIMESTAMP |
| ingestion_ts | TIMESTAMP |
| source_file | STRING |
| batch_id | STRING |
| row_hash | STRING |

---

## 3. bronze_order_items_raw
| Column | Type |
|--------|------|
| order_item_id | STRING |
| order_id | STRING |
| product_id | STRING |
| quantity | INT |
| price | DOUBLE |
| ingestion_ts | TIMESTAMP |
| source_file | STRING |
| batch_id | STRING |
| row_hash | STRING |

---

## 4. bronze_products_raw
| Column | Type |
|--------|------|
| product_id | STRING |
| name | STRING |
| category | STRING |
| sub_category | STRING |
| price | DOUBLE |
| updated_at | TIMESTAMP |
| ingestion_ts | TIMESTAMP |
| source_file | STRING |
| batch_id | STRING |
| row_hash | STRING |

---

## 5. bronze_payments_raw
| Column | Type |
|--------|------|
| payment_id | STRING |
| order_id | STRING |
| payment_method | STRING |
| payment_status | STRING |
| amount | DOUBLE |
| payment_ts | TIMESTAMP |
| ingestion_ts | TIMESTAMP |
| source_file | STRING |
| batch_id | STRING |
| row_hash | STRING |

---

# ⚪ SILVER LAYER (Cleaned + Conformed)

## silver_customers
- Deduplicated on `customer_id`
- Standardized email, phone formats
- Null checks & data quality validation

Same columns as bronze except:
- `row_hash` removed
- Data cleaned and normalized

---

## silver_orders
- Deduplicated on `order_id`
- Valid customer_id enforced
- Order_status standardized

---

## silver_order_items
- Deduplicated on `order_item_id`
- Quantity/price validated
- Foreign keys validated

---

## silver_products
- Most recent product version selected using updated_at
- SCD Type-2 support possible

---

## silver_payments
- Deduplicated on `payment_id`
- Invalid/missing amounts filtered

---

# 🟡 GOLD LAYER (Analytics Ready)

## gold_sales_facts
| Column | Description |
|--------|-------------|
| order_id | Unique order |
| customer_id | Customer who placed order |
| product_id | Product purchased |
| quantity | Units sold |
| total_amount | quantity × price |
| payment_status | Paid / Failed |
| order_date | Date key |
| product_category | Category for reporting |

---

## gold_customer_360
Aggregated customer metrics:

| Metric |
|--------|
| total_orders |
| total_spend |
| avg_order_value |
| last_purchase_date |
| preferred_category |

---

## gold_product_performance
Product-level KPIs:

| Metric |
|--------|
| total_units_sold |
| total_revenue |
| refund_rate |
| repeat_purchase_rate |

---
