# MongoDB Vocabulary

## Database

Container for collections.

---

## Collection

Equivalent to a SQL table.

---

## Document

Equivalent to a SQL row.

Stored as BSON.

---

## BSON

Binary JSON.

MongoDB's storage format.

---

## CRUD

Create

Read

Update

Delete

---

## Projection

Selecting which fields to return.

---

## Filter

Conditions used by find().

---

## Query

Request to retrieve documents.

---

## Index

Data structure that speeds up searches.

---

## Unique Index

Prevents duplicate values.

Example

```javascript
unique: true;
```

# ObjectId

MongoDB's default document identifier.

# Cursor

Result returned by find().

# Embedded Document

Document stored inside another document.

# Array

Ordered list inside a document.

# Operator

Special MongoDB command beginning with "$".

Examples

- $set
- $push
- $lt
- $gte
- $or

# Projection

Choosing which fields are returned.

# Sorting

Ordering query results.

# Limit

Restricting number of returned documents.

# countDocuments()

Efficient method for counting matching documents.
