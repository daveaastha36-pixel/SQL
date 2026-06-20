

---

# Section A: Concept Application

## 1. Difference between `SELECT *` and specifying column names

### `SELECT *`

Retrieves **all columns** from a table.

```sql
SELECT * FROM Orders;
```

### Specifying Column Names

Retrieves only the required columns.

```sql
SELECT Order_ID, Sales, Profit
FROM Orders;
```

### When to Use

| SELECT *                    | Specific Columns        |
| --------------------------- | ----------------------- |
| Quick data exploration      | Production queries      |
| Small datasets              | Large datasets          |
| Testing purposes            | Better performance      |
| Not recommended for reports | Recommended for reports |

**Advantage of specific columns:** Faster execution, less memory usage, better readability.

---

## 2. Which keyword renames a column and does it change the table structure?

The keyword used is **AS** (Alias).

```sql
SELECT Sales AS Total_Sales
FROM Orders;
```

### Important

* Alias only changes the column name in the query output.
* It does **not** modify the actual table structure.

Example:

```sql
SELECT Profit AS Net_Profit
FROM Orders;
```

Output column name becomes `Net_Profit`, but the database column remains `Profit`.

---

## 3. Why does `'5000'` create a data type conflict?

```sql
SELECT *
FROM Orders
WHERE Sales > '5000';
```

Here:

* `Sales` is Numeric (INT/DECIMAL/FLOAT)
* `'5000'` is a String (VARCHAR)

SQL must convert one datatype into another before comparison.

Possible issues:

* Slower performance due to implicit conversion.
* Errors in strict SQL engines.
* Unexpected results.

Correct:

```sql
SELECT *
FROM Orders
WHERE Sales > 5000;
```

---

## 4. Difference between `ORDER BY Profit DESC` and `ASC`

### DESC (Descending)

```sql
ORDER BY Profit DESC
```

Highest profit → Lowest profit

Example:

| Profit |
| ------ |
| 2500   |
| 1800   |
| 1200   |

Used for:

* Top profitable orders
* Best-selling products
* Highest sales analysis

---

### ASC (Ascending)

```sql
ORDER BY Profit ASC
```

Lowest profit → Highest profit

Example:

| Profit |
| ------ |
| -1500  |
| -500   |
| 200    |

Used for:

* Loss-making orders
* Worst-performing products

### For Top 10 Profitable Orders

Use:

```sql
ORDER BY Profit DESC
```

---

## 5. T-SQL equivalent of LIMIT

MySQL:

```sql
SELECT *
FROM Orders
LIMIT 10;
```

MS SQL Server:

```sql
SELECT TOP 10 *
FROM Orders;
```

### Why syntax differs?

Different database vendors developed SQL extensions independently.

Examples:

| Database   | Syntax      |
| ---------- | ----------- |
| MySQL      | LIMIT       |
| PostgreSQL | LIMIT       |
| SQL Server | TOP         |
| Oracle     | FETCH FIRST |

All achieve the same goal but use different syntax.

---

## 6. Logical Execution Order

Query:

```sql
SELECT Order_ID, Sales
FROM Orders
WHERE Sales > 5000
ORDER BY Sales DESC
LIMIT 10;
```

### Actual Execution Order

1. FROM
2. WHERE
3. SELECT
4. ORDER BY
5. LIMIT/TOP

### Flow

```
FROM Orders
      ↓
WHERE Sales > 5000
      ↓
SELECT columns
      ↓
ORDER BY Sales DESC
      ↓
LIMIT 10
```

---

# Section B: Practical Task

## 1. Retrieve first 20 records

### MySQL/PostgreSQL

```sql
SELECT *
FROM Orders
LIMIT 20;
```

### SQL Server

```sql
SELECT TOP 20 *
FROM Orders;
```

---

## 2. Display Order ID, Order Date, Sales and Profit with Alias

```sql
SELECT
    Order_ID,
    Order_Date,
    Sales AS Total_Sales,
    Profit
FROM Orders;
```

---

## 3. High-value transactions (Sales > 5000)

```sql
SELECT *
FROM Orders
WHERE Sales > 5000;
```

---

## 4. Top 10 most profitable orders

### MySQL/PostgreSQL

```sql
SELECT *
FROM Orders
ORDER BY Profit DESC
LIMIT 10;
```

### SQL Server

```sql
SELECT TOP 10 *
FROM Orders
ORDER BY Profit DESC;
```

---

# Section C: Mini Project

# Retail Profitability & Market Segment Analysis

## 1. Database Schema Creation

```sql
CREATE DATABASE SuperstoreDB;
USE SuperstoreDB;
```

### Create Table

```sql
CREATE TABLE Orders (
    Row_ID INT,
    Order_ID VARCHAR(30),
    Order_Date DATE,
    Ship_Date DATE,
    Ship_Mode VARCHAR(50),
    Customer_ID VARCHAR(30),
    Customer_Name VARCHAR(100),
    Segment VARCHAR(50),
    Country VARCHAR(50),
    City VARCHAR(50),
    State VARCHAR(50),
    Postal_Code VARCHAR(20),
    Region VARCHAR(30),
    Product_ID VARCHAR(50),
    Category VARCHAR(50),
    Sub_Category VARCHAR(50),
    Product_Name VARCHAR(255),
    Sales DECIMAL(10,2),
    Quantity INT,
    Discount DECIMAL(5,2),
    Profit DECIMAL(10,2)
);
```

---

## 2. Multi-condition Filtering Query

Find orders having:

* Discount > 20%
* Profit < 0
* Sales > 1000

```sql
SELECT
    Order_ID,
    Category,
    Region,
    Sales,
    Discount,
    Profit
FROM Orders
WHERE Discount > 0.20
AND Profit < 0
AND Sales > 1000;
```

---

## 3. Aggregated Performance Report by Region

```sql
SELECT
    Region,
    COUNT(*) AS Total_Orders,
    SUM(Sales) AS Total_Sales,
    SUM(Profit) AS Total_Profit,
    AVG(Discount) AS Avg_Discount
FROM Orders
GROUP BY Region
ORDER BY Total_Profit DESC;
```

### Expected Output

| Region | Total Orders | Total Sales | Total Profit | Avg Discount |
| ------ | ------------ | ----------- | ------------ | ------------ |
| West   | ...          | ...         | ...          | ...          |
| East   | ...          | ...         | ...          | ...          |

---

## 4. Category-wise Profitability Analysis

Identify underperforming categories.

```sql
SELECT
    Category,
    SUM(Sales) AS Total_Sales,
    SUM(Profit) AS Total_Profit,
    AVG(Discount) AS Avg_Discount
FROM Orders
GROUP BY Category
ORDER BY Total_Profit ASC;
```

---

## 5. Profit Margin Analysis

Formula:

```text
Profit Margin (%) =
(Profit / Sales) × 100
```

SQL:

```sql
SELECT
    Category,
    Region,
    SUM(Sales) AS Total_Sales,
    SUM(Profit) AS Total_Profit,
    ROUND((SUM(Profit)/SUM(Sales))*100,2) AS Profit_Margin_Percent
FROM Orders
GROUP BY Category, Region
ORDER BY Profit_Margin_Percent ASC;
```

---

## 6. Loss-Making Transactions Summary

```sql
SELECT
    Order_ID,
    Category,
    Sub_Category,
    Region,
    Sales,
    Discount,
    Profit
FROM Orders
WHERE Profit < 0
ORDER BY Profit ASC;
```

---

## 7. Highest Loss Transactions

```sql
SELECT TOP 10
    Order_ID,
    Category,
    Region,
    Sales,
    Discount,
    Profit
FROM Orders
WHERE Profit < 0
ORDER BY Profit ASC;
```

(MySQL/PostgreSQL)

```sql
SELECT
    Order_ID,
    Category,
    Region,
    Sales,
    Discount,
    Profit
FROM Orders
WHERE Profit < 0
ORDER BY Profit ASC
LIMIT 10;
```

---


   
