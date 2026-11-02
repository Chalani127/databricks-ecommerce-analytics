# Databricks E-Commerce Analytics

Medallion-architecture data pipeline for e-commerce sales analytics, built with PySpark on Databricks and governed by Unity Catalog. Raw CSV data is ingested from a Unity Catalog volume, progressively cleansed through bronze → silver → gold layers, and shaped into BI-ready dimension and fact tables.

## Project Structure

```text
1_setup/                      Workspace setup: creates the `ecommerce` catalog and the bronze/silver/gold schemas
2_medallion_processing_dim/   Dimension pipeline (brands, categories, products, customers)
  1_dim_bronze                Ingest raw CSVs from the UC volume into bronze tables (brz_*)
  2_dim_silver                Cleanse & standardize (trimming, symbol removal) into silver tables (slv_*)
  3_dim_gold                  Build BI-ready gold dimension tables (e.g. products denormalized with brand & category)
3_medallion_processing_fact/  Fact pipeline (order items)
  1_fact_bronze               Ingest order-items CSVs from the landing volume into brz_order_items
  2_fact_silver               Deduplicate and fix data anomalies (word-numbers, $ / % symbols, mixed date formats) → slv_order_items
  3_fact_gold                 Build BI-ready gold fact: gross_amount, discount_amount, sale_amount, currency conversion, date_id, coupon_flag
```

## Medallion Architecture

* **Bronze** — raw ingestion with explicit schemas plus ingestion metadata (`file_name` / `_source_file`, `ingest_timestamp`), landing in `ecommerce.bronze`
* **Silver** — cleansed and conformed data (deduplication, type casting, anomaly fixes) in `ecommerce.silver`
* **Gold** — business-ready dimension and fact tables for analytics and BI in `ecommerce.gold`

## Data Source

Raw CSVs live in the Unity Catalog volume `/Volumes/ecommerce/source_data/raw/` (brands, categories, products, customers, order items).

## Dashboard

A Sales Analytics dashboard (Databricks AI/BI Lakeview) is planned on top of the gold tables; its `.lvdash.json` source will be versioned in this repo once built.

## Tech Stack

* Databricks notebooks (PySpark + SQL)
* Delta Lake
* Unity Catalog (`ecommerce` catalog; `bronze`, `silver`, `gold` schemas)

## How to Run

1. Run `1_setup/` to create the catalog and schemas.
2. Run the dimension notebooks in order: `1_dim_bronze` → `2_dim_silver` → `3_dim_gold`.
3. Run the fact notebooks in order: `1_fact_bronze` → `2_fact_silver` → `3_fact_gold`.
