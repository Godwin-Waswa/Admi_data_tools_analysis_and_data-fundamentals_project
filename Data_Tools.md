# Online Store SQL Project

<a name="readme-top"></a>

<!-- TABLE OF CONTENTS -->

# 📗 Table of Contents

- [My SQL Project](#about-project)
- [📗 Table of Contents](#-table-of-contents)
- [📖 My SQL Project](#about-project)
  - [🛠 Built With ](#-built-with-)
    - [Tech Stack ](#tech-stack-)
    - [Key Features ](#key-features-)
    - [Data Dictionary](#Data_Dictionary)
    - [ERD Diagram](#ERD)
  - [💻 Getting Started ](#-getting-started-)
    - [Prerequisites](#prerequisites)
    - [Setup](#setup)
    - [Usage](#usage)
  - [👥 Authors ](#-authors-)
  - [🔭 Future Features ](#-future-features-)
  - [🤝 Contributing ](#-contributing-)

<!-- PROJECT DESCRIPTION -->

# 📖 My SQL Project <a name="about-project"></a>

**My SQL Project** is a simple Database that uses SQL, Postgres via Supabase and R to create, query and secure a **Bookstore** database.

## ✅ Project Overview

This project implements a simple **Online Store database** using **Supabase (PostgreSQL)**.  

The name of the schema is **Online_Store**.

The schema contains **3 main tables** with **sample data** and a **foreign key relationship**:  

- **customers** - Stores customer information.  
- **products** - Stores product details.  
- **orders** - Tracks purchases (linked to customers & products).  

The **orders** table acts as a joining table linking the `customers` and `products` tables. It contains foreign keys referencing both.

---

## 🎯 Project Purpose

The **Online Store Database** project was developed to demonstrate key database design and management concepts using **Supabase (PostgreSQL)**.  
Its main purpose is to model how an e-commerce platform manages **customers**, **products**, and **orders** in a structured, efficient, and relational way.

The database provides a foundation for:
- Storing and retrieving customer information securely.  
- Managing product listings, prices, and inventory levels.  
- Recording customer orders and linking them to purchased products.  
- Supporting business analytics such as total sales per product or per customer.  

This project showcases practical database design principles including **normalization**, **foreign key constraints**, and **referential integrity** while preparing the groundwork for future expansion into a full-stack e-commerce application.

---
## 🧩 Schema Overview

The **Online_Store** schema is designed to represent the core structure of a basic e-commerce system.  
It includes **three interrelated tables**; `customers`, `products`, and `orders`, each serving a distinct role in maintaining data consistency and enabling relational queries.

---

## 💡 Example Queries

Below are sample SQL queries tested in **Supabase SQL Editor** to demonstrate how the `Online_Store` database works.

---

### 🧍‍♂️ **1. List All Customers**

Retrieves all customer records from the `customers` table.

```sql
SELECT * FROM customers;
```
📘 This query displays all registered customers along with their contact details and registration date.

---

### 📦 **2. View Available Products**
Displays the list of all products and their current stock levels.

``` sql

SELECT name, category, price, stock_quantity
FROM products
ORDER BY category, name;
```
📘 This is useful for viewing the store’s catalog and inventory overview.

---

### 🧾 **3. View All Orders with Customer and Product Details**

The query fetches all orders joined with customer and product information.

```sql
SELECT 
    o.order_id,
    c.name AS customer_name,
    p.name AS product_name,
    o.quantity,
    o.total_price,
    o.order_date
FROM orders o
JOIN customers c ON o.customer_id = c.customer_id
JOIN products p ON o.product_id = p.product_id
ORDER BY o.order_date DESC;
```
📘 Displays complete transaction details with relational links.
---
### 💰 **4. Calculate Total Sales per Product**

The query shows how much revenue each product has generated based on all orders.

```sql

SELECT 
    p.name AS product_name,
    SUM(o.total_price) AS total_sales
FROM orders o
JOIN products p ON o.product_id = p.product_id
GROUP BY p.name
ORDER BY total_sales DESC;
```
📘 This helps in identifying top-performing products by total sales value.
--
### 👑 **5. Find the Top Customer by Total Spending**

This Determines which customer has spent the most money on orders.

``` sql

SELECT 
    c.name AS customer_name,
    SUM(o.total_price) AS total_spent
FROM orders o
JOIN customers c ON o.customer_id = c.customer_id
GROUP BY c.name
ORDER BY total_spent DESC
LIMIT 1;
```

## 🛠 Built With <a name="built-with"></a>

### Tech Stack <a name="tech-stack"></a>
- SQL
- Postgres DB

<!-- Features -->

### Key Features <a name="key-features"></a>

- [ ] **Tables**
- [ ] **Schema**
- [ ] **Access control**

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- GETTING STARTED -->

## 💻 Getting Started <a name="getting-started"></a>

To rebuild this DB, follow these steps.

### Prerequisites

To run this project, you need:
- [A Supabase account](https://supabase.com/)
- [Knowledge on SQL](https://www.w3schools.com/sql/)
- A schema for creating your tables in the DB

<!-- ### Setup -->
### Setup

Copy the contents of this Readme.md to your Project's file

OR

Clone this repository to your desired folder:

```sh
  git clone https://github.com/joyapisi/readme-template-data
  cd budget-app
```

<!-- ### DB Creation -->
### Install

- No installation required.

### Usage

- Open your Supabase project dashboard.

- Run the SQL scripts provided step by step guide below

### Live Demo

- No live demo provided by following step by step guide below will enable you excecute the project

### Database Schema

- The DB is made up of 3 tables. Each table has atleast 5 entries.
- To create the table, you will need a schema as shown below:

```sql
-- Drop old tables if they exist
DROP TABLE IF EXISTS orders CASCADE;
DROP TABLE IF EXISTS customers CASCADE;
DROP TABLE IF EXISTS products CASCADE;
```
## 🗂 Database Schema (`Online_Store.sql`)

### ``Creating Tables``

#### `TABLE: customers`

```sql
CREATE TABLE customers (
    customer_id SERIAL PRIMARY KEY,
    name TEXT NOT NULL,
    email TEXT UNIQUE NOT NULL,
    phone TEXT,
    created_at TIMESTAMP DEFAULT NOW()
);
```
---
#### `TABLE: products`
```sql
CREATE TABLE products (
    product_id SERIAL PRIMARY KEY,
    name TEXT NOT NULL,
    category TEXT NOT NULL,
    price DECIMAL(10,2) NOT NULL,
    stock_quantity INT NOT NULL
);
```
---
#### `TABLE: orders`
``` sql
CREATE TABLE orders (
    order_id SERIAL PRIMARY KEY,
    customer_id INT REFERENCES customers(customer_id) ON DELETE CASCADE,
    product_id INT REFERENCES products(product_id),
    order_date TIMESTAMP DEFAULT NOW(),
    quantity INT NOT NULL CHECK (quantity > 0),
    total_price DECIMAL(10,2) NOT NULL
);
```
---
### `Inserting Sample Data`

#### customers table
```sql
INSERT INTO customers (name, email, phone) VALUES
('Godwin Waswa', 'waswa@gmail.com', '0177788772'),
('Alice Wanyonyi', 'wanyony@gmail.com', '0112382342'),
('Charles Kinyua', 'kcharles@gmail.com', '0712344455'),
('Diana Bett', 'diana@gmail.com', '0231234832'),
('Blessings Onyango', 'onyango@yahoo.com', '0823462710'),
('Charles Omondi', 'omondi@gmail.com', '03212334432');
```
---
#### products table
```sql
INSERT INTO products (name, category, price, stock_quantity) VALUES
('Laptop', 'Electronics', 89566.00, 5),
('Smartphone', 'Electronics', 34000.50, 20),
('Office Chair', 'Furniture', 13000.00, 65),
('Coffee Maker', 'Appliances', 5000.00, 30),
('Headphones', 'Accessories', 8000.00, 52),
('Television', 'Electronics', 50000.00, 52);
```
---

#### orders table
```sql
INSERT INTO orders (customer_id, product_id, quantity) VALUES
(1, 1, 1),
(2, 2, 2),
(3, 3, 1),
(4, 4, 3),
(5, 5, 2);

```

- The Tables look like this in Supabase:
---
## ✅ Steps in Supabase

### Step 1 - Created a Project Called Online_Store

<img width="1600" height="860" alt="image" src="https://github.com/user-attachments/assets/32ba9d26-abbf-44a6-ae4a-32a7f5f9c0ea" />

---

### Step 2 - Created three tables
<img width="1600" height="600" alt="image" src="https://github.com/user-attachments/assets/a5b8b238-ff26-4a80-aa52-a807f3debfe2" />

### Step 3 - Inserted at least 5 rows per table.
<img width="1600" height="600" alt="image" src="https://github.com/user-attachments/assets/ca0b7e4c-243b-4698-b5c4-16dd6081d389" />

### Step 4 - Test queries in Supabase SQL editor.

*`List all customers`*

<img width="1600" height="600" alt="image" src="https://github.com/user-attachments/assets/a7671f23-f617-43e6-a618-79f25d8c8da1" />

*`List all Products`*

<img width="1600" height="600" alt="image" src="https://github.com/user-attachments/assets/1b73b530-1017-422a-b915-6624b4a0e37f" />

*`List all orders`*

<img width="1600" height="600" alt="image" src="https://github.com/user-attachments/assets/36bb052d-afec-49a8-b062-3b94e483e965" />

---

# 📘 Data Dictionary <a name="Data_Dictionary"></a>

This **Data Dictionary** describes the structure of the **Online_Store** database, detailing each table, its columns, data types, and purpose.  
The database consists of **three main tables** — `customers`, `products`, and `orders` — with defined relationships that ensure referential integrity and support business operations.

---

## 🧍‍♂️ Table 1: `customers`

Stores essential customer information used for transactions and communication.

| Column Name | Data Type | Constraints | Description |
|--------------|------------|--------------|--------------|
| `customer_id` | SERIAL | PRIMARY KEY | Unique identifier for each customer |
| `name` | TEXT | NOT NULL | Full name of the customer |
| `email` | TEXT | UNIQUE, NOT NULL | Customer email address (used for login/communication) |
| `phone` | TEXT | NULL | Customer contact phone number |
| `created_at` | TIMESTAMP | DEFAULT NOW() | Timestamp when the customer was added |

**Purpose:**  
Holds unique customer data. Serves as the parent table for orders placed in the system.  

**Relationships:**  
Referenced by `orders.customer_id` (One-to-Many relationship).

---

## 📦 Table 2: `products`

Contains product catalog information including pricing and stock quantity.

| Column Name | Data Type | Constraints | Description |
|--------------|------------|--------------|--------------|
| `product_id` | SERIAL | PRIMARY KEY | Unique identifier for each product |
| `name` | TEXT | NOT NULL | Product name |
| `category` | TEXT | NOT NULL | Product category (e.g., Electronics, Furniture, Appliances) |
| `price` | DECIMAL(10,2) | NOT NULL | Price per unit of the product |
| `stock_quantity` | INT | NOT NULL | Current quantity in stock |

**Purpose:**  
Stores all available products in the online store. Used to manage stock levels and pricing.  

**Relationships:**  
Referenced by `orders.product_id` (One-to-Many relationship).

---

## 🧾 Table 3: `orders`

Links customers to the products they purchase. Each record represents a single order transaction.

| Column Name | Data Type | Constraints | Description |
|--------------|------------|--------------|--------------|
| `order_id` | SERIAL | PRIMARY KEY | Unique identifier for each order |
| `customer_id` | INT | FOREIGN KEY → customers(customer_id) | ID of the customer placing the order |
| `product_id` | INT | FOREIGN KEY → products(product_id) | ID of the product purchased |
| `order_date` | TIMESTAMP | DEFAULT NOW() | Date and time when the order was made |
| `quantity` | INT | NOT NULL, CHECK (quantity > 0) | Number of items ordered |
| `total_price` | DECIMAL(10,2) | NOT NULL | Total cost of the order (product price × quantity) |

**Purpose:**  
Acts as a linking table that records transactions and connects customers to purchased products.  

**Relationships:**  
- `customer_id` → references `customers(customer_id)`  
- `product_id` → references `products(product_id)`  
(Both form **foreign key** relationships.)

---

## 🧠 Relationships Summary

| Relationship | Type | Description |
|---------------|------|--------------|
| `customers` → `orders` | One-to-Many | A customer can have multiple orders |
| `products` → `orders` | One-to-Many | A product can appear in multiple orders |
| `orders` | Junction | Connects customers and products |

---

# 🧩 `Entity-Relationship Diagram (ERD)` <a name="ERD"></a>

An **Entity-Relationship Diagram (ERD)** is a visual representation of a database structure that shows how different entities (tables) relate to each other. It helps developers and database designers understand the logical flow of data and the relationships between various components.

In an ERD:
- **Entities** represent database tables (e.g., `customers`, `products`, `orders`).
- **Attributes** represent columns or fields within those tables (e.g., `customer_id`, `name`).
- **Relationships** illustrate how entities are linked using **Primary Keys (PK)** and **Foreign Keys (FK)**.

ERDs are essential for:
- Designing and normalizing databases.
- Communicating database architecture clearly.
- Maintaining consistency and avoiding redundancy.

## `Online-Store ERD Diagram`

<img width="1600" height="900" alt="image" src="https://github.com/user-attachments/assets/439abfe6-40c7-4e98-b28e-88c85d5754d9" />

---

- To test the table, I used five queries as listed below: 


### 🧍‍♂️ **1. List All Customers**

Retrieves all customer records from the `customers` table.

```sql
SELECT * FROM customers;
```
📘 This query displays all registered customers along with their contact details and registration date.

---

### 📦 **2. View Available Products**
Displays the list of all products and their current stock levels.

``` sql

SELECT name, category, price, stock_quantity
FROM products
ORDER BY category, name;
```
📘 This is useful for viewing the store’s catalog and inventory overview.

---

### 🧾 **3. View All Orders with Customer and Product Details**

The query fetches all orders joined with customer and product information.

```sql
SELECT 
    o.order_id,
    c.name AS customer_name,
    p.name AS product_name,
    o.quantity,
    o.total_price,
    o.order_date
FROM orders o
JOIN customers c ON o.customer_id = c.customer_id
JOIN products p ON o.product_id = p.product_id
ORDER BY o.order_date DESC;
```
📘 Displays complete transaction details with relational links.
---
### 💰 **4. Calculate Total Sales per Product**

The query shows how much revenue each product has generated based on all orders.

```sql

SELECT 
    p.name AS product_name,
    SUM(o.total_price) AS total_sales
FROM orders o
JOIN products p ON o.product_id = p.product_id
GROUP BY p.name
ORDER BY total_sales DESC;
```
📘 This helps in identifying top-performing products by total sales value.
--
### 👑 **5. Find the Top Customer by Total Spending**

This Determines which customer has spent the most money on orders.

``` sql

SELECT 
    c.name AS customer_name,
    SUM(o.total_price) AS total_spent
FROM orders o
JOIN customers c ON o.customer_id = c.customer_id
GROUP BY c.name
ORDER BY total_spent DESC
LIMIT 1;
```
- Here are the results of the queries:
<img width="1591" height="736" alt="image" src="https://github.com/user-attachments/assets/4dbdedae-8963-407a-a4ef-fab7312a6dc9" />

---

<img width="1595" height="728" alt="image" src="https://github.com/user-attachments/assets/ff581876-f021-4ec3-b52c-4fab15011751" />

---

<img width="1588" height="709" alt="image" src="https://github.com/user-attachments/assets/29765026-0ee2-4cb4-9cc0-b401d278b738" />

---

<img width="1592" height="730" alt="image" src="https://github.com/user-attachments/assets/5f364298-810f-4c54-814e-14d64b2aaa95" />

---

<img width="1596" height="699" alt="image" src="https://github.com/user-attachments/assets/ff378d39-d57d-4660-9bd7-899558ed2e27" />


<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- AUTHORS -->

## 👥 Authors <a name="authors"></a>

👤 **Godwin Wafula Waswa**

- GitHub: [@Godwin-Waswa](https://github.com/Godwin-Waswa)
- Twitter: [@Godwin_Waswa](https://twitter.com/Godwin_Waswa)
- LinkedIn: [@Godwin-Waswa](https://www.linkedin.com/in/godwin-waswa-170307123/)

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- FUTURE FEATURES -->

## 🔭 Future Features <a name="future-features"></a>

- [ ] **Add security**
- [ ] **Link DB to R for visualisation purposes and further analyses**

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- CONTRIBUTING -->

## 🤝 Contributing <a name="contributing"></a>

Contributions, issues, and feature requests are welcome!

Feel free to check the [issues page](../../issues/).

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- SUPPORT -->
