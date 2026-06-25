## Query Operators

# Comparison

$eq
$ne
$lt
$lte
$gt
$gte

# Logical

$and
$or

# Array

$all
$push
$pull
$addToSet

# Sorting

Ascending

.sort({ score: 1 })

Descending

.sort({ score: -1 })

# Limiting

.limit(5)

# Counting

countDocuments()

# Projection

Returning only specific fields.

{
name:1,
department:1,
\_id:0
}

---

# MongoDB Query Operators

## Equality

```javascript
{
  category: "electronics";
}
```

Equivalent to

```javascript
{
  category: {
    $eq: "electronics";
  }
}
```

---

## Comparison

```javascript
{
  price: {
    $gt: 100;
  }
}
```

---

## Multiple Conditions

```javascript
{
  $and: [{ price: { $lt: 500 } }, { inventory: { $gt: 0 } }];
}
```

---

## OR

```javascript
{
  $or: [{ status: "active" }, { featured: true }];
}
```

---

## Arrays

Exact array

```javascript
{
  categories: ["Action", "Comedy"];
}
```

Contains values

```javascript
{
  categories: {
    $all: ["Action", "Comedy"];
  }
}
```

---

## Projection

Return selected fields

```javascript
{
name:1,
department:1,
_id:0
}
```

---

## Counting

```javascript
countDocuments();
```

---

## Sorting

```javascript
.sort({
score:-1
})
```

---

## Limiting

```javascript
.limit(5)
```

```

```
