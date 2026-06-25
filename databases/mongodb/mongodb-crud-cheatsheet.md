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
