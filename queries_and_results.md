# Customer & Revenue Analysis - SQL Portfolio

> **Project Overview:** This analysis explores customer spending patterns, revenue distribution, and purchasing behavior using advanced SQL techniques including Common Table Expressions (CTEs) and Window Functions.

---

## Combined SQL Analysis Script

```sql
/* ===============================================================================
CUSTOMER & REVENUE ANALYSIS SCRIPT
===============================================================================
*/

-- Q1: Calculate total revenue per customer
-- Insight: Revenue distribution varies significantly across customers.
SELECT
    customer_id,
    SUM(o.units * p.unit_price) AS total_revenue
FROM orders o
JOIN products p
    ON o.product_id = p.product_id
GROUP BY customer_id;


-- Q2: Find the top 5 customers by total revenue
-- Insight: A small group of customers contributes a large portion of revenue.
SELECT
    customer_id,
    SUM(o.units * p.unit_price) AS total_revenue
FROM orders o
JOIN products p
    ON o.product_id = p.product_id
GROUP BY customer_id
ORDER BY total_revenue DESC
LIMIT 5;


-- Q3: Calculate percentage contribution of each customer to total revenue
-- Insight: Revenue contribution highlights dependency on top customers.
WITH customer_revenue AS (
    SELECT
        customer_id,
        SUM(o.units * p.unit_price) AS total_revenue
    FROM orders o
    JOIN products p
        ON o.product_id = p.product_id
    GROUP BY customer_id
)
SELECT
    customer_id,
    total_revenue,
    ROUND(total_revenue * 100.0 / SUM(total_revenue) OVER (), 2) AS contribution_percentage
FROM customer_revenue;


-- Q4: Rank customers based on total revenue (highest first)
-- Insight: Ranking helps prioritize high-value customers.
WITH customer_revenue AS (
    SELECT
        customer_id,
        SUM(o.units * p.unit_price) AS total_revenue
    FROM orders o
    JOIN products p
        ON o.product_id = p.product_id
    GROUP BY customer_id
)
SELECT
    customer_id,
    total_revenue,
    DENSE_RANK() OVER (
        ORDER BY total_revenue DESC
    ) AS customer_rank
FROM customer_revenue;


-- Q5: Identify the top 10% highest-spending customers
-- Insight: Top 10% customers form the core revenue-driving segment.
WITH customer_revenue AS (
    SELECT
        customer_id,
        SUM(o.units * p.unit_price) AS total_revenue
    FROM orders o
    JOIN products p
        ON o.product_id = p.product_id
    GROUP BY customer_id
),
ranked AS (
    SELECT
        *,
        NTILE(100) OVER (
            ORDER BY total_revenue DESC
        ) AS percentile_bucket
    FROM customer_revenue
)
SELECT
    customer_id,
    total_revenue
FROM ranked
WHERE percentile_bucket <= 10;


-- Q6: Find the most expensive order for each customer
-- Insight: Identifies peak spending behavior per customer.
WITH order_values AS (
    SELECT
        customer_id,
        order_id,
        SUM(o.units * p.unit_price) AS total_order_value
    FROM orders o
    JOIN products p
        ON o.product_id = p.product_id
    GROUP BY customer_id, order_id
),
ranked AS (
    SELECT
        *,
        ROW_NUMBER() OVER (
            PARTITION BY customer_id
            ORDER BY total_order_value DESC, order_id
        ) AS rank_order
    FROM order_values
)
SELECT
    customer_id,
    order_id,
    total_order_value
FROM ranked
WHERE rank_order = 1;


-- Q7: Find the second most expensive order for each customer
-- Insight: Shows secondary spending behavior patterns.
WITH order_values AS (
    SELECT
        customer_id,
        order_id,
        SUM(o.units * p.unit_price) AS total_order_value
    FROM orders o
    JOIN products p
        ON o.product_id = p.product_id
    GROUP BY customer_id, order_id
),
ranked AS (
    SELECT
        *,
        ROW_NUMBER() OVER (
            PARTITION BY customer_id
            ORDER BY total_order_value DESC, order_id
        ) AS rank_order
    FROM order_values
)
SELECT
    customer_id,
    order_id,
    total_order_value
FROM ranked
WHERE rank_order = 2;


-- Q8: Find orders above customer’s average order value
-- Insight: Highlights unusually high-value orders.
WITH order_values AS (
    SELECT
        customer_id,
        order_id,
        SUM(o.units * p.unit_price) AS total_order_value
    FROM orders o
    JOIN products p
        ON o.product_id = p.product_id
    GROUP BY customer_id, order_id
),
avg_values AS (
    SELECT
        *,
        AVG(total_order_value) OVER (
            PARTITION BY customer_id
        ) AS avg_order_value
    FROM order_values
)
SELECT
    customer_id,
    order_id,
    total_order_value
FROM avg_values
WHERE total_order_value > avg_order_value;


-- Q9: Find the product contributing highest value in each order
-- Insight: Identifies key products driving order value.
WITH product_values AS (
    SELECT
        order_id,
        o.product_id,
        SUM(o.units * p.unit_price) AS product_contribution_value
    FROM orders o
    JOIN products p
        ON o.product_id = p.product_id
    GROUP BY order_id, o.product_id
),
ranked AS (
    SELECT
        *,
        ROW_NUMBER() OVER (
            PARTITION BY order_id
            ORDER BY product_contribution_value DESC, product_id
        ) AS rank_product
    FROM product_values
)
SELECT
    order_id,
    product_id,
    product_contribution_value
FROM ranked
WHERE rank_product = 1;


-- Q10: Calculate running revenue per customer
-- Insight: Tracks cumulative customer spending over time.
WITH order_values AS (
    SELECT
        customer_id,
        order_id,
        SUM(o.units * p.unit_price) AS total_order_value
    FROM orders o
    JOIN products p
        ON o.product_id = p.product_id
    GROUP BY customer_id, order_id
)
SELECT
    customer_id,
    order_id,
    total_order_value,
    SUM(total_order_value) OVER (
        PARTITION BY customer_id
        ORDER BY order_id
    ) AS running_total_revenue
FROM order_values;


-- Q11: Segment customers based on revenue
-- Insight: Enables targeted customer segmentation (High/Medium/Low).
WITH customer_revenue AS (
    SELECT
        customer_id,
        SUM(o.units * p.unit_price) AS total_revenue
    FROM orders o
    JOIN products p
        ON o.product_id = p.product_id
    GROUP BY customer_id
),
ranked AS (
    SELECT
        *,
        NTILE(100) OVER (
            ORDER BY total_revenue DESC
        ) AS percentile
    FROM customer_revenue
)
SELECT
    customer_id,
    total_revenue,
    CASE
        WHEN percentile <= 20 THEN 'High'
        WHEN percentile <= 50 THEN 'Medium'
        ELSE 'Low'
    END AS segment
FROM ranked;


-- Q12: Compare each order with previous order
-- Insight: Reveals changes in customer spending behavior over time.
WITH order_values AS (
    SELECT
        customer_id,
        order_id,
        SUM(o.units * p.unit_price) AS current_order_value
    FROM orders o
    JOIN products p
        ON o.product_id = p.product_id
    GROUP BY customer_id, order_id
)
SELECT
    customer_id,
    order_id,
    current_order_value,
    LAG(current_order_value) OVER (
        PARTITION BY customer_id
        ORDER BY order_id
    ) AS previous_order_value,
    current_order_value -
    LAG(current_order_value) OVER (
        PARTITION BY customer_id
        ORDER BY order_id
    ) AS difference
FROM order_values;
