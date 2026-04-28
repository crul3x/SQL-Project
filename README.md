# Customer & Revenue Analysis - SQL

## Project Overview

This project analyzes customer purchasing behavior and revenue patterns using transactional order data in MySQL. The objective is to solve core business problems related to customer value, order performance, and revenue distribution, while demonstrating strong SQL fundamentals including aggregations, joins, CTEs, and advanced window functions.

The analysis focuses on identifying high-value customers, understanding order-level dynamics, and uncovering patterns that can support pricing, segmentation, and retention strategies.

---

## Dataset : [View Raw Dataset](https://docs.google.com/spreadsheets/d/1lfsP7dfm60WHy492Xa4O4jFkUtnvULR0ypaYO1w1Y2E/edit?usp=sharing)

| Property | Detail |
|---|---|
| Source | Transactional e-commerce dataset |
| Tables | 3 (customers, orders, products) |
| Key Metrics | Customer revenue, order value, product contribution |

**Table Structure:**
- `customers` - customer-level information  
- `orders` - transactional order data including units purchased  
- `products` - product pricing and details  

---

## Business Questions

1. Which customers generate the most revenue?
2. What percentage of total revenue does each customer contribute?
3. Who are the top 10% highest-value customers?
4. What is the most expensive order per customer?
5. How does each order compare to a customer’s average spending behavior?
6. Which products dominate revenue within each order?
7. How does revenue accumulate over time for each customer?
8. How do customers segment into high, medium, and low value groups?

---

## Process Walkthrough

### 1. Revenue Aggregation
Calculated total revenue per customer using joins and aggregation to establish a baseline understanding of customer value distribution.

### 2. Customer Ranking & Segmentation
Used window functions like `DENSE_RANK()` and `NTILE()` to rank customers and segment them into percentile-based groups (high, medium, low value).

### 3. Order-Level Analysis
Computed total value per order and identified:
- highest value orders per customer  
- second highest orders  
- orders exceeding customer averages  

This helped analyze consistency and spending patterns.

### 4. Product Contribution Analysis
Broke down each order to identify which product contributed the most value using window functions and row-level ranking.

### 5. Running Revenue Trends
Used cumulative sums (`SUM() OVER (ORDER BY ...)`) to track how revenue builds over time for each customer, highlighting purchasing behavior patterns.

### 6. Behavioral Comparison
Used `LAG()` to compare each order with the previous one, identifying changes in spending and variability in customer behavior.

---

## Key Findings

- Revenue distribution is highly skewed, with a small percentage of customers contributing a disproportionately large share of total revenue  
- Top 10% of customers account for a significant portion of overall revenue, indicating strong potential for targeted retention strategies  
- Customer spending is inconsistent across orders, with several instances of large deviations from average order value  
- Certain products consistently dominate order value, suggesting opportunities for bundling and pricing optimization  
- Running revenue trends show that high-value customers tend to place fewer but significantly larger orders  
- Segmentation highlights clear behavioral differences between high, medium, and low-value customers, enabling targeted marketing strategies  

---

## SQL Techniques Demonstrated

- Multi-table `JOIN` across relational datasets  
- Aggregate functions: `SUM`, `AVG`, `COUNT`  
- `GROUP BY` and `ORDER BY` for structured aggregation  
- CTEs (`WITH`) for modular query design  
- Window functions:
  - `ROW_NUMBER()`, `DENSE_RANK()` for ranking  
  - `NTILE()` for segmentation  
  - `LAG()` for sequential comparison  
  - `SUM() OVER()` for running totals  
- Conditional logic using `CASE`  

---

## Business Impact / What This Solves

- **Customer Segmentation** → Identifies high-value customers for targeted retention and marketing  
- **Revenue Concentration Analysis** → Helps understand dependency on top customers  
- **Order Behavior Insights** → Reveals inconsistencies in customer spending patterns  
- **Product Strategy** → Identifies products driving maximum value per order  
- **Performance Tracking** → Enables monitoring of revenue growth at a customer level  

---

## Conclusion

This project demonstrates the ability to translate business problems into structured SQL analysis. The most important takeaway is the concentration of revenue among a small segment of customers and the variability in order behavior, both of which are critical for designing pricing strategies, customer retention plans, and targeted promotions.

---

## Contact

Gmail: [yash2601sharma2@gmail.com](mailto:yash2601sharma2@gmail.com)  
LinkedIn: [https://www.linkedin.com/in/yash-sharma-841835278/](https://www.linkedin.com/in/yash-sharma-841835278/)  

---

*Dataset is for analytical practice and does not represent any real business.*
