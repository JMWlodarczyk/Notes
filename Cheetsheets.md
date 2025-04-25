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
```

---

## 🧠 Advanced SQL Cheat Sheet

```sql
-- Window Functions
SELECT name, salary,
       RANK() OVER (PARTITION BY department ORDER BY salary DESC) AS rank
FROM employees;

-- CTE (Common Table Expressions)
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

-- Advanced Joins
SELECT * FROM a
FULL OUTER JOIN b ON a.id = b.id;

-- Aggregates with GROUP BY and HAVING
SELECT department, COUNT(*) as num_employees
FROM employees
GROUP BY department
HAVING COUNT(*) > 5;
```

---

## 🧠 PySpark Cheat Sheet

```python
# Init
from pyspark.sql import SparkSession
spark = SparkSession.builder.appName("App").getOrCreate()

# Load Data
df = spark.read.csv("file.csv", header=True, inferSchema=True)

# Show & Schema
df.show()
df.printSchema()

# Select & Filter
df.select("name").show()
df.filter(df.age > 30).show()

# GroupBy & Agg
df.groupBy("dept").agg({"salary": "avg"}).show()

# With Column
from pyspark.sql.functions import col, when
df.withColumn("senior", when(col("age") > 50, True).otherwise(False))

# SQL
df.createOrReplaceTempView("people")
spark.sql("SELECT * FROM people WHERE age > 30").show()
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
```

