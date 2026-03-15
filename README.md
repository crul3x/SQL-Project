# Pizza Sales Analysis - SQL

## Project Overview

This project presents an in-depth analysis of a fictional pizza chain's full year of sales data (Jan - Dec 2015) using MySQL. The goal was to answer real business questions around revenue, customer ordering behavior, peak hours, and product performance - and to demonstrate practical SQL skills across aggregation, joins, subqueries, and window functions.

---

## Dataset

| Property | Detail |
|---|---|
| Source | Fictional pizza chain sales data |
| Period | January 1, 2015 - December 31, 2015 |
| Tables | 4 (orders, order_details, pizzas, pizza_types) |
| Total Orders | 21,350 |
| Total Order Lines | 48,620 |
| Total Pizzas Sold | 49,574 |
| Total Revenue | $817,860 |

**Table Structure:**
- `orders` - order ID, date, and time of each transaction (21,350 rows)
- `order_details` - pizza ID and quantity per order line (48,620 rows)
- `pizzas` - pizza SKU, size, and price (96 rows)
- `pizza_types` - pizza name, category, and ingredients (32 rows)

---

## Business Questions

1. How many total orders were placed and what revenue was generated?
2. Which pizza is the most expensive and which size drives the most volume?
3. Which pizzas and categories are the top performers by order volume and revenue?
4. When during the day are orders most concentrated?
5. How does each category contribute to total revenue, and which generates the highest value per order?
6. Which days of the week drive the most revenue?

---

## Process Walkthrough

### 1. Basic Aggregations
Started with high-level metrics - total orders, total revenue, highest priced item, and most popular size - to establish a baseline understanding of scale and structure.

### 2. Product Performance Analysis
Used multi-table joins across all 4 tables to break down performance by pizza type and category, identifying which products drive the most volume versus the most revenue - revealing a meaningful disconnect between the two.

### 3. Time-based Analysis
Extracted order hour using `HOUR()` to identify peak ordering windows throughout the day. Used `DAYNAME()` with date grouping to determine which days of the week generate the most revenue.

### 4. Revenue Distribution
Used subqueries to calculate each category's percentage contribution to total revenue. Used a CTE to calculate average revenue per order by category, and window functions to track cumulative revenue growth across the full year.

---

## Key Findings

- **Total revenue of $817,860** across 21,350 orders - averaging **$38.31 per order** and **$2,284 per day**
- **Large size dominates** with 18,956 units sold - nearly double Medium (15,635) and more than double Small (14,403)
- **Classic category leads in order volume** (14,579 orders) but Chicken pizzas take the top 3 spots by revenue - The Thai Chicken Pizza alone generated $43,434
- **Chicken has the highest avg revenue per order at $22.95** vs Classic at just $20.26 - despite Classic being ordered most frequently, Chicken delivers more value per transaction
- **Peak hours are 12pm and 1pm** with 2,520 and 2,455 orders respectively - followed closely by 6pm (2,399). Over 40% of all orders fall between 11am and 2pm or 5pm and 8pm
- **Friday is the strongest day** at $136,074 in revenue - Sunday is the weakest at $99,204, a 37% gap
- **Classic contributes 26.9% of total revenue**, Supreme 25.5%, Chicken 24.0%, Veggie 23.7% - a remarkably even distribution across all four categories
- **138 pizzas ordered per day on average** across 358 operating days

---

## SQL Techniques Demonstrated

- Multi-table `JOIN` across 4 related tables
- Aggregate functions: `SUM`, `COUNT`, `AVG`, `ROUND`, `MAX`
- Subqueries for percentage and filtered calculations
- `GROUP BY` and `ORDER BY` with `LIMIT`
- `HOUR()` and `DAYNAME()` for temporal analysis
- Window functions: `SUM() OVER (ORDER BY ...)` for cumulative revenue
- CTEs (`WITH`) for readable multi-step queries

---

## Files

| File | Description |
|---|---|
| `queries_and_results.md` | All 14 SQL queries with result tables |
| `orders.csv` | Raw orders data (21,350 rows) |
| `order_details.csv` | Raw order line items (48,620 rows) |
| `pizzas.csv` | Pizza SKUs, sizes, and prices (96 rows) |
| `pizza_types.csv` | Pizza names, categories, ingredients (32 rows) |

---

## How to Reproduce

1. Set up a MySQL database
2. Import all 4 CSV files as tables
3. Run queries from `queries_and_results.md` in order

---

### Conclusion

This project demonstrates SQL-driven analysis across a realistic multi-table dataset. The most actionable insight is the disconnect between order volume and revenue by category - Classic pizzas are ordered most frequently but Chicken pizzas generate disproportionately high revenue per order, which has direct implications for menu pricing and promotional strategy. The strong Friday peak and weak Sunday performance also point to clear opportunities for targeted weekend promotions.

---

### Contact

Feel free to contact me via:

Gmail : [yash2601sharma2@gmail.com](mailto:yash2601sharma2@gmail.com)

LinkedIn : [https://www.linkedin.com/in/yash-sharma-841835278/](https://www.linkedin.com/in/yash-sharma-841835278/)

---

*Dataset is fictional and does not represent any real business or individuals.*
