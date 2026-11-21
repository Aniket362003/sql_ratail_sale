# Retail Sales Analysis (SQL)

A beginner-level SQL project exploring retail sales data through data cleaning, exploratory analysis, and business-focused queries.

## Project Overview

- **Database:** `p1_retail_db`
- **Table:** `retail_sales`
- **Tools:** SQL (PostgreSQL)

## Objectives

- Create and explore a retail sales database.
- Identify and handle missing data.
- Analyze sales, customers, product categories, and trends.
- Answer business questions using SQL.

## Database Setup

```sql
CREATE DATABASE p1_retail_db;

CREATE TABLE retail_sales (
    transactions_id INT PRIMARY KEY,
    sale_date DATE,
    sale_time TIME,
    customer_id INT,
    gender VARCHAR(10),
    age INT,
    category VARCHAR(35),
    quantity INT,
    price_per_unit FLOAT,
    cogs FLOAT,
    total_sale FLOAT
);
```

## Data Exploration and Cleaning

```sql
-- Total records
SELECT COUNT(*) FROM retail_sales;

-- Unique customers
SELECT COUNT(DISTINCT customer_id) FROM retail_sales;

-- Product categories
SELECT DISTINCT category FROM retail_sales;

-- Find records with missing values
SELECT *
FROM retail_sales
WHERE sale_date IS NULL
   OR sale_time IS NULL
   OR customer_id IS NULL
   OR gender IS NULL
   OR age IS NULL
   OR category IS NULL
   OR quantity IS NULL
   OR price_per_unit IS NULL
   OR cogs IS NULL;

-- Remove records with missing values
DELETE FROM retail_sales
WHERE sale_date IS NULL
   OR sale_time IS NULL
   OR customer_id IS NULL
   OR gender IS NULL
   OR age IS NULL
   OR category IS NULL
   OR quantity IS NULL
   OR price_per_unit IS NULL
   OR cogs IS NULL;
```

## Business Questions and SQL Analysis


### 1. Retrieve sales made on November 5, 2022
```sql
SELECT *
FROM retail_sales
WHERE sale_date = '2022-11-05';
```

### 2. Find clothing transactions with a quantity greater than 4 in November 2022
```sql
SELECT *
FROM retail_sales
WHERE category = 'Clothing'
  AND TO_CHAR(sale_date, 'YYYY-MM') = '2022-11'
  AND quantity > 4;
```

### 3. Calculate total sales and order count for each category
```sql
SELECT
    category,
    SUM(total_sale) AS net_sales,
    COUNT(*) AS total_orders
FROM retail_sales
GROUP BY category;
```

### 4. Find the average age of customers who purchased from the Beauty category
```sql
SELECT ROUND(AVG(age), 2) AS average_age
FROM retail_sales
WHERE category = 'Beauty';
```

### 5. Find transactions with sales greater than 1,000
```sql
SELECT *
FROM retail_sales
WHERE total_sale > 1000;
```

### 6. Count transactions by gender for each category
```sql
SELECT
    category,
    gender,
    COUNT(*) AS total_transactions
FROM retail_sales
GROUP BY category, gender
ORDER BY category;
```

### 7. Find the month with the highest average sale in each year
```sql
SELECT year, month, avg_sale
FROM (
    SELECT
        EXTRACT(YEAR FROM sale_date) AS year,
        EXTRACT(MONTH FROM sale_date) AS month,
        AVG(total_sale) AS avg_sale,
        RANK() OVER (
            PARTITION BY EXTRACT(YEAR FROM sale_date)
            ORDER BY AVG(total_sale) DESC
        ) AS rank
    FROM retail_sales
    GROUP BY 1, 2
) AS monthly_sales
WHERE rank = 1;
```

### 8. Find the top 5 customers by total sales
```sql
SELECT
    customer_id,
    SUM(total_sale) AS total_sales
FROM retail_sales
GROUP BY customer_id
ORDER BY total_sales DESC
LIMIT 5;
```

### 9. Count unique customers in each category
```sql
SELECT
    category,
    COUNT(DISTINCT customer_id) AS unique_customers
FROM retail_sales
GROUP BY category;
```

### 10. Count orders by sales shift (Morning, Afternoon, Evening)
```sql
WITH hourly_sales AS (
    SELECT *,
        CASE
            WHEN EXTRACT(HOUR FROM sale_time) < 12 THEN 'Morning'
            WHEN EXTRACT(HOUR FROM sale_time) BETWEEN 12 AND 17 THEN 'Afternoon'
            ELSE 'Evening'
        END AS shift
    FROM retail_sales
)
SELECT
    shift,
    COUNT(*) AS total_orders
FROM hourly_sales
GROUP BY shift;
```

## Key Insights

- Analyzed customer demographics and purchasing patterns across product categories.
- Identified high-value transactions and top-spending customers.
- Examined monthly sales trends and sales activity by time of day.
- Compared category performance and unique customer counts.

## How to Run

1. Clone this repository.
2. Create the database and table using the setup SQL.
3. Import the retail sales dataset into `retail_sales`.
4. Run the queries to explore the data and answer the business questions.


