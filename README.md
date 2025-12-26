# MongoDB Cheatsheet
A comprehensive, beginner-to-advanced guide for MongoDB using a practical students collection. Includes CRUD operations, advanced aggregation pipelines, indexing strategies, and performance best practices.

## A Complete Guide Using the `students` Collection

---

## 1. Database & Collection Basics

### Show All Databases

```javascript
show dbs
```

**Pro-Tip:** This lists all databases on your MongoDB server. Your database won't appear until it has data.

### Create or Switch to a Database

```javascript
use university
```

**Pro-Tip:** MongoDB creates the database automatically when you insert the first document.

### Show Current Database

```javascript
db;
```

### Show All Collections in Current Database

```javascript
show collections
```

### Create a Collection Explicitly

```javascript
db.createCollection("students");
```

**Pro-Tip:** Usually unnecessary—MongoDB creates collections automatically on first insert.

### Drop a Collection

```javascript
db.students.drop();
```

### Drop a Database

```javascript
db.dropDatabase();
```

---

## 2. CRUD Operations (The Basics)

### CREATE Operations

#### Insert One Student

```javascript
db.students.insertOne({
  roll_no: 1001,
  first_name: "Sarah",
  last_name: "Johnson",
  age: 20,
  email: "sarah.johnson@university.edu",
  country: "USA",
  major: "Computer Science",
  student_status: "active",
  gpa: 3.8,
  tuition_paid: 15000.0,
  scholarship_amount: 5000.0,
  enrollment_date: "2023-09-01",
  graduation_date: "2027-06-15",
});
```

**Pro-Tip:** `insertOne()` returns an `insertedId` which is the automatically generated `_id` field.

#### Insert Many Students

```javascript
db.students.insertMany([
  {
    roll_no: 1002,
    first_name: "Li",
    last_name: "Wei",
    age: 22,
    email: "li.wei@university.cn",
    country: "China",
    major: "Business Administration",
    student_status: "active",
    gpa: 3.5,
    tuition_paid: 12000.0,
    scholarship_amount: 3000.0,
    enrollment_date: "2022-09-01",
    graduation_date: "2026-06-15",
  },
  {
    roll_no: 1003,
    first_name: "Maria",
    last_name: "Garcia",
    age: 21,
    email: "maria.garcia@uni.es",
    country: "Spain",
    major: "Computer Science",
    student_status: "active",
    gpa: 3.9,
    tuition_paid: 10000.0,
    scholarship_amount: 7000.0,
    enrollment_date: "2023-09-01",
    graduation_date: "2027-06-15",
  },
]);
```

**Pro-Tip:** Use `insertMany()` for bulk operations. It's much faster than multiple `insertOne()` calls.

---

### READ Operations

#### Find All Students

```javascript
db.students.find();
```

#### Find with Pretty Formatting

```javascript
db.students.find().pretty();
```

**Pro-Tip:** `.pretty()` makes output readable in the shell (not needed in MongoDB Compass or most drivers).

#### Find One Student

```javascript
db.students.findOne();
```

**Pro-Tip:** Returns only the first matching document. Useful for testing queries.

#### Find Students by Major

```javascript
db.students.find({ major: "Computer Science" });
```

#### Find Students by Country

```javascript
db.students.find({ country: "USA" });
```

#### Find Students by Age

```javascript
db.students.find({ age: 21 });
```

#### Find Students by Multiple Criteria

```javascript
db.students.find({
  major: "Computer Science",
  country: "USA",
});
```

**Pro-Tip:** Multiple fields in the query object create an implicit `$and` condition.

---

### UPDATE Operations

#### Update One Student's Status

```javascript
db.students.updateOne({ roll_no: 1001 }, { $set: { student_status: "graduated" } });
```

**Pro-Tip:** Always use `$set` operator to update specific fields. Without it, you'll replace the entire document!

#### Update One Student's GPA

```javascript
db.students.updateOne({ email: "sarah.johnson@university.edu" }, { $set: { gpa: 3.9 } });
```

#### Increase GPA for One Student

```javascript
db.students.updateOne({ roll_no: 1002 }, { $inc: { gpa: 0.1 } });
```

**Pro-Tip:** `$inc` increments (or decrements with negative values) numeric fields.

#### Update Many Students' Status

```javascript
db.students.updateMany({ country: "Spain" }, { $set: { student_status: "exchange_program" } });
```

#### Update All Computer Science Students

```javascript
db.students.updateMany({ major: "Computer Science" }, { $inc: { scholarship_amount: 1000 } });
```

**Pro-Tip:** `updateMany()` affects all matching documents. Be careful with your filter!

#### Update with Multiple Fields

```javascript
db.students.updateOne(
  { roll_no: 1003 },
  {
    $set: {
      student_status: "on_leave",
      gpa: 3.95,
    },
  }
);
```

---

### DELETE Operations

#### Delete One Student by Roll Number

```javascript
db.students.deleteOne({ roll_no: 1001 });
```

**Pro-Tip:** Deletes only the first matching document.

#### Delete Many Students by Country

```javascript
db.students.deleteMany({ country: "Spain" });
```

#### Delete Students by Status

```javascript
db.students.deleteMany({ student_status: "inactive" });
```

**Pro-Tip:** Always test your filter with `find()` before using `deleteMany()` to avoid accidental data loss!

---

## 3. Advanced Querying & Operators

### Comparison Operators

#### Find Students with GPA Greater Than 3.5

```javascript
db.students.find({ gpa: { $gt: 3.5 } });
```

#### Find Students with GPA Greater Than or Equal to 3.8

```javascript
db.students.find({ gpa: { $gte: 3.8 } });
```

#### Find Students Younger Than 22

```javascript
db.students.find({ age: { $lt: 22 } });
```

#### Find Students 21 Years or Younger

```javascript
db.students.find({ age: { $lte: 21 } });
```

#### Find Students NOT from USA

```javascript
db.students.find({ country: { $ne: "USA" } });
```

**Pro-Tip:** `$ne` means "not equal to".

#### Find Students from Specific Countries

```javascript
db.students.find({
  country: { $in: ["USA", "China", "Spain"] },
});
```

**Pro-Tip:** `$in` checks if a field's value is in an array of values.

#### Find Students NOT from Specific Countries

```javascript
db.students.find({
  country: { $nin: ["USA", "Canada"] },
});
```

#### Find Students with GPA Between 3.0 and 3.7

```javascript
db.students.find({
  gpa: { $gte: 3.0, $lte: 3.7 },
});
```

---

### Logical Operators

#### Find Students from China OR USA

```javascript
db.students.find({
  $or: [{ country: "China" }, { country: "USA" }],
});
```

**Pro-Tip:** `$or` requires an array of condition objects.

#### Find Students with GPA > 3.5 AND from USA

```javascript
db.students.find({
  $and: [{ gpa: { $gt: 3.5 } }, { country: "USA" }],
});
```

**Pro-Tip:** Usually not needed—implicit `$and` works for most cases. Use explicit `$and` when you need the same field multiple times.

#### Find Students NOT Majoring in Computer Science

```javascript
db.students.find({
  major: { $not: { $eq: "Computer Science" } },
});
```

**Pro-Tip:** `$not` inverts the result of an operator expression.

#### Find Students Neither from USA NOR China

```javascript
db.students.find({
  $nor: [{ country: "USA" }, { country: "China" }],
});
```

**Pro-Tip:** `$nor` returns documents that fail ALL conditions in the array.

#### Complex Query: CS Students with High GPA from USA or China

```javascript
db.students.find({
  major: "Computer Science",
  gpa: { $gte: 3.7 },
  $or: [{ country: "USA" }, { country: "China" }],
});
```

---

### Element Operators

#### Find Students Who Have a Graduation Date

```javascript
db.students.find({
  graduation_date: { $exists: true },
});
```

**Pro-Tip:** `$exists` checks if a field is present in the document, regardless of its value.

#### Find Students Missing Email Field

```javascript
db.students.find({
  email: { $exists: false },
});
```

#### Find Students Where Age is a Number

```javascript
db.students.find({
  age: { $type: "number" },
});
```

**Pro-Tip:** `$type` checks the BSON type of a field. Useful for data validation.

#### Find Students Where Age is a String (Data Quality Check)

```javascript
db.students.find({
  age: { $type: "string" },
});
```

---

### Evaluation Operators

#### Find Students with .edu Email Addresses

```javascript
db.students.find({
  email: { $regex: /\.edu$/ },
});
```

**Pro-Tip:** `$regex` enables pattern matching. The `$` anchors the pattern to the end of the string.

#### Find Students with Names Starting with 'S' (Case Insensitive)

```javascript
db.students.find({
  first_name: { $regex: /^s/i },
});
```

**Pro-Tip:** The `i` flag makes the regex case-insensitive. The `^` anchors to the start.

#### Find Students with Gmail Addresses

```javascript
db.students.find({
  email: { $regex: /@gmail\.com$/ },
});
```

#### Find Students with Names Containing 'ar'

```javascript
db.students.find({
  first_name: { $regex: /ar/i },
});
```

---

## 4. Projection and Sorting

### Projection (Selecting Specific Fields)

#### Show Only First Name and GPA

```javascript
db.students.find({}, { first_name: 1, gpa: 1 });
```

**Pro-Tip:** `1` means include the field, `0` means exclude. The `_id` field is included by default.

#### Show First Name and GPA, Exclude \_id

```javascript
db.students.find({}, { first_name: 1, gpa: 1, _id: 0 });
```

#### Show All Fields Except Country and Age

```javascript
db.students.find({}, { country: 0, age: 0 });
```

**Pro-Tip:** You can't mix inclusion and exclusion (except for `_id`).

#### Find CS Students, Show Only Name and Email

```javascript
db.students.find({ major: "Computer Science" }, { first_name: 1, last_name: 1, email: 1, _id: 0 });
```

---

### Sorting

#### Sort Students by GPA (Ascending)

```javascript
db.students.find().sort({ gpa: 1 });
```

**Pro-Tip:** `1` = ascending (lowest to highest), `-1` = descending (highest to lowest).

#### Sort Students by GPA (Descending)

```javascript
db.students.find().sort({ gpa: -1 });
```

#### Sort by Graduation Date (Ascending)

```javascript
db.students.find().sort({ graduation_date: 1 });
```

#### Sort by Multiple Fields: Country (Asc), Then GPA (Desc)

```javascript
db.students.find().sort({
  country: 1,
  gpa: -1,
});
```

**Pro-Tip:** MongoDB sorts by the first field, then uses subsequent fields as tiebreakers.

#### Find Top 5 Students by GPA

```javascript
db.students.find().sort({ gpa: -1 }).limit(5);
```

#### Show Top Students with Projection

```javascript
db.students.find({}, { first_name: 1, last_name: 1, gpa: 1, _id: 0 }).sort({ gpa: -1 }).limit(3);
```

---

## 5. Aggregation Framework (Advanced)

**Pro-Tip:** The aggregation framework processes data in stages. Each stage transforms the documents and passes them to the next stage.

### Basic Aggregation Pipeline

#### Find Average GPA of All Students

```javascript
db.students.aggregate([
  {
    $group: {
      _id: null,
      averageGPA: { $avg: "$gpa" },
    },
  },
]);
```

**Pro-Tip:** `$group` with `_id: null` aggregates all documents into a single group.

#### Count Students by Major

```javascript
db.students.aggregate([
  {
    $group: {
      _id: "$major",
      count: { $sum: 1 },
    },
  },
]);
```

**Pro-Tip:** `$sum: 1` counts documents. The `_id` field specifies what to group by.

#### Average GPA by Major

```javascript
db.students.aggregate([
  {
    $group: {
      _id: "$major",
      averageGPA: { $avg: "$gpa" },
      studentCount: { $sum: 1 },
    },
  },
]);
```

#### Average GPA by Country

```javascript
db.students.aggregate([
  {
    $group: {
      _id: "$country",
      averageGPA: { $avg: "$gpa" },
    },
  },
]);
```

---

### Multi-Stage Aggregation Pipelines

#### Find Average GPA for Active CS Students

```javascript
db.students.aggregate([
  {
    $match: {
      major: "Computer Science",
      student_status: "active",
    },
  },
  {
    $group: {
      _id: null,
      averageGPA: { $avg: "$gpa" },
    },
  },
]);
```

**Pro-Tip:** `$match` filters documents early in the pipeline for better performance.

#### Average GPA by Major, Sorted by Average

```javascript
db.students.aggregate([
  {
    $group: {
      _id: "$major",
      averageGPA: { $avg: "$gpa" },
    },
  },
  {
    $sort: { averageGPA: -1 },
  },
]);
```

#### Top 3 Countries by Average GPA

```javascript
db.students.aggregate([
  {
    $group: {
      _id: "$country",
      averageGPA: { $avg: "$gpa" },
      studentCount: { $sum: 1 },
    },
  },
  {
    $sort: { averageGPA: -1 },
  },
  {
    $limit: 3,
  },
]);
```

---

### Using $project in Aggregation

#### Show Student Names and Calculate Full Name

```javascript
db.students.aggregate([
  {
    $project: {
      _id: 0,
      fullName: {
        $concat: ["$first_name", " ", "$last_name"],
      },
      gpa: 1,
      country: 1,
    },
  },
]);
```

**Pro-Tip:** `$project` reshapes documents, can create computed fields, and control what fields to include.

#### Calculate Net Tuition (Tuition - Scholarship)

```javascript
db.students.aggregate([
  {
    $project: {
      first_name: 1,
      last_name: 1,
      tuition_paid: 1,
      scholarship_amount: 1,
      netTuition: {
        $subtract: ["$tuition_paid", "$scholarship_amount"],
      },
    },
  },
]);
```

#### Show Students with GPA Status Label

```javascript
db.students.aggregate([
  {
    $project: {
      first_name: 1,
      gpa: 1,
      gpaStatus: {
        $cond: {
          if: { $gte: ["$gpa", 3.7] },
          then: "Excellent",
          else: "Good",
        },
      },
    },
  },
]);
```

---

### Financial Aggregations

#### Total Tuition Paid by All Students

```javascript
db.students.aggregate([
  {
    $group: {
      _id: null,
      totalTuition: { $sum: "$tuition_paid" },
    },
  },
]);
```

#### Total Scholarship Amount by Major

```javascript
db.students.aggregate([
  {
    $group: {
      _id: "$major",
      totalScholarships: { $sum: "$scholarship_amount" },
      averageScholarship: { $avg: "$scholarship_amount" },
    },
  },
  {
    $sort: { totalScholarships: -1 },
  },
]);
```

#### Tuition Statistics by Country

```javascript
db.students.aggregate([
  {
    $group: {
      _id: "$country",
      totalTuition: { $sum: "$tuition_paid" },
      averageTuition: { $avg: "$tuition_paid" },
      totalScholarships: { $sum: "$scholarship_amount" },
      studentCount: { $sum: 1 },
    },
  },
  {
    $project: {
      country: "$_id",
      totalTuition: 1,
      averageTuition: 1,
      totalScholarships: 1,
      studentCount: 1,
      netRevenue: {
        $subtract: ["$totalTuition", "$totalScholarships"],
      },
    },
  },
  {
    $sort: { netRevenue: -1 },
  },
]);
```

**Pro-Tip:** Complex pipelines combine multiple stages to create powerful analytics.

---

### Advanced Pipeline Example

#### Comprehensive Student Report by Major

```javascript
db.students.aggregate([
  {
    $match: { student_status: "active" },
  },
  {
    $group: {
      _id: "$major",
      studentCount: { $sum: 1 },
      averageGPA: { $avg: "$gpa" },
      averageAge: { $avg: "$age" },
      totalTuition: { $sum: "$tuition_paid" },
      totalScholarships: { $sum: "$scholarship_amount" },
    },
  },
  {
    $project: {
      major: "$_id",
      studentCount: 1,
      averageGPA: { $round: ["$averageGPA", 2] },
      averageAge: { $round: ["$averageAge", 1] },
      totalTuition: 1,
      totalScholarships: 1,
      netRevenue: {
        $subtract: ["$totalTuition", "$totalScholarships"],
      },
    },
  },
  {
    $sort: { studentCount: -1 },
  },
]);
```

---

## 6. Indexing

**Pro-Tip:** Indexes dramatically improve query performance but use memory and slow down writes. Create indexes on fields you frequently query.

### View Existing Indexes

```javascript
db.students.getIndexes();
```

### Create Single Field Index on Roll Number

```javascript
db.students.createIndex({ roll_no: 1 });
```

**Pro-Tip:** Unique identifiers like `roll_no` are perfect candidates for indexes.

### Create Unique Index on Email

```javascript
db.students.createIndex({ email: 1 }, { unique: true });
```

**Pro-Tip:** Unique indexes prevent duplicate values and improve lookup performance.

### Create Index on Major

```javascript
db.students.createIndex({ major: 1 });
```

### Create Compound Index on Country and Major

```javascript
db.students.createIndex({ country: 1, major: 1 });
```

**Pro-Tip:** Compound indexes support queries on the first field alone, or on both fields together, but not on the second field alone.

### Create Index on GPA (Descending)

```javascript
db.students.createIndex({ gpa: -1 });
```

**Pro-Tip:** Index direction matters for sort operations. Match your index direction to your most common sort patterns.

### Create Text Index for Search

```javascript
db.students.createIndex({
  first_name: "text",
  last_name: "text",
});
```

**Pro-Tip:** Text indexes enable full-text search capabilities.

### Drop an Index

```javascript
db.students.dropIndex("email_1");
```

### Drop All Indexes (Except \_id)

```javascript
db.students.dropIndexes();
```

### View Query Execution Plan (Check if Index is Used)

```javascript
db.students.find({ email: "sarah.johnson@university.edu" }).explain("executionStats");
```

**Pro-Tip:** Use `explain()` to see if your queries are using indexes efficiently.

---

## 7. Utility Commands

### Count Documents

#### Count All Students

```javascript
db.students.countDocuments();
```

#### Count Students by Major

```javascript
db.students.countDocuments({ major: "Computer Science" });
```

#### Count Active Students with GPA > 3.5

```javascript
db.students.countDocuments({
  student_status: "active",
  gpa: { $gt: 3.5 },
});
```

**Pro-Tip:** `countDocuments()` is accurate but can be slow on large collections. For approximate counts, use `estimatedDocumentCount()`.

---

### Distinct Values

#### Get All Unique Majors

```javascript
db.students.distinct("major");
```

#### Get All Unique Countries

```javascript
db.students.distinct("country");
```

#### Get Distinct Countries for CS Students

```javascript
db.students.distinct("country", { major: "Computer Science" });
```

**Pro-Tip:** `distinct()` is great for getting unique values and understanding your data distribution.

---

### Limit and Skip

#### Get First 5 Students

```javascript
db.students.find().limit(5);
```

#### Skip First 5 Students, Get Next 5

```javascript
db.students.find().skip(5).limit(5);
```

**Pro-Tip:** Use `skip()` and `limit()` together for pagination.

#### Pagination Example: Page 3, 10 Students per Page

```javascript
const page = 3;
const perPage = 10;

db.students
  .find()
  .skip((page - 1) * perPage)
  .limit(perPage)
  .sort({ roll_no: 1 });
```

---

### Other Useful Commands

#### Find One and Update (Returns Original Document)

```javascript
db.students.findOneAndUpdate({ roll_no: 1001 }, { $set: { student_status: "graduated" } });
```

#### Find One and Update (Returns Modified Document)

```javascript
db.students.findOneAndUpdate({ roll_no: 1001 }, { $set: { gpa: 4.0 } }, { returnNewDocument: true });
```

**Pro-Tip:** Use `findOneAndUpdate()` when you need the document before or after the update in one atomic operation.

#### Find One and Delete

```javascript
db.students.findOneAndDelete({ roll_no: 1001 });
```

#### Bulk Write Operations

```javascript
db.students.bulkWrite([
  {
    insertOne: {
      document: {
        roll_no: 1005,
        first_name: "John",
        last_name: "Doe",
        age: 20,
        major: "Engineering",
      },
    },
  },
  {
    updateOne: {
      filter: { roll_no: 1002 },
      update: { $set: { gpa: 3.6 } },
    },
  },
  {
    deleteOne: {
      filter: { roll_no: 1003 },
    },
  },
]);
```

**Pro-Tip:** `bulkWrite()` executes multiple operations efficiently in a single call.

---

## Quick Reference: Operator Summary

### Comparison Operators

- `$eq` - Equal to
- `$ne` - Not equal to
- `$gt` - Greater than
- `$gte` - Greater than or equal to
- `$lt` - Less than
- `$lte` - Less than or equal to
- `$in` - Matches any value in array
- `$nin` - Matches no value in array

### Logical Operators

- `$and` - Joins query clauses with AND
- `$or` - Joins query clauses with OR
- `$not` - Inverts effect of query expression
- `$nor` - Joins query clauses with NOR

### Element Operators

- `$exists` - Matches documents with specified field
- `$type` - Matches documents with specified BSON type

### Evaluation Operators

- `$regex` - Pattern matching with regular expressions
- `$expr` - Allows use of aggregation expressions

### Update Operators

- `$set` - Sets field value
- `$inc` - Increments field value
- `$unset` - Removes field
- `$push` - Adds element to array
- `$pull` - Removes element from array

### Aggregation Operators

- `$match` - Filters documents
- `$group` - Groups documents by expression
- `$project` - Reshapes documents
- `$sort` - Sorts documents
- `$limit` - Limits number of documents
- `$skip` - Skips documents
- `$sum` - Calculates sum
- `$avg` - Calculates average
- `$min` - Gets minimum value
- `$max` - Gets maximum value
- `$concat` - Concatenates strings

---

## Performance Best Practices

1. **Create indexes** on frequently queried fields
2. **Use projection** to return only needed fields
3. **Use `$match` early** in aggregation pipelines
4. **Limit results** when you don't need all documents
5. **Use covered queries** (queries that can be satisfied entirely from an index)
6. **Monitor with `explain()`** to understand query performance
7. **Use appropriate data types** (numbers as numbers, not strings)
8. **Avoid regex queries** on large collections without indexes

---
