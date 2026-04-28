# Customer & Revenue Analysis - SQL Portfolio

> **Project Overview:** This analysis explores customer spending patterns, revenue distribution, and purchasing behavior using advanced SQL techniques including Common Table Expressions (CTEs) and Window Functions.

---

## SQL Queries & Insights

### Q1: Calculate total revenue per customer
```sql
SELECT
    customer_id,
    SUM(o.units * p.unit_price) AS total_revenue
FROM orders o
JOIN products p
    ON o.product_id = p.product_id
GROUP BY customer_id;

-- Insight: Revenue distribution varies significantly across customers.
