## 🧠 Git Commands Cheat Sheet (Important)

```bash
# Config
git config --global user.name "Your Name"
git config --global user.email "you@example.com"

# Repo setup
git init                        # Create new repo
git clone <url>                # Clone remote repo

# Stage & commit
git status                     # Check status
git add <file> or .            # Stage file(s)
git commit -m "Message"        # Commit changes

# Branching
git branch                     # List branches
git checkout -b <branch>       # Create & switch
git switch <branch>            # Switch branches
git merge <branch>             # Merge branch into current
git rebase <branch>            # Rebase current onto <branch>
git push origin -u <branch>    #pgte branch to remote

# Syncing
git pull                       # Pull changes
git push                       # Push changes
git push -u origin <branch>    # Push new branch

# Stash
git stash                      # Stash changes
git stash pop                  # Apply stashed changes

# Logs
git log --oneline              # Condensed log
git log -p <file>              # File history

# Super important
git fetch
```

---

## 🧠 Advanced SQL Cheat Sheet

```sql
-- SQL ORDER OF EXECUTION
FROM and/or JOIN clause.
WHERE clause.
GROUP BY clause.
HAVING clause.
SELECT clause.
DISTINCT clause.
ORDER BY clause.
LIMIT and/or OFFSET clause.
-- Window Functions
-- Rank() is good for deduplication of data - example: get the most recent records on certain grain.
SELECT name, salary,
       RANK() OVER (PARTITION BY department ORDER BY salary DESC) AS rank
FROM employees;

-- CTE (Common Table Expressions)
-- Comment: Better to use CTE instead of building loooong ass SQL
WITH top_salaries AS (
    SELECT name, salary FROM employees ORDER BY salary DESC LIMIT 5
)
SELECT * FROM top_salaries;

-- CASE
SELECT name,
       CASE
         WHEN salary > 100000 THEN 'High'
         WHEN salary > 50000 THEN 'Medium'
         ELSE 'Low'
       END AS salary_band
FROM employees;


-- Aggregates with GROUP BY and HAVING
-- My comment: Can be used for looking for duplicates but it is better to use windows function with rank().

SELECT department, COUNT(*) as num_employees
FROM employees
GROUP BY department
HAVING COUNT(*) > 5;

-- UNION is important
-- Merge into super important
-- Merge into vs overwrite

Most Important Window Functions in SQL:

Window functions perform calculations across a set of table rows related to the current row, without collapsing the results.

Top window functions:

ROW_NUMBER() - Assigns a unique number to rows within a partition (good for rankings).
RANK() - Ranks rows with gaps in case of ties.
DENSE_RANK() - Like RANK() but no gaps between rankings.
NTILE(n) - Divides rows into n groups.
LEAD() - Access the next row’s value.
LAG() - Access the previous row’s value.
SUM() OVER() - Running total without grouping.
AVG() OVER() - Moving averages.
FIRST_VALUE() - First value in a window.
LAST_VALUE() - Last value in a window.

Example:

SELECT 
    user_id,
    order_date,
    SUM(order_amount) OVER (PARTITION BY user_id ORDER BY order_date) AS running_total
FROM orders;

---

What is a CTE (Common Table Expression)?

CTEs are temporary result sets used inside a larger query, started with the WITH keyword.

Example:

WITH recent_orders AS (
    SELECT user_id, order_date
    FROM orders
    WHERE order_date >= '2025-01-01'
)
SELECT user_id, COUNT(*)
FROM recent_orders
GROUP BY user_id;

You can chain multiple CTEs.

---

What is COALESCE?

COALESCE returns the first non-NULL value from a list.

Example:

SELECT COALESCE(phone_number, email, 'no_contact_info') AS contact
FROM users;

Meaning: Try phone_number first, if NULL then try email, else use 'no_contact_info'.

---

Advanced SQL Concepts and Real-World Examples:

1. Recursive CTEs
   - Solve hierarchy problems (e.g., org charts).
   
   Example:

   WITH RECURSIVE org_chart AS (
       SELECT employee_id, manager_id, 1 AS level
       FROM employees
       WHERE employee_id = 'John'

       UNION ALL

       SELECT e.employee_id, e.manager_id, level + 1
       FROM employees e
       INNER JOIN org_chart o ON e.employee_id = o.manager_id
   )
   SELECT * FROM org_chart;

---

2. Window Frame Clauses
   - Control how much data a window function sees (e.g., moving averages).

   Example:

   SELECT 
       date,
       sales,
       AVG(sales) OVER (ORDER BY date ROWS BETWEEN 2 PRECEDING AND CURRENT ROW) AS moving_avg
   FROM sales_data;

---

3. Partitioning and Indexing
   - Divide big tables like logs into partitions (e.g., by date) for faster querying.

---

4. CTEs + Window Functions + Joins Together
   - Combine techniques for complex logic.

   Example:

   WITH ranked_purchases AS (
       SELECT 
           customer_id,
           purchase_date,
           ROW_NUMBER() OVER (PARTITION BY customer_id ORDER BY purchase_date) AS rn
       FROM purchases
   )
   SELECT customer_id, purchase_date
   FROM ranked_purchases
   WHERE rn IN (1, 2);

---

5. CASE Inside Aggregations
   - Conditional sums or counts.

   Example:

   SELECT 
       COUNT(CASE WHEN status = 'active' THEN 1 END) AS active_users,
       COUNT(CASE WHEN status = 'inactive' THEN 1 END) AS inactive_users
   FROM users;

---

6. Materialized Views
   - Save a query result and refresh it periodically.

   Example:

   CREATE MATERIALIZED VIEW daily_active_users AS
   SELECT date, COUNT(DISTINCT user_id)
   FROM user_activity
   GROUP BY date;

---

7. Pivot and Unpivot
   - Transform rows into columns or vice versa.

   Example (pivot orders into months):

   SELECT 
       customer_id,
       SUM(CASE WHEN EXTRACT(MONTH FROM order_date) = 1 THEN 1 ELSE 0 END) AS jan_orders,
       SUM(CASE WHEN EXTRACT(MONTH FROM order_date) = 2 THEN 1 ELSE 0 END) AS feb_orders
   FROM orders
   GROUP BY customer_id;

---

8. Anti-Joins and Semi-Joins
   - Find missing or existing relationships efficiently.

   Example (users who never purchased):

   SELECT u.user_id
   FROM users u
   LEFT JOIN purchases p ON u.user_id = p.user_id
   WHERE p.user_id IS NULL;

---

Real-World SQL Patterns:

---

1. Daily Data Pipeline with SQL

Goal: Load only new data daily (incremental load).

Example:

INSERT INTO clean_orders (order_id, customer_id, amount, created_at)
SELECT 
    order_id,
    customer_id,
    amount,
    created_at
FROM raw_orders
WHERE created_at >= CURRENT_DATE - INTERVAL '1 day'
  AND created_at < CURRENT_DATE;

---

2. Retention Cohorts Analysis

Goal: Track user retention over time.

Example:

WITH cohort AS (
    SELECT user_id, MIN(signup_date) AS cohort_date
    FROM users
    GROUP BY user_id
),
activity AS (
    SELECT l.user_id, l.login_date, c.cohort_date,
           DATE_DIFF('day', c.cohort_date, l.login_date) AS days_since_signup
    FROM logins l
    JOIN cohort c ON l.user_id = c.user_id
)
SELECT 
    cohort_date,
    days_since_signup,
    COUNT(DISTINCT user_id) AS active_users
FROM activity
GROUP BY cohort_date, days_since_signup
ORDER BY cohort_date, days_since_signup;

---

3. Anomaly Detection in SQL

Goal: Find sudden drops or spikes (e.g., sales drop by 50%).

Example:

WITH sales_by_day AS (
    SELECT 
        order_date,
        SUM(order_amount) AS daily_sales
    FROM orders
    GROUP BY order_date
),
sales_with_lag AS (
    SELECT 
        order_date,
        daily_sales,
        LAG(daily_sales) OVER (ORDER BY order_date) AS previous_day_sales
    FROM sales_by_day
)
SELECT 
    order_date,
    daily_sales,
    previous_day_sales,
    (daily_sales - previous_day_sales) / previous_day_sales AS percent_change
FROM sales_with_lag
WHERE ABS((daily_sales - previous_day_sales) / previous_day_sales) > 0.5;

---

4. Data Cleaning and Deduplication

Goal: Remove duplicates, keeping the most recent record.

Example:

WITH ranked_users AS (
    SELECT *,
           ROW_NUMBER() OVER (PARTITION BY email ORDER BY created_at DESC) AS rn
    FROM users
)
DELETE FROM ranked_users
WHERE rn > 1;

---

5. Slowly Changing Dimension (SCD) - Type 2

Goal: Track history of changes without overwriting.

Example structure:

| user_id | address    | valid_from | valid_to    |
|--------|-------------|------------|-------------|
| 123    | old address | 2020-01-01 | 2022-01-01  |
| 123    | new address | 2022-01-01 | NULL        |

---

Quick Summary:

| Pattern                  | Real-world use case                      |
|---------------------------|------------------------------------------|
| Daily pipeline            | Regularly update tables without full reloads |
| Retention cohorts         | Understand user stickiness              |
| Anomaly detection         | Alert on sudden data changes            |
| Deduplication             | Clean messy raw data                    |
| SCD Type 2                | Track full historical changes           |


```
---

## 🧠 PySpark Cheat Sheet

```python

# Don't use show and display in ETL/ELT scripts - it just takes up memory
# Init - not important for Databricks ofc
from pyspark.sql import SparkSession
spark = SparkSession.builder.appName("App").getOrCreate()

# Load Data
df = spark.read.csv("file.csv", header=True, inferSchema=True)
# Load data with pyspark.pandas


# Show & Schema
df
df.printSchema()

# Select & Filter
df.select("name")
df.filter(df.age > 30)

# GroupBy & Agg
df.groupBy("dept").agg({"salary": "avg"})

# With Column
from pyspark.sql.functions import col, when
df.withColumn("senior", when(col("age") > 50, True).otherwise(False))

# SQL - super importnat <3
df.createOrReplaceTempView("people")
spark.sql("SELECT * FROM people WHERE age > 30")


```
---

## 🧠 Pandas Cheat Sheet

```python
import pandas as pd

# Read / Write
df = pd.read_csv("file.csv")
df.to_csv("out.csv", index=False)

# Explore
df.head()
df.info()
df.describe()

# Select / Filter
df["column"]
df[["col1", "col2"]]
df[df["age"] > 30]

# GroupBy
df.groupby("dept")["salary"].mean()

# Apply
df["age_group"] = df["age"].apply(lambda x: "Senior" if x > 50 else "Junior")

# Merge / Join
pd.merge(df1, df2, on="id", how="left")

# Missing values
df.dropna()
df.fillna(0)

# Pivot
df.pivot_table(index="dept", values="salary", aggfunc="mean")
```

---

## 🧠 Python Data Types & Structures Cheat Sheet

```python
# Data Types
int, float, str, bool, None

#Hints

# Type check
type(x)

# List
lst = [1, 2, 3]
lst.append(4)
lst[0]        # Access
lst[-1]       # Last element
lst[1:3]      # Slice

# Dict
d = {"a": 1, "b": 2}
d["a"]
d.get("c", 0)
for k, v in d.items(): print(k, v)

# Set
s = {1, 2, 3}
s.add(4)
s & {2, 3, 5}   # Intersection

# Tuple
t = (1, 2)
x, y = t        # Unpacking

# Comprehensions
[x**2 for x in range(5) if x % 2 == 0]
{k: v**2 for k, v in d.items()}
{x for x in range(10) if x % 2 == 0}

# Functions
def add(a, b=0):
    return a + b

# Lambda
f = lambda x: x**2

# Classes
class Animal:
    def __init__(self, name):
        self.name = name
    def speak(self):
        return "Hi, I'm " + self.name
        

## 🧠 Python Data Structures: Key Differences

### 📋 Overview

| Data Structure | Ordered | Mutable | Duplicates | Common Use                          | Syntax              |
|----------------|---------|---------|------------|--------------------------------------|---------------------|
| **List**       | ✅ Yes  | ✅ Yes  | ✅ Yes     | Sequence of items                    | `[1, 2, 3]`          |
| **Tuple**      | ✅ Yes  | ❌ No   | ✅ Yes     | Immutable sequence                   | `(1, 2, 3)`          |
| **Set**        | ❌ No   | ✅ Yes  | ❌ No     | Unique, unordered elements           | `{1, 2, 3}`          |
| **Dict**       | ✅ Yes  | ✅ Yes  | ❌ No (keys) | Key-value pairs (map, JSON-like)    | `{"a": 1, "b": 2}`   |
| **String**     | ✅ Yes  | ❌ No   | ✅ Yes     | Text, character sequence             | `"hello"`            |

---

### ⚙️ Functional Differences

- **List vs Tuple**:
  - Use **list** when items need to change.
  - Use **tuple** for fixed data (can be used as dictionary keys).

- **Set vs List**:
  - Use **set** for **fast membership tests** and storing **unique** elements.
  - Sets are unordered and don’t allow duplicates.

- **Dict vs Set**:
  - `set` holds **values only**.
  - `dict` maps **keys to values**.

- **String vs List**:
  - Strings are **immutable** (e.g., `s[0] = 'H'` will error).
  - Lists are **mutable** and can store **mixed types**.

---

### ⏱️ Performance Comparison

| Operation              | List       | Tuple      | Set        | Dict             |
|------------------------|------------|------------|------------|------------------|
| Access by index        | O(1)       | O(1)       | ❌         | ❌               |
| Search by value        | O(n)       | O(n)       | O(1)       | O(1) *(by key)*  |
| Insertion (end)        | O(1)       | ❌         | O(1)       | O(1)             |
| Deletion               | O(n)       | ❌         | O(1)       | O(1)             |

> ❌ = Not supported or not applicable (e.g., no index in sets or dicts)


# Decorators

#Multhreading


```

