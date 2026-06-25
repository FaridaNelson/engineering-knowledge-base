## CRUD Operations

# Create

insertOne()
insertMany()

Example:

db.students.insertOne({
name: "John Doe",
age: 21
})

# Read

find()
findOne()

Filtering

db.products.find({
category: "electronics"
})

# Update

updateOne()
updateMany()

Using $set

db.orders.updateOne({ \_id: ObjectId(...) },
{ $set: {
status: "shipped" } }
)

# Delete

deleteOne()
deleteMany()

Example

db.users.deleteMany({
status: "inactive"
})

# Query Operators

Comparison

$eq
$ne
$lt
$lte
$gt
$gte

Logical

$and
$or

Array

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
