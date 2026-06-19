# Pandas

## Overview

Pandas is the primary Python library for working with structured and tabular data. It provides tools for data cleaning, transformation, exploration, analysis, and preparation for machine learning and data engineering workflows.

Pandas is commonly used alongside:

- NumPy
- Matplotlib
- Seaborn
- Jupyter Notebooks
- SQL Databases
- Snowflake / Snowpark
- Machine Learning Libraries

---

## Topics Covered

### Data Inspection

- `head()`
- `info()`
- `describe()`

### Data Selection

- Column selection
- Boolean filtering
- `loc`
- `iloc`

### Data Cleaning

- Missing values
- `isna()`
- `fillna()`
- `dropna()`
- Data correction strategies

### Data Manipulation

- Sorting
- Filtering
- Sampling
- Aggregation

### Statistical Analysis

- Mean
- Median
- Mode
- Standard Deviation
- Quartiles

### Data Visualization

- Histograms
- Box Plots
- Seaborn Visualizations

### Time Series Data

- Datetime conversion
- Time-based joins
- `merge_asof()`

---

## Frequently Used Methods

```python
head()
info()
describe()

loc[]
iloc[]

isna()
fillna()
dropna()

sample()

merge()
merge_asof()
```

---

## Key Concepts

### Boolean Indexing

```python
df[df["price"] < 300000]
```

### Integer-Based Selection

```python
df.iloc[10:15, 2:5]
```

### Missing Value Analysis

```python
df.isna().sum()
```

### Random Sampling

```python
df.sample(5, replace=False)
```

### Time-Based Matching

```python
pd.merge_asof(...)
```

---

## Learning Goals

- Understand Pandas DataFrames and Series
- Clean and prepare datasets
- Perform exploratory data analysis (EDA)
- Work with time-series data
- Build foundations for SQL, Snowflake, Data Engineering, and AI Engineering

---

## Related Topics

- Databases
- SQL
- Snowflake
- Data Engineering
- Data Science
- Machine Learning
- AI Engineering
