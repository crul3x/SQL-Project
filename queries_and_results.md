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

| name         | pizza_id      | pizza_size | price |
|--------------|---------------|------------|-------|
| The Greek Pizza | the_greek_xxl | XXL          | 35.95      |

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
|------|--------|
| L    | 18526  |

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

| name                      | orders |
|---------------------------|--------|
| The Classic Deluxe Pizza   | 2416   |
| The Barbecue Chicken Pizza | 2372   |
| The Hawaiian Pizza        | 2370   |
| The Pepperoni Pizza       | 2369   |
| The Thai Chicken Pizza    | 2315   |

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
|----------|--------|
| Classic  | 14579  |
| Supreme  | 11777  |
| Veggie   | 11449  |
| Chicken  | 10815  |

## Q7: Determine the Distribution of Orders by Hour of the Day

```sql
SELECT 
    HOUR(order_time) AS hour, COUNT(order_time) AS orders
FROM
    orders
GROUP BY hour;
```

| hour | orders |
|------|--------|
| 9    | 1      |
| 10   | 8      |
| 11   | 1231   |
| 12   | 2520   |
| 13   | 2455   |
| 14   | 1472   |
| 15   | 1468   |
| 16   | 1920   |
| 17   | 2336   |
| 18   | 2399   |
| 19   | 2009   |
| 20   | 1642   |
| 21   | 1198   |
| 22   | 663    |
| 23   | 28     |


## Q8: Find the Category-wise Distribution of Pizzas

```sql
SELECT category, COUNT(name)
FROM pizza_types
GROUP BY category;
```

| category | count |
|----------|-------|
| Chicken  | 6     |
| Classic  | 8     |
| Supreme  | 9     |
| Veggie   | 9     |

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

| avg      |
|----------|
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

| pizza_type_id | name                     | revenue  |
|---------------|--------------------------|----------|
| thai_ckn      | The Thai Chicken Pizza    | 43434.2  |
| bbq_ckn       | The Barbecue Chicken Pizza | 42768    |
| cali_ckn      | The California Chicken Pizza | 41409.5 |

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
|----------|------------|
| Veggie   | 23.68      |
| Chicken  | 23.96      |
| Supreme  | 25.46      |
| Classic  | 26.91      |

