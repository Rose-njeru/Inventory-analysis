# Inventory-analysis
## Problem Statement

The survival of a large online retailer is at stake due to inaccurate inventory tracking causing lost sales and unhappy customers.

As a digital marketer, your task is to use SQL to track and analyze the inventory levels of the retailer’s eCommerce store to save the day!

```sql
CREATE TABLE products (
product_id SERIAL PRIMARY KEY,
product_name VARCHAR(50),
product_category VARCHAR(20),
product_price NUMERIC(10,2)
);
```
```sql
INSERT INTO products (product_name, product_category, product_price)
VALUES
('Product A', 'Category 1', 19.99),
('Product B', 'Category 2', 29.99),
('Product C', 'Category 1', 39.99),
('Product D', 'Category 3', 49.99),
('Product E', 'Category 2', 59.99);
```
```sql
CREATE TABLE inventory (
product_id INT,
inventory_date DATE,
inventory_level INT
);
```sql
INSERT INTO inventory (product_id, inventory_date, inventory_level)
VALUE
(1, '2022-01-01', 100),
(2, '2022-01-01', 200),
(3, '2022-01-01', 150),
(4, '2022-01-01', 75),
(5, '2022-01-01', 250),
(1, '2022-01-02', 80),
(2, '2022-01-02', 180),
(3, '2022-01-02', 100),
(4, '2022-01-02', 60),
(5, '2022-01-02', 220),
(1, '2022-01-03', 50),
(2, '2022-01-03', 150),
(3, '2022-01-03', 75),
(4, '2022-01-03', 80),
(5, '2022-01-03', 200);
```

**Question 1**
+ What are the top 5 products with the highest inventory levels on the most recent inventory date?
```sql
SELECT
product_name,
inventory_level
FROM products AS products
INNER JOIN inventory AS inventory
ON products.product_id=inventory.product_id
WHERE inventory_date=(SELECT MAX(inventory_date) FROM inventory)
ORDER BY inventory_level DESC 
LIMIT 5;
```

![image](https://user-images.githubusercontent.com/92436079/220821205-9918f90c-408e-40c5-bd31-691e6bdc553f.png)

**Question 2**
+ What is the total inventory level for each product category on the most recent inventory date?
```sql
SELECT 
product_category,
SUM(inventory_level) AS total_inventory_level
FROM products AS products
JOIN inventory AS inventory
ON products.product_id=inventory.product_id
WHERE inventory_date=(SELECT MAX(inventory_date) FROM inventory)
GROUP BY product_category;
```
![image](https://user-images.githubusercontent.com/92436079/220821274-63752eb1-441b-43c4-826a-c4452d73019c.png)

**Question 3**
+ What is the average inventory level for each product category for the month of January 2022?
``` sql
SELECT
product_category,
ROUND(AVG(inventory_level),2) AS Average_inventory_level
FROM products AS products
JOIN inventory AS inventory
ON products.product_id=inventory.product_id
WHERE inventory_date>='2022-01-01' AND inventory_date <'2022-01-31'
GROUP BY product_category;
``` 

![image](https://user-images.githubusercontent.com/92436079/220821331-680ea54d-5eba-4f6d-9bcf-7abe0cde4b90.png)

**Question 4**
+ Which products had a decrease in inventory level from the previous inventory date to the current inventory date?
```sql
SELECT
p.product_name,
p.product_id,
(a1.inventory_level-a2.inventory_level) AS difference
FROM  inventory AS a1
JOIN inventory AS a2
ON a1.product_id=a2.product_id
AND a1.inventory_date=date_add( a2.inventory_date, INTERVAL 1 DAY)
JOIN products AS p
ON a1.product_id =p.product_id
WHERE a1.inventory_level< a2.inventory_level;
```
![image](https://user-images.githubusercontent.com/92436079/220821395-3df663f5-da7d-4188-b8bc-1dae58234c53.png)

**Question 5**
+ What is the overall trend in inventory levels for each product category over the month of January 2022?
```sql
SELECT 
product_category,
inventory_date,
round(AVG(inventory_level),2) AS average_inventory_level
FROM products AS products
JOIN inventory AS inventory
ON products.product_id=inventory.product_id
WHERE inventory_date>='2022-01-01' AND inventory_date<'2022-01-31'
GROUP BY product_category,inventory_date
ORDER BY product_category,round(AVG(inventory_level),2) DESC;
```

![image](https://user-images.githubusercontent.com/92436079/220822160-acaa6295-744c-4cf5-9dad-9453b5dd09d4.png)
