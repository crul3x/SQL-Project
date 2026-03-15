# Pizza Sales - SQL Queries & Results

## Q1: Retrieve the Total Number of Orders Placed

```sql
SELECT 
    COUNT(order_id) AS total_orders
FROM
    orders;
```

| total_orders |
|--------------|
| 21350        |

## Q2: Calculate the Total Revenue Generated from Pizza Sales

```sql
SELECT 
    ROUND(SUM(order_details.quantity * pizzas.price), 2) AS total_revenue
FROM
    order_details
    JOIN pizzas ON order_details.pizza_id = pizzas.pizza_id;
```

| total_revenue |
|---------------|
| 817860.05     |

## Q3: Identify the Highest-Priced Pizza

```sql
SELECT 
    pizza_types.name, pizzas.*
FROM
    pizzas
    JOIN pizza_types ON pizzas.pizza_type_id = pizza_types.pizza_type_id
WHERE
    price = (SELECT MAX(price) FROM pizzas);
```

| name | pizza_id | pizza_size | price |
|---|---|---|---|
| The Greek Pizza | the_greek_xxl | XXL | 35.95 |

## Q4: Identify the Most Common Pizza Size Ordered

```sql
SELECT 
    size, COUNT(size) AS orders
FROM
    order_details
    JOIN pizzas ON order_details.pizza_id = pizzas.pizza_id
GROUP BY size
ORDER BY orders DESC
LIMIT 1;
```

| size | orders |
|---|---|
| L | 18526 |

## Q5: List the Top 5 Most Ordered Pizza Types Along with Their Quantities

```sql
SELECT 
    pizza_types.name, COUNT(order_details.pizza_id) AS orders
FROM
    order_details
    JOIN pizzas ON order_details.pizza_id = pizzas.pizza_id
    JOIN pizza_types ON pizzas.pizza_type_id = pizza_types.pizza_type_id
GROUP BY pizza_types.name
ORDER BY orders DESC
LIMIT 5;
```

| name | orders |
|---|---|
| The Classic Deluxe Pizza | 2416 |
| The Barbecue Chicken Pizza | 2372 |
| The Hawaiian Pizza | 2370 |
| The Pepperoni Pizza | 2369 |
| The Thai Chicken Pizza | 2315 |

## Q6: Find the Total Quantity of Each Pizza Category Ordered

```sql
SELECT 
    category, COUNT(category) AS orders
FROM
    pizza_types
    JOIN pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
    JOIN order_details ON order_details.pizza_id = pizzas.pizza_id
GROUP BY category
ORDER BY orders DESC;
```

| category | orders |
|---|---|
| Classic | 14579 |
| Supreme | 11777 |
| Veggie | 11449 |
| Chicken | 10815 |

## Q7: Determine the Distribution of Orders by Hour of the Day

```sql
SELECT 
    HOUR(order_time) AS hour, COUNT(order_time) AS orders
FROM
    orders
GROUP BY hour;
```

| hour | orders |
|---|---|
| 9 | 1 |
| 10 | 8 |
| 11 | 1231 |
| 12 | 2520 |
| 13 | 2455 |
| 14 | 1472 |
| 15 | 1468 |
| 16 | 1920 |
| 17 | 2336 |
| 18 | 2399 |
| 19 | 2009 |
| 20 | 1642 |
| 21 | 1198 |
| 22 | 663 |
| 23 | 28 |

## Q8: Find the Category-wise Distribution of Pizzas

```sql
SELECT category, COUNT(name)
FROM pizza_types
GROUP BY category;
```

| category | count |
|---|---|
| Chicken | 6 |
| Classic | 8 |
| Supreme | 9 |
| Veggie | 9 |

## Q9: Calculate the Average Number of Pizzas Ordered Per Day

```sql
SELECT 
    AVG(quantity)
FROM
    (SELECT 
        orders.order_date, SUM(order_details.quantity) AS quantity
    FROM
        orders
    JOIN order_details ON orders.order_id = order_details.order_id
    GROUP BY orders.order_date) AS order_quantity;
```

| avg |
|---|
| 138.4749 |

## Q10: Determine the Top 3 Most Ordered Pizza Types Based on Revenue

```sql
SELECT 
    pizzas.pizza_type_id,
    name,
    ROUND(SUM(quantity * price), 1) AS revenue
FROM
    order_details
    JOIN pizzas ON order_details.pizza_id = pizzas.pizza_id
    JOIN pizza_types ON pizzas.pizza_type_id = pizza_types.pizza_type_id
GROUP BY pizzas.pizza_type_id, name
ORDER BY revenue DESC
LIMIT 3;
```

| pizza_type_id | name | revenue |
|---|---|---|
| thai_ckn | The Thai Chicken Pizza | 43434.2 |
| bbq_ckn | The Barbecue Chicken Pizza | 42768.0 |
| cali_ckn | The California Chicken Pizza | 41409.5 |

## Q11: Calculate the Percentage Contribution of Each Pizza Category to Total Revenue

```sql
SELECT 
    pizza_types.category,
    ROUND(SUM(order_details.quantity * pizzas.price) / (
        SELECT 
            ROUND(SUM(order_details.quantity * pizzas.price), 2)
        FROM
            order_details
            JOIN pizzas ON order_details.pizza_id = pizzas.pizza_id
    ) * 100, 2) AS percentage
FROM
    order_details
    JOIN pizzas ON order_details.pizza_id = pizzas.pizza_id
    JOIN pizza_types ON pizzas.pizza_type_id = pizza_types.pizza_type_id
GROUP BY pizza_types.category;
```

| category | percentage |
|---|---|
| Classic | 26.91 |
| Supreme | 25.46 |
| Chicken | 23.96 |
| Veggie | 23.68 |

## Q12: Cumulative Revenue Over Time

```sql
SELECT 
    order_date,
    ROUND(SUM(daily_revenue) OVER (ORDER BY order_date), 2) AS cumulative_revenue
FROM (
    SELECT 
        orders.order_date,
        SUM(order_details.quantity * pizzas.price) AS daily_revenue
    FROM orders
    JOIN order_details ON orders.order_id = order_details.order_id
    JOIN pizzas ON order_details.pizza_id = pizzas.pizza_id
    GROUP BY orders.order_date
) AS daily_totals
ORDER BY order_date;
```

*Sample of results (full output spans 358 days):*

| order_date | cumulative_revenue |
|---|---|
| 2015-01-01 | 2713.85 |
| 2015-01-02 | 5125.65 |
| 2015-01-03 | 7489.30 |
| ... | ... |
| 2015-12-31 | 817860.05 |

*Shows revenue accumulating day by day across the full year - useful for spotting growth trends and seasonal dips.*

## Q13: Average Revenue Per Order by Category

```sql
WITH order_revenue AS (
    SELECT 
        orders.order_id,
        pizza_types.category,
        SUM(order_details.quantity * pizzas.price) AS order_value
    FROM orders
    JOIN order_details ON orders.order_id = order_details.order_id
    JOIN pizzas ON order_details.pizza_id = pizzas.pizza_id
    JOIN pizza_types ON pizzas.pizza_type_id = pizza_types.pizza_type_id
    GROUP BY orders.order_id, pizza_types.category
)
SELECT 
    category,
    ROUND(AVG(order_value), 2) AS avg_revenue_per_order,
    COUNT(order_id) AS total_orders
FROM order_revenue
GROUP BY category
ORDER BY avg_revenue_per_order DESC;
```

| category | avg_revenue_per_order | total_orders |
|---|---|---|
| Chicken | 22.95 | 8536 |
| Supreme | 22.92 | 9085 |
| Veggie | 21.66 | 8941 |
| Classic | 20.26 | 10859 |

*Despite Classic being the most ordered category by volume (Q6), Chicken generates the highest average revenue per order - direct evidence that order frequency alone does not reflect revenue value.*

## Q14: Top Performing Day of the Week by Revenue

```sql
SELECT 
    DAYNAME(orders.order_date) AS day_of_week,
    ROUND(SUM(order_details.quantity * pizzas.price), 2) AS total_revenue,
    COUNT(DISTINCT orders.order_id) AS total_orders
FROM orders
JOIN order_details ON orders.order_id = order_details.order_id
JOIN pizzas ON order_details.pizza_id = pizzas.pizza_id
GROUP BY day_of_week
ORDER BY total_revenue DESC;
```

| day_of_week | total_revenue | total_orders |
|---|---|---|
| Friday | 136,073.90 | 3538 |
| Thursday | 123,528.50 | 3239 |
| Saturday | 123,182.40 | 3158 |
| Wednesday | 114,408.40 | 3024 |
| Tuesday | 114,133.80 | 3009 |
| Monday | 107,329.55 | 2944 |
| Sunday | 99,203.50 | 2438 |

*Friday is the strongest day at $136,074 - 37% more revenue than Sunday at $99,204. Thursday and Saturday are closely matched as the second strongest days.*
