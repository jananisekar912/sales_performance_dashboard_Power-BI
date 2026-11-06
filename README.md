# Sales Performance Dashboard (Power BI)

An interactive three-page Power BI dashboard that turns sales data into business insights: revenue, customers, products and growth over time.

![Dashboard overview](screenshots/01_sales_dashboard.png)

**Data source:** the gold layer of my SQL Server data warehouse (`fact_sales`, `dim_customers`, `dim_products`) and two report views (`report_customers`, `report_products`) from my exploratory data analysis project.
Related repos: [Data Warehouse project](https://github.com/jananisekar912/sql-data-warehouse-project) | [SQL EDA project](https://github.com//jananisekar912/exploratory_data_analysis)

---

## Business questions

- How much revenue do we make, and from how many orders and customers?
- Which categories, products and countries matter most?
- Who are our customers, and how valuable are they?
- How are sales growing compared with the previous year?

## Key insights

| Metric | Result |
| Total sales | about 29.4 million |
| Total orders | 27,659 |
| Customers with orders | 18,484 |
| Average order value | about 1,061 |
| Top category | **Bikes: 96.5% of revenue** |
| Top country by customers | United States (7,482) |
| Customer segments | VIP 1,653 (9%) / Regular 2,200 (12%) / New 14,629 (79%) |

1. **Revenue depends on one category.** Bikes bring 96.5% of sales, so a drop in bike sales would hit the whole business.
2. **Most customers are New (79%).** Turning them into repeat buyers is the biggest opportunity.
3. **The top 5 products are all Mountain-200 bikes**, each bringing about 1.3 to 1.4 million.

**Recommendations:** run retention offers for New customers, protect VIP customers, and grow Accessories and Clothing.

---

## Dashboard pages

### 1. Sales Dashboard
[Sales Dashboard](screenshots/01_sales_dashboard.png)
- KPI cards: total sales, total orders, average order value, total customers
- Revenue by category (donut) and sales trend (line)
- Top 5 products, with a slicer to switch between **Sales** and **Quantity**

### 2. Customer Analysis
[Customer Analysis](screenshots/02_customer_analysis.png)
- Customer segments, customers by country, age group and gender
- VIP customers card with a **what-if slider** for the spending threshold
- Average spend per customer

### 3. Product and Sales Analysis
[Product and Sales Analysis](screenshots/03_product_sales_analysis.png)
- Sales vs prior year, YoY growth by category, running total
- Product table with performance segments (High / Mid / Low)
- Reached by **drill-through** from the Top 5 products chart

---

## Data model

Star schema:

- **Fact table:** `fact_sales`
- **Dimensions:** `dim_customers`, `dim_products`, `Date` (calendar table)
- **Report tables:** `report_customers`, `report_products`

The `Date` table is marked as the date table and is related to `fact_sales[order_date]` (one-to-many, single direction).

## DAX measures

```DAX
total_sales      = SUM(fact_sales[sales_amount])
total_orders     = DISTINCTCOUNT(fact_sales[order_number])
total_customers  = DISTINCTCOUNT(fact_sales[customer_key])
total_quantity   = SUM(fact_sales[quantity])
avg_order_value  = DIVIDE([total_sales], [total_orders])

Sales PY   = CALCULATE([total_sales], SAMEPERIODLASTYEAR('Date'[Date]))
Sales YoY % = DIVIDE([total_sales] - [Sales PY], [Sales PY])
Sales YTD  = TOTALYTD([total_sales], 'Date'[Date])
Running Total Sales =
    CALCULATE([total_sales],
        FILTER(ALLSELECTED('Date'[Date]), 'Date'[Date] <= MAX('Date'[Date])))

VIP Customers =
    CALCULATE(
        DISTINCTCOUNT(report_customers[customer_key]),
        FILTER(report_customers,
            report_customers[lifespan] >= 12
            && report_customers[total_sales] > [VIP Threshold Value]))
```

## Customer segments

- **VIP:** at least 12 months of history and spending over 5,000
- **Regular:** at least 12 months of history and spending 5,000 or less
- **New:** less than 12 months of history


## Repository structure

```
sales-performance-dashboard-powerbi/
|-- README.md
|-- powerbi/        # .pbix report
|-- dashboards/    # dashboard images
`-- docs/           # PDF export
```

## Author

Janani S | [LinkedIn](https://www.linkedin.com/in/janani-s-12091209p) | [GitHub](https://github.com/jananisekar912)

