# Snowflake Vocabulary

## What is Snowpark?

Snowpark is Snowflake's developer framework that lets you write code in Python, Java, Scala, or SQL and run it directly inside Snowflake.

Instead of pulling data out of Snowflake to your laptop, Snowpark brings your code to where the data lives.

### Think:

**Traditional:**

```text
Database → Python
```

**Snowpark:**

```text
Python → Database
```

---

## What is a Snowpark DataFrame?

A Snowpark DataFrame is a table-like object that represents data stored in Snowflake.

Unlike Pandas, the data usually remains in Snowflake until you explicitly request it.

### Example

```python
df = session.table("LOCATION")
```

This doesn't download the data.

It creates a reference to the table.

---

## What is a Warehouse?

A Warehouse is Snowflake's compute engine.

It is the processing power that executes queries, transformations, notebooks, and data pipelines.

### Think:

```text
Database = Storage
Warehouse = CPU/RAM
```

Without a warehouse running, nothing gets processed.

---

## What is a Schema?

A Schema is a folder inside a database.

### Example

```text
DEMO_DB
├── DEV_SCHEMA
│   ├── LOCATION
│   └── ORDER_DETAIL
│
└── PROD_SCHEMA
```

Schemas help organize tables, views, stages, and other database objects.

---

## What is a Role?

A Role defines permissions.

Roles determine what you can:

- Read
- Write
- Create
- Delete
- Execute

### Example

```text
ACCOUNTADMIN
```

can do almost anything.

```text
DEMO_ROLE
```

only has permissions granted to it.

### Think:

```text
Role = Job Title
```

inside Snowflake.

---

## Why is GitHub Connected?

GitHub stores:

- Notebooks
- SQL scripts
- Python code
- Configuration

Snowflake can pull those files directly from GitHub.

This allows:

```text
GitHub
   ↓
Snowflake Workspace
```

instead of manually copying files around.

It's essentially source control for your Snowflake projects.

---

## What is a PAT?

PAT = Personal Access Token

It's a secure GitHub password specifically for applications.

Snowflake uses the PAT to prove:

> "I am allowed to access Farida's GitHub repository."

without knowing your GitHub password.

---

## What is a Kernel?

A Kernel is the Python process that runs notebook code.

### Think:

```text
Notebook
    ↓
Kernel
    ↓
Python Execution
```

All variables live inside the kernel.

### Example

```python
x = 5
```

The kernel remembers that value.

If the kernel restarts:

```python
x
```

no longer exists.

Everything must be rerun.

---

## What is Pandas?

Pandas is the most popular Python library for working with tabular data.

### Think of it as:

```text
Excel for Python
```

### Common Uses

- Filter rows
- Sort data
- Aggregate data
- Join datasets
- Analyze data

---

## What is a Pandas DataFrame?

A Pandas DataFrame is a table stored entirely in memory.

### Example

```python
import pandas as pd

df = pd.read_excel("location.xlsx")
```

The data is now sitting inside Python memory.

---

## How is a Pandas DataFrame Different from a Snowpark DataFrame?

### Pandas

```text
Data lives in Python memory
```

Good for:

- Analysis
- Small/medium datasets
- Local processing

### Snowpark

```text
Data lives in Snowflake
```

Good for:

- Large datasets
- Distributed processing
- Production pipelines

### Example

```python
pandas_df.iterrows()
```

works because the data is local.

But:

```python
snowpark_df.iterrows()
```

does not exist because Snowpark isn't storing rows in Python memory.

---

## Why Did `iterrows()` Fail?

Your variable:

```python
dataframe_1
```

was a:

```text
Snowpark DataFrame
```

not a:

```text
Pandas DataFrame
```

The notebook assumed:

```python
dataframe_1.iterrows()
```

would work.

But Snowpark DataFrames don't have an `iterrows()` method.

You fixed it with:

```python
files_to_load = dataframe_1.to_pandas()
```

Now:

```python
files_to_load.iterrows()
```

works because it's a Pandas DataFrame.

---

## How Does Excel Become a Snowflake Table?

This is exactly what you did during the workshop.

### Step 1

Read Excel

```python
load_workbook()
```

↓

### Step 2

Create Pandas DataFrame

```python
pd.DataFrame(...)
```

↓

### Step 3

Create Snowpark DataFrame

```python
session.create_dataframe(df)
```

↓

### Step 4

Write to Snowflake

```python
snowpark_df.write.save_as_table()
```

↓

### Result

```text
Excel File
    ↓
Pandas DataFrame
    ↓
Snowpark DataFrame
    ↓
Snowflake Table
```

### Your Specific Example

```text
location.xlsx
    ↓
Pandas
    ↓
Snowpark
    ↓
DEMO_DB.DEV_SCHEMA.LOCATION
    ↓
4 rows
```

and

```text
order_detail.xlsx
    ↓
Pandas
    ↓
Snowpark
    ↓
DEMO_DB.DEV_SCHEMA.ORDER_DETAIL
    ↓
100 rows
```

---

## What is a Stage?

A Stage is a storage location that Snowflake can read files from.

### Examples

- S3 Bucket
- Azure Blob Storage
- Google Cloud Storage
- Internal Snowflake Storage

In your notebook:

```text
@INTEGRATIONS.FROSTBYTE_RAW_STAGE
```

was a stage pointing to files stored externally.

### Think:

```text
Stage = File Storage
Table = Structured Data
```

### The Full Pipeline

```text
Excel File
   ↓
Stage
   ↓
Pandas
   ↓
Snowpark
   ↓
Snowflake Table
```

That concept appears constantly in real-world data engineering.
