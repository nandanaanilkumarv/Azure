# Retail Sales Analytics using Microsoft Fabric

End-to-end data engineering project: raw retail files in Azure Storage are ingested with a Fabric Data Factory pipeline, cleaned with PySpark using a Bronze → Silver → Gold lakehouse design, and reported through a Power BI dashboard.

![Retail Sales Performance Dashboard](images/dashboard.png)

## Architecture

```
Azure Storage → Retail_Pipeline → Retail_LH (Bronze) → Retail_NB (PySpark) → Silver tables → gold_product_kpis → Power BI semantic model → Dashboard
```

| Layer | Object | Purpose |
|---|---|---|
| Source | Azure Storage container | `orders_data.csv`, `inventory_data.json`, `returns_data.xlsx` |
| Ingestion | `Retail_Pipeline` | Three Copy Data activities (orders, inventory, returns) |
| Bronze | `Retail_LH / Files / Bronze` | Raw data landed as Parquet |
| Silver | `silver_orders`, `silver_returns`, `silver_inventory` | Cleaned and typed tables |
| Gold | `gold_product_kpis` | Product-level KPIs |
| Reporting | Power BI semantic model + report | Retail Sales Performance Dashboard |

## Tech stack

Microsoft Fabric (Lakehouse, Data Factory, Notebooks) · Azure Storage (ADLS Gen2) · PySpark · Delta/Parquet · Power BI

## What the pipeline does

1. **Ingest:** Copy Data activities move the three source files into the Bronze folder as Parquet.
2. **Clean (Silver):** PySpark standardises column names, product names, quantities (e.g. "twenty", "one"), dates in mixed formats, and currency values, and handles nulls.
3. **Enrich:** Orders are left-joined with a returns summary on `Order_ID`; missing return counts and amounts are replaced with 0.
4. **Gold:** `gold_product_kpis` holds product-level metrics such as Total_Orders, Total_Revenue, Total_COGS, Net_Profit, Return_Rate_Percent, Current_Stock and Avg_Cost.
5. **Report:** A Power BI semantic model on the Gold table feeds the dashboard.

## Dashboard

- ProductName slicer
- Current stock by product (column chart)
- Returned orders by product (pie chart)
- Product-level summary table (orders, quantity, return amount, returns, average cost)
- Average cost by product

## Data quality issues handled

- Mixed quantity formats (numbers, words, NULL)
- Multiple date formats
- Mixed currency symbols and codes
- Inconsistent capitalisation and naming
- Header-like row inside the returns file
- 15 raw order rows vs 13 cleaned rows (needs reconciliation before production use)

## Repository contents

```
├── README.md
├── Retail_Sales_and_Return_Analytics.pdf   # full project report
└── images/
    └── dashboard.png
```

## Author

Nandana VA — [GitHub](https://github.com/nandanaanilkumarv)
