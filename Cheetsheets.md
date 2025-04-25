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

