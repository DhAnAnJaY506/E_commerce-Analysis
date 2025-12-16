## E_commerce-Analysis

```sql
CREATE TABLE E_Commerce(
	user_id Text PRIMARY KEY,
	product_id VARCHAR(50),
	category VARCHAR(50),
	price FLOAT,
	discount INT,
	final_price FLOAT,
	payment_method VARCHAR(50),
	purchase DATE
);
```

```sql
SELECT * FROM E_Commerce;
```

1. What is the total revenue generated from all purchases (using final_price)?
```sql
SELECT
	ROUND(SUM(final_price)::Numeric,2) AS total_revenue
FROM E_Commerce;
```

2. What is the average order value per user?
```sql
SELECT
	user_id,
	AVG(final_price) AS avg_order_value
FROM E_Commerce
GROUP BY 1;
```

3.Which category generates the highest total revenue?
```sql
SELECT
	Category,
	SUM(final_price) AS total_revenue
FROM E_Commerce
GROUP BY 1F;
```

4.What is the monthly revenue trend based on purchase date?
```sql
ALTER TABLE E_Commerce
ADD COLUMN month VARCHAR(50);
```

```sql
UPDATE E_Commerce
SET month = TO_CHAR(purchase, 'MONTH');
```

```sql
SELECT
	month,
	SUM(final_price) AS total_revenue
FROM E_Commerce
GROUP BY 1;
```

5. Which product_id has the highest total sales value?
```sql
ALTER TABLE E_Commerce
ADD COLUMN sales FLOAT;
```

```sql
UPDATE E_Commerce
SET sales = final_price - discount;
```

```sql
SELECT
	product_id,
	SUM(sales) AS total_sales
FROM E_Commerce
GROUP BY 1
ORDER BY 2 DESC
LIMIT 10;
```

6. What is the average discount percentage applied per category?
```sql
SELECT
	category,
	AVG(discount) AS avg_discount
FROM E_Commerce
GROUP BY 1;
```

7. How does discount level impact final_price (low, medium, high discount buckets)?
```sql
SELECT
    CASE
        WHEN discount = 0 THEN 'No Discount'
        WHEN discount BETWEEN 1 AND 10 THEN 'Low Discount'
        WHEN discount BETWEEN 11 AND 30 THEN 'Medium Discount'
        ELSE 'High Discount'
    END AS discount_level,
    COUNT(*) AS total_orders,
    ROUND(AVG(final_price)::NUMERIC, 2) AS avg_final_price
FROM e_commerce
GROUP BY discount_level
ORDER BY total_orders DESC;
```

8. Which category offers the maximum total discount amount?
```sql
SELECT
    category,
    SUM(discount) AS total_discount
FROM e_commerce
GROUP BY category
ORDER BY total_discount DESC
LIMIT 1;
```

9. Who are the top 5 users by total spending?
```sql
SELECT
    user_id,
    ROUND(SUM(final_price)::NUMERIC, 2) AS total_spent
FROM e_commerce
GROUP BY user_id
ORDER BY total_spent DESC
LIMIT 5;
```

10.What is the average spending per user per month?
```sql
SELECT
    user_id,
    month,
    ROUND(AVG(final_price)::NUMERIC, 2) AS avg_spending
FROM e_commerce
GROUP BY user_id, month
ORDER BY user_id, month;
```

11. Which payment method is most frequently used?
```sql
SELECT
    payment_method,
    COUNT(*) AS usage_count
FROM e_commerce
GROUP BY payment_method
ORDER BY usage_count DESC
LIMIT 1;
```

12. What is the total revenue by payment method?
```sql
SELECT
    payment_method,
    ROUND(SUM(final_price)::NUMERIC, 2) AS total_revenue
FROM e_commerce
GROUP BY payment_method
ORDER BY total_revenue DESC;
```

 13.Which categor shows high sales volume but low average final_price, indicating low-margin items?
```sql
SELECT
    category,
    COUNT(*) AS sales_volume,
    ROUND(AVG(final_price)::NUMERIC, 2) AS avg_final_price
FROM e_commerce
GROUP BY category
ORDER BY sales_volume DESC, avg_final_price ASC;
```
