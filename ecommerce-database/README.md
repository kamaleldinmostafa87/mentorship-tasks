# Ecommerce-Database

Create the DB schema script with the following entities

- Identify the relationships between entities
- Draw the ERD diagram of this sample schema
  g p
- Write an SQL query to generate a daily report of the total revenue for a specific date.
- Write an SQL query to generate a monthly report of the top-selling products in a given month.
- Write a SQL query to retrieve a list of customers who have placed orders

## Script:

```
CREATE SCHEMA ecommerce;

CREATE TABLE categories (
    category_id SERIAL PRIMARY KEY,
    category_name VARCHAR(30) NOT NULL,
    created_at TIMESTAMP DEFAULT NOW()
);

CREATE TABLE products (
    product_id SERIAL PRIMARY KEY,
    category_id INT REFERENCES ecommerce.categories(category_id),
    product_name VARCHAR(50) NOT NULL,
    description VARCHAR(255),
    price NUMERIC(10,2) NOT NULL,
    stock_quantity INT NOT NULL,
    created_at TIMESTAMP DEFAULT now()
);

CREATE TABLE customers (
    customer_id SERIAL PRIMARY KEY,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50),
    email VARCHAR(150) UNIQUE NOT NULL,
    password_hash TEXT NOT NULL,
    created_at TIMESTAMP DEFAULT NOW()
);

CREATE TABLE orders (
    order_id SERIAL PRIMARY KEY,
    customer_id INT NOT NULL REFERENCES ecommerce.customers(customer_id),
    order_date DATE NOT NULL DEFAULT CURRENT_DATE,
    total_amount NUMERIC(10,2) NOT NULL
);

CREATE TABLE order_details (
    order_detail_id SERIAL PRIMARY KEY,
    order_id INT REFERENCES ecommerce.orders(order_id),
    product_id INT REFERENCES ecommerce.products(product_id),
    quantity INT NOT NULL,
    unit_price NUMERIC(10,2) NOT NULL
);

```

## ERD Schema

![ERD](./erd-schema.png)

## The Relation between Entities:

- M:m products and orders
- M:m products and categories
- 1:m customers and orders

## A SQL query to generate a daily report of the total revenue for a specific date

```sql
select Sum(total_amount) AS today_sum GROUP BY order_date where order_date="2025-month-day" from "Ecommerce".orders;
```

## A SQL query to generate a monthly report of the top-selling products in a given month

```sql
Select products.product_id, products.product_name, Sum(order_details.quantity * order_details.unit_price) As top_selling_product
from order_details
join order_details.product_id on order_details.product_id = products.product_id
join orders on orders.order_id = order_details.order_id
where orders.order_date between  "2025-month-day" AND "2025-month-day"
Group By products.product_id, products.product_name
ORDER BY top_selling_product DESC
Limit 5;
```

## A SQL query to retrieve a list of customers who have placed orders totaling more than $500 in the past month.

Include customer names and their total order amounts

```sql
select Contact(first_name, last_name) AS full_name, Sum(total_amount)
from "Ecommerce".customers
JOIN "Ecommerce".orders ON orders.customer_id = customers.customer_id
Group by customers.customer_id
WHERE orders.order_date >= CURRENT_DATE - INTERVAL '1 month'
    AND orders.order_date < CURRENT_DATE
HAVING Sum(total_amount) > 500;
limit 5;
```
