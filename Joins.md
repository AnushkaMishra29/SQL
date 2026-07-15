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
