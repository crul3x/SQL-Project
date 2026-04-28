I see what happened—the previous response got cut off because it was so long! Here is the **entire project** in one single block of code.

Copy everything inside the box below and paste it directly into that empty space where it says "Enter file contents here" in your screenshot.

```markdown
# Customer & Revenue Analysis - SQL Portfolio

> **Project Overview:** This analysis explores customer spending patterns, revenue distribution, and purchasing behavior using advanced SQL techniques including Common Table Expressions (CTEs), Window Functions, and Data Segmentation.

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
```

### Q2: Find the top 5 customers by total revenue
```sql
SELECT
    customer_id,
    SUM(o.units * p.unit_price) AS total_revenue
FROM orders o
JOIN products p
    ON o.product_id = p.product_id
GROUP BY customer_id
ORDER BY total_revenue DESC
LIMIT 5;

-- Insight: A small group of customers contributes a large portion of revenue.
```

### Q3: Calculate percentage contribution of each customer to total revenue
```sql
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

-- Insight: Revenue is concentrated among a limited number of customers.
```

### Q4: Rank customers based on total revenue
```sql
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

-- Insight: Ranking helps identify and prioritize high-value customers.
```

### Q5: Identify the top 10% highest-spending customers
```sql
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

-- Insight: Top 10% customers form the core revenue-driving segment.
```

### Q6: Find the most expensive order for each customer
```sql
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

-- Insight: Identifies peak spending behavior per customer.
```

### Q7: Find the second most expensive order for each customer
```sql
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

-- Insight: Shows consistency and secondary spending patterns.
```

### Q8: Find orders above customer’s average order value
```sql
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

-- Insight: Highlights unusually high-value transactions compared to typical behavior.
```

### Q9: Find the product contributing highest value in each order
```sql
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

-- Insight: Identifies which products dominate order value.
```

### Q10: Calculate running revenue per customer
```sql
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

-- Insight: Tracks how customer revenue accumulates over time.
```

### Q11: Segment customers based on revenue
```sql
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

-- Insight: Enables clear segmentation for targeted marketing or retention strategies.
```

### Q12: Compare each order with previous order
```sql
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

-- Insight: Reveals changes and volatility in customer spending behavior over time.
```
```
