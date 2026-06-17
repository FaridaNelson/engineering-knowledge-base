# Snowflake Data Engineering Workshop — June 17, 2026

## Event Information

**Workshop:** Build Data Engineering Pipelines Using Snowpark in Snowflake Notebooks
**Author:** Jeremiah Hansen
**Date Attended:** June 17, 2026
**Format:** Hands-on Snowflake Workshop

---

## Objective

Learn how to build a simple data engineering pipeline using Snowflake Notebooks, Snowpark, Snowflake Marketplace data, staged Excel files, and Snowflake tables.

The workshop demonstrated how data can be ingested, transformed, and stored entirely within the Snowflake ecosystem.

---

## Technologies Encountered

- Snowflake
- Snowpark
- Snowflake Notebooks
- Snowflake Marketplace
- Snowflake Stages
- Snowflake Warehouses
- Snowflake Roles
- Snowflake Schemas
- GitHub Integration
- Git Workspaces
- Personal Access Tokens (PAT)
- Python
- Pandas
- openpyxl

---

## Environment Setup

### Completed

- Created Snowflake trial account
- Created Notebook Service
- Forked workshop GitHub repository
- Connected GitHub repository to Snowflake
- Created GitHub Personal Access Token (PAT)
- Created Snowflake Git Workspace
- Ran `setup.sql`
- Added Frostbyte Weather Source data from Snowflake Marketplace

### Objects Created

- DEMO_ROLE
- DEMO_WH
- DEMO_DB
- DEV_SCHEMA
- PROD_SCHEMA
- INTEGRATIONS schema
- FROSTBYTE_RAW_STAGE
- Event Table

---

## Key Concepts Learned

### Snowpark

Snowpark allows developers to write Python code that executes directly inside Snowflake.

Instead of moving data from Snowflake into Python, Snowpark moves computation closer to the data.

---

### Snowpark DataFrames

A Snowpark DataFrame is similar to a Pandas DataFrame but represents data that remains in Snowflake until explicitly retrieved.

Example:

```python
df = session.table("LOCATION")
```

This creates a reference to data rather than downloading it.

---

### Data Pipeline Pattern

The primary pattern demonstrated during the workshop was:

```text
Excel File
    ↓
Stage
    ↓
Pandas DataFrame
    ↓
Snowpark DataFrame
    ↓
Snowflake Table
```

This pattern is common in modern data engineering workflows.

---

## Major Issue Encountered

### Snowpark DataFrame vs Pandas DataFrame

The workshop notebook attempted to execute:

```python
files_to_load = dataframe_1

for index, excel_file in files_to_load.iterrows():
```

This produced:

```text
AttributeError: DataFrame object has no attribute iterrows
```

### Root Cause

`dataframe_1` was a Snowpark DataFrame, not a Pandas DataFrame.

The `iterrows()` method only exists on Pandas DataFrames.

### Solution

Convert the Snowpark DataFrame to a Pandas DataFrame:

```python
files_to_load = dataframe_1.to_pandas()
```

After conversion:

```python
files_to_load.iterrows()
```

executed successfully.

---

## Data Loaded

### LOCATION

Successfully loaded:

```text
DEMO_DB.DEV_SCHEMA.LOCATION
```

Verification:

```sql
SELECT COUNT(*) FROM LOCATION;
```

Result:

```text
4 rows
```

---

### ORDER_DETAIL

Successfully loaded:

```text
DEMO_DB.DEV_SCHEMA.ORDER_DETAIL
```

Verification:

```sql
SELECT COUNT(*) FROM ORDER_DETAIL;
```

Result:

```text
100 rows
```

---

## Personal Takeaways

### Technical

- Learned the difference between Snowpark and Pandas DataFrames.
- Learned how Snowflake uses Warehouses, Roles, Schemas, and Stages.
- Learned how Snowflake Notebooks execute Python directly inside Snowflake.
- Learned how GitHub repositories can be connected directly to Snowflake Workspaces.
- Learned how Marketplace datasets can be added to a Snowflake environment.

### Engineering

The most valuable learning came from debugging the DataFrame mismatch rather than simply following workshop instructions.

Understanding why the error occurred provided a deeper understanding of how Snowpark differs from Pandas.

---

## Future Applications

### StudioPulse

Potential future uses:

- Importing student data from spreadsheets.
- Building analytics pipelines.
- Generating reporting datasets.
- Running large-scale data transformations.
- Supporting future AI-driven insights.

### Career Development

This workshop provided practical exposure to:

- Data Engineering
- Snowflake
- Snowpark
- Cloud Data Platforms

which are frequently requested skills in modern software engineering and AI-related roles.

---

## Next Topics to Explore

- Snowpark Transformations
- Snowflake Tasks
- Snowflake Streams
- Dynamic Tables
- Snowflake Cortex AI
- Data Warehousing Concepts
- ETL / ELT Design Patterns
- Data Modeling
- Analytics Engineering

---

## Overall Assessment

Although the workshop officially lasted one hour, completing the setup and understanding the concepts required significantly more time.

The hands-on experience was valuable because it combined:

- Cloud infrastructure
- GitHub integration
- Python development
- Data engineering workflows
- Real-world debugging

The workshop transformed several previously abstract concepts into practical experience.
