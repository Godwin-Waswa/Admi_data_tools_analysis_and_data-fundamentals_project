# Online Store — Data Analysis Project

## Project Title
Online Store — Data Analysis with RStudio Cloud & Supabase

## Overview
This repository contains a 4-week data analysis project that uses:
- **RStudio Cloud (Posit Cloud)** for development,
- **Supabase (Postgres)** as the database backend,
- **R** with **DBI**, **RPostgres**, **dplyr**, and **ggplot2** for analysis and visualization.

The dataset used is the **Online_Store** schema from the Data Tools final project. It contains three tables: `customers`, `products`, and `orders` (each with at least 5 rows), with clear foreign key relationships.

## Repo structure

├── README.md <- This file (project overview + instructions)

├── data_dictionary.md <- Data dictionary for the three tables

├── analysis.R <- R script: connects to Supabase, runs analysis, saves plots

├── figures/ <- Output folder for PNG figures (created by analysis.R)

└── sql/

├── schema.sql <- (optional) CREATE TABLE statements / triggers

└── sample_queries.sql <- example SQL queries used for testing
---
## 🛠 Built With <a name="built-with"></a>

### Tech Stack <a name="tech-stack"></a>

<details>
  <summary>Client</summary>
  <ul>
    <li><a href="https://posit.cloud/">RStudio Cloud (Posit Cloud)</a></li>
  </ul>
</details>

<details>
  <summary>Server</summary>
  <ul>
    <li><a href="https://supabase.com/">Supabase Backend</a></li>
  </ul>
</details>

<details>
  <summary>Database</summary>
  <ul>
    <li><a href="https://www.postgresql.org/">PostgreSQL</a></li>
  </ul>
</details>

---
### Key Features <a name="key-features"></a>

- **Seamless Connection** between RStudio Cloud and Supabase PostgreSQL  
- **Data Analysis in R** using `dplyr` and `DBI`  
- **Interactive Visualizations** using `ggplot2`  
- **Structured SQL Database** with clear relationships and constraints  

<p align="right">(<a href="#readme-top">back to top</a>)</p>

---

## 🚀 Live Demo <a name="live-demo"></a>

- [RStudio Project Workspace](https://posit.cloud/)
- [Supabase Console](https://supabase.com/)

<p align="right">(<a href="#readme-top">back to top</a>)</p>

---

## 💻 Getting Started <a name="getting-started"></a>

To get a local copy up and running, follow these simple steps.

### Prerequisites

In order to run this project, you need the following tools:

- A Supabase account with a running PostgreSQL database  
- RStudio Cloud (https://posit.cloud)  
- Installed R packages: `DBI`, `RPostgres`, `dplyr`, `ggplot2`

### Setup

Clone this repository to your desired folder:

```bash
  git clone https://github.com/yourusername/data-analysis-rstudio-supabase.git
  cd data-analysis-rstudio-supabase
```
### How to run the analysis
1. Open this repo in **RStudio Cloud** (or locally).
2. Install packages (first run only):

```r
install.packages(c("DBI","RPostgres","dplyr","ggplot2"))
```

### Install

Install the required R packages in your RStudio Cloud console:

```r
install.packages("DBI")
install.packages("RPostgres")
install.packages("dplyr")
install.packages("ggplot2")
```
## `data_dictionary.md`

# Data Dictionary — Online_Store

This document describes the three tables used in the Online_Store schema.

---

## Table: customers

| Column       | Type         | Constraints                | Description                         |
|--------------|--------------|----------------------------|-------------------------------------|
| customer_id  | SERIAL       | PRIMARY KEY                | Unique customer identifier          |
| name         | TEXT         | NOT NULL                   | Full name of the customer           |
| email        | TEXT         | UNIQUE, NOT NULL           | Email address                       |
| phone        | TEXT         | NULL                       | Contact phone number                |
| created_at   | TIMESTAMP    | DEFAULT NOW()              | Record creation timestamp           |

**Purpose:** Stores customer information used for orders and contact.

---

## Table: products

| Column         | Type            | Constraints          | Description                          |
|----------------|------------------|----------------------|--------------------------------------|
| product_id     | SERIAL           | PRIMARY KEY          | Unique product identifier            |
| name           | TEXT             | NOT NULL             | Product name                         |
| category       | TEXT             | NOT NULL             | Category (Electronics, Furniture...) |
| price          | DECIMAL(10,2)    | NOT NULL             | Price per unit                       |
| stock_quantity | INT              | NOT NULL             | Current stock count                  |

**Purpose:** Catalog of available products, pricing and inventory counts.

---

## Table: orders

| Column        | Type            | Constraints                            | Description                                  |
|---------------|------------------|----------------------------------------|----------------------------------------------|
| order_id      | SERIAL           | PRIMARY KEY                            | Unique order identifier                      |
| customer_id   | INT              | FOREIGN KEY -> customers(customer_id)  | Customer who made the order                  |
| product_id    | INT              | FOREIGN KEY -> products(product_id)    | Product purchased                             |
| order_date    | TIMESTAMP        | DEFAULT NOW()                           | Date and time of the order                    |
| quantity      | INT              | NOT NULL CHECK (quantity > 0)          | Number of units ordered                       |
| total_price   | DECIMAL(10,2)    | NOT NULL                               | Total price (product price × quantity)        |

**Purpose:** Links customers and products to record transactions. Acts as the junction table.

---

## Relationships summary

- `customers.customer_id` (1) ← `orders.customer_id` (many)  
- `products.product_id` (1) ← `orders.product_id` (many)

---

## Notes
- `total_price` should equal `price * quantity`. If this is not guaranteed by the DB, compute and validate in ETL/analysis or add a DB trigger to enforce it.
- Consider adding `order_status` in future (pending, shipped, delivered).

**`analysis.R`**
```r
# Online Store Data Analysis
#
# Requirements:
#  - Create a .Renviron or set environment variables:
#    SUPABASE_HOST, SUPABASE_DBNAME, SUPABASE_PORT, SUPABASE_USER, SUPABASE_PASSWORD
#
# Run in RStudio Cloud (Posit) or locally.

library(DBI)
library(RPostgres)
library(dplyr)
library(ggplot2)

# -------------------------
# Database connection setup
# -------------------------
host <- Sys.getenv("SUPABASE_HOST")
dbname <- Sys.getenv("SUPABASE_DBNAME", unset = "postgres")
port <- as.integer(Sys.getenv("SUPABASE_PORT", unset = "5432"))
user <- Sys.getenv("SUPABASE_USER")
password <- Sys.getenv("SUPABASE_PASSWORD")

if (host == "" || user == "" || password == "") {
  stop("Please set SUPABASE_HOST, SUPABASE_USER, and SUPABASE_PASSWORD in .Renviron or environment.")
}

con <- dbConnect(
  RPostgres::Postgres(),
  host = host,
  port = port,
  dbname = dbname,
  user = user,
  password = password,
  sslmode = "require"
)

# List tables (sanity check)
print("Tables in database:")
print(dbListTables(con))

# -------------------------
# Load tables into R
# -------------------------
customers <- dbGetQuery(con, "SELECT * FROM customers;")
products  <- dbGetQuery(con, "SELECT * FROM products;")
orders    <- dbGetQuery(con, "SELECT * FROM orders;")

# Basic sanity checks
cat("Rows — customers:", nrow(customers), " products:", nrow(products), " orders:", nrow(orders), "\n")

# -------------------------
# Data cleaning / compute missing total_price
# -------------------------
# If total_price is NULL or 0, compute price * quantity by joining products.
orders_clean <- orders %>%
  left_join(products %>% select(product_id, price), by = "product_id") %>%
  mutate(
    computed_total = round(as.numeric(price) * as.numeric(quantity), 2),
    total_price = ifelse(is.na(total_price) | total_price == 0, computed_total, as.numeric(total_price))
  ) %>%
  select(-price, -computed_total)

# If any total_price still NA, warn
if (any(is.na(orders_clean$total_price))) {
  warning("Some orders have NA total_price after computation. Inspect orders table.")
}

# -------------------------
# Analysis 1: Total sales by product
# -------------------------
sales_by_product <- orders_clean %>%
  group_by(product_id) %>%
  summarise(
    total_quantity = sum(quantity, na.rm = TRUE),
    total_sales = sum(total_price, na.rm = TRUE)
  ) %>%
  left_join(products %>% select(product_id, name, category), by = "product_id") %>%
  arrange(desc(total_sales))

print("Top sales by product:")
print(sales_by_product)

# Plot: total sales by product
if (!dir.exists("figures")) dir.create("figures")
p1 <- ggplot(sales_by_product, aes(x = reorder(name, total_sales), y = total_sales)) +
  geom_col() +
  coord_flip() +
  labs(title = "Total Sales by Product", x = "Product", y = "Total Sales (currency)") +
  theme_minimal()

ggsave("figures/total_sales_by_product.png", plot = p1, width = 8, height = 5)

# -------------------------
# Analysis 2: Top customers by spending
# -------------------------
customer_spending <- orders_clean %>%
  group_by(customer_id) %>%
  summarise(total_spent = sum(total_price, na.rm = TRUE), orders_count = n()) %>%
  left_join(customers %>% select(customer_id, name, email), by = "customer_id") %>%
  arrange(desc(total_spent))

print("Top customers by spending:")
print(customer_spending)

# Plot: top customers (top 10)
top_customers <- customer_spending %>% top_n(10, wt = total_spent)

p2 <- ggplot(top_customers, aes(x = reorder(name, total_spent), y = total_spent)) +
  geom_col() +
  coord_flip() +
  labs(title = "Top Customers by Total Spent", x = "Customer", y = "Total Spent (currency)") +
  theme_minimal()

ggsave("figures/top_customers.png", plot = p2, width = 8, height = 5)

# -------------------------
# Analysis 3: Orders over time
# -------------------------
# Convert order_date to Date if needed
orders_clean <- orders_clean %>%
  mutate(order_date = as.POSIXct(order_date, tz = "UTC"))

orders_trend <- orders_clean %>%
  mutate(order_day = as.Date(order_date)) %>%
  group_by(order_day) %>%
  summarise(daily_orders = n(), daily_sales = sum(total_price, na.rm = TRUE)) %>%
  arrange(order_day)

print("Orders over time (by day):")
print(orders_trend)

p3 <- ggplot(orders_trend, aes(x = order_day, y = daily_sales)) +
  geom_line() +
  geom_point() +
  labs(title = "Daily Sales Over Time", x = "Date", y = "Daily Sales (currency)") +
  theme_minimal()

ggsave("figures/orders_over_time.png", plot = p3, width = 8, height = 4)

# -------------------------
# Analysis 4: Inventory check (low stock)
# -------------------------
low_stock_threshold <- 10
low_stock <- products %>%
  filter(stock_quantity <= low_stock_threshold) %>%
  arrange(stock_quantity)

print("Low stock products (<= 10):")
print(low_stock)

# Save summary tables to CSV for quick inspection (optional)
write.csv(sales_by_product, "figures/sales_by_product.csv", row.names = FALSE)
write.csv(customer_spending, "figures/customer_spending.csv", row.names = FALSE)

# -------------------------
# Close connection
# -------------------------
dbDisconnect(con)

cat("Analysis complete. Figures saved to figures/ (total_sales_by_product.png, top_customers.png, orders_over_time.png)\n")
```
---
**`Optional SQL(trigger to maintain total_price)`**
```sql
-- Example: trigger to auto-set orders.total_price on INSERT/UPDATE
-- (Run this in Supabase SQL editor; test on a dev DB first.)

-- 1. ensure plpgsql is available (it normally is)
-- 2. create the function
CREATE OR REPLACE FUNCTION update_total_price()
RETURNS TRIGGER AS $$
BEGIN
  -- lookup product price
  SELECT price INTO STRICT NEW.total_price
  FROM products
  WHERE product_id = NEW.product_id;

  IF NEW.total_price IS NULL THEN
    RAISE EXCEPTION 'Product price not found for product_id %', NEW.product_id;
  END IF;

  -- multiply by quantity
  NEW.total_price := (SELECT price FROM products WHERE product_id = NEW.product_id) * NEW.quantity;
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

-- 3. create trigger
DROP TRIGGER IF EXISTS trg_update_total_price ON orders;
CREATE TRIGGER trg_update_total_price
BEFORE INSERT OR UPDATE ON orders
FOR EACH ROW
EXECUTE FUNCTION update_total_price();
```
# Example SQL queries file `sql/sample_queries.sql`

```sql
-- List all customers
SELECT * FROM customers ORDER BY customer_id;

-- List all products with stock
SELECT product_id, name, category, price, stock_quantity FROM products ORDER BY category, name;

-- View all orders with customer and product details
SELECT o.order_id, o.order_date, c.name AS customer_name, p.name AS product_name,
       o.quantity, o.total_price
FROM orders o
JOIN customers c ON o.customer_id = c.customer_id
JOIN products p ON o.product_id = p.product_id
ORDER BY o.order_date DESC;

-- Total sales per product
SELECT p.name AS product_name, SUM(o.total_price) AS total_sales
FROM orders o
JOIN products p ON o.product_id = p.product_id
GROUP BY p.name
ORDER BY total_sales DESC;

-- Top customer by total spending
SELECT c.name AS customer_name, SUM(o.total_price) AS total_spent
FROM orders o
JOIN customers c ON o.customer_id = c.customer_id
GROUP BY c.name
ORDER BY total_spent DESC
LIMIT 1;
```
# Usage

Connect R to your Supabase PostgreSQL database:

```r
library(DBI)
library(RPostgres)

con <- dbConnect(
  RPostgres::Postgres(),
  host = "yourproject.supabase.co",
  port = 5432,
  dbname = "postgres",
  user = "postgres",
  password = "your_real_password",
  sslmode = "require"
)

dbListTables(con)
```
Fetch and explore your data:

```r
library(dplyr)

sales_data <- dbGetQuery(con, "SELECT * FROM sales;")
head(sales_data)
```
Perform analysis:

```r
sales_summary <- sales_data %>%
  group_by(product_id) %>%
  summarise(total_sales = sum(quantity * price))
```
Visualize insights:
```r
library(ggplot2)

ggplot(sales_summary, aes(x = product_id, y = total_sales, fill = product_id)) +
  geom_bar(stat = "identity") +
  theme_minimal() +
  labs(title = "Total Sales per Product")
```
# Run tests
You can validate your connection and queries by running:
```r
dbGetQuery(con, "SELECT COUNT(*) FROM products;")
```
# Deployment

You can deploy or publish this project using GitHub.
Push your R files and documentation to your repository and create a pull request for review.
```r
git add .
git commit -m "Initial RStudio Data Analysis project"
git push origin analysis-branch
```
<p align="right">(<a href="#readme-top">back to top</a>)</p>

## 👥 Authors <a name="authors"></a>

👤 **Godwin Wafula Waswa**

- GitHub: [@Godwin-Waswa](https://github.com/Godwin-Waswa)
- Twitter: [@Godwin_Waswa](https://twitter.com/Godwin_Waswa)
- LinkedIn: [@Godwin-Waswa](https://www.linkedin.com/in/godwin-waswa-170307123/)

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## 🔭 Future Features <a name="future-features"></a>

- [ ] **Add predictive analysis for sales forecasting using `caret`**
- [ ] **Generate automated R Markdown PDF reports**
- [ ] **Build a live dashboard using `Shiny`**

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## 🤝 Contributing <a name="contributing"></a>

Contributions, issues, and feature requests are welcome!

Feel free to check the [issues page](../../issues/).

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## ⭐️ Show your support <a name="support"></a>

If you found this project helpful, give it a ⭐️ and share it with your peers learning R + Supabase integration.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## 🙏 Acknowledgements <a name="acknowledgements"></a>

Special thanks to:

- KCB Data Tools Course Instructors

- Supabase Team for their free cloud PostgreSQL

- Posit (RStudio Cloud) for enabling cloud-based R analytics

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## ❓ FAQ <a name="faq"></a>

- Can I use a different database than Supabase?

  Yes, any PostgreSQL-compatible database will work.

- Is RStudio Cloud free?

  Yes, Posit Cloud offers a free tier for education and light usage.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## 📝 License <a name="license"></a>

This project is MIT
 licensed.
You may freely reuse and modify with attribution.

<p align="right">(<a href="#readme-top">back to top</a>)</p>
