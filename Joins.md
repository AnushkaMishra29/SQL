### Q1. Find all customers who have purchased every active product in the Electronics category at least once. Return the customer ID, customer name, and the number of distinct active Electronics products they purchased.
<img width="1562" height="807" alt="image" src="https://github.com/user-attachments/assets/e9cda7da-16f0-4db6-9350-c51092307386" />


### Asnswer:
SELECT
    c.customer_id,
    c.customer_name,
    COUNT(DISTINCT p.product_id) AS products_purchased
FROM customers AS c
JOIN purchases AS p
    ON c.customer_id = p.customer_id
JOIN products AS pr
    ON p.product_id = pr.product_id
WHERE p.purchase_status = 'completed'
  AND pr.category = 'Electronics'
  AND pr.is_active = TRUE
GROUP BY
    c.customer_id,
    c.customer_name
HAVING COUNT(DISTINCT p.product_id) = (
    SELECT COUNT(*)
    FROM products
    WHERE category = 'Electronics'
      AND is_active = TRUE
)
ORDER BY
    c.customer_id;



### Q2. For each product category, find the top two products by total completed sales revenue during July 2026. If multiple products have the same revenue, assign them the same position. Return every product whose position is 1 or 2.
<img width="1062" height="810" alt="image" src="https://github.com/user-attachments/assets/3c7e002d-ccb6-4436-8855-8bcd5c044d14" />

### Answer:

WITH product_revenue AS (
    -- Calculate total July revenue separately for every product
    SELECT
        p.category,
        p.product_id,
        p.product_name,
        SUM(oi.quantity * oi.unit_price) AS total_revenue
    FROM products AS p
    JOIN order_items AS oi
        ON p.product_id = oi.product_id
    JOIN orders AS o
        ON oi.order_id = o.order_id
    WHERE o.order_status = 'completed'
      AND o.order_date >= DATE '2026-07-01'
      AND o.order_date < DATE '2026-08-01'
    GROUP BY
        p.category,
        p.product_id,
        p.product_name
),

ranked_products AS (
    -- Rank products by total revenue within each category
    SELECT
        category,
        product_id,
        product_name,
        total_revenue,
        DENSE_RANK() OVER (
            PARTITION BY category
            ORDER BY total_revenue DESC
        ) AS revenue_position
    FROM product_revenue
)

SELECT
    category,
    product_id,
    product_name,
    total_revenue,
    revenue_position
FROM ranked_products
WHERE revenue_position <= 2
ORDER BY
    category,
    revenue_position,
    product_id;
