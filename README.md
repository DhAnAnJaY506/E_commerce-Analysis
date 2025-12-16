## E_commerce-Analysis

# Overview

* Your E_Commerce dataset contains the following columns:
	* user_id, product_id, category, price, discount, final_price, payment_method, purchase
	* You calculated additional metrics:
	* sales = final_price - discount
	* month extracted from purchase
* The project includes key analytical areas:
* Revenue analysis (total revenue, revenue by category/payment method)
* Sales analysis (sales per product, average order per user)
* Discount impact (average discount, discount buckets, total discount by category)
* Customer behavior (top users by spending, repeat purchases, monthly spending)
* Payment method trends (frequency and revenue)
* Low-margin products/categories (high volume, low price)

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

# Key Insights

* Revenue & Sales
	* Total revenue gives an overview of earnings from all purchases.
	* Certain categories generate the highest revenue, indicating best-selling product lines.
	* Product-level analysis shows top 10 products by sales, helping identify high-demand items.

* Discount Impact
	* Average discount per category shows where price incentives are concentrated.
	* Discount buckets (low, medium, high) indicate how final price reacts to discount levels.
	* Categories offering maximum total discount could be reducing margins, highlighting areas for pricing optimization.

* Customer Behavior
	* Top 5 users by total spending reveal high-value customers.
	* Average spending per user per month highlights loyal or repeat buyers.

* Payment Methods
	* Most frequently used payment methods show customer preferences.
	* Revenue per payment method helps in assessing the most profitable payment channels
	* Low-Margin, High-Volume Products
	* Categories with high sales volume but low average final_price indicate low-margin items.
	* These insights help prioritize products for promotion or cost optimization.
   
# Conclusions

* The business generates most revenue from specific categories and top-selling products, which should be maintained or promoted further.
* Discounts play a key role in sales volume; high discounts increase order count but may reduce margins.
* Identifying top-spending customers enables targeted loyalty programs and marketing campaigns.
* Payment method analysis can guide partnerships with payment providers or promotional offers.
* Low-margin, high-volume categories are strategic for volume sales but require careful pricing or cost management.
