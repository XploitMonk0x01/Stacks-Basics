# MongoDB & Mongoose: The Complete Guide

This guide provides a comprehensive overview of MongoDB and Mongoose, from basic shell commands to advanced data modeling in a Node.js environment.

## Table of Contents

1.  [Introduction to MongoDB](#1-introduction-to-mongodb)
2.  [Basic Shell Commands (CRUD)](#2-basic-shell-commands-crud)
    - [Create: Inserting Documents](#create-inserting-documents)
    - [Read: Finding Documents](#read-finding-documents)
    - [Update: Modifying Documents](#update-modifying-documents)
    - [Delete: Removing Documents](#delete-removing-documents)
3.  [Querying in Detail](#3-querying-in-detail)
    - [Query Selectors (`$lt`, `$gt`)](#query-selectors-lt-gt)
    - [Projections: Limiting Fields](#projections-limiting-fields)
    - [Sorting, Skipping, and Limiting](#sorting-skipping-and-limiting)
4.  [Introduction to Mongoose](#4-introduction-to-mongoose)
    - [What is Mongoose?](#what-is-mongoose)
    - [Schemas & Models](#schemas--models)
5.  [Working with Mongoose in Node.js](#5-working-with-mongoose-in-nodejs)
    - [Connecting to the Database](#connecting-to-the-database)
    - [Defining a Schema](#defining-a-schema)
    - [Creating a Model](#creating-a-model)
    - [Performing Mongoose Operations](#performing-mongoose-operations)

---

## 1. Introduction to MongoDB

MongoDB is a NoSQL database that stores data in flexible, JSON-like documents. This "document model" maps directly to objects in your application code, making it intuitive to work with. Instead of tables and rows, you have **collections** and **documents**.

- **Document**: A single record, structured with fields and values (similar to a JSON object).
- **Collection**: A group of documents (similar to a table in a SQL database).

---

## 2. Basic Shell Commands (CRUD)

These commands are used directly in the MongoDB shell (`mongosh`).

### Create: Inserting Documents

- **Insert a single document**:
  Use `db.collection.insertOne()` to add one document to a collection.

  ```javascript
  db.movies.insertOne({ name: "Extraction", rating: 4 });
  ```

- **Insert multiple documents**:
  Use `db.collection.insertMany()` to add an array of documents.

  ```javascript
  db.movies.insertMany([
    { name: "Sita Ramam", rating: 5 },
    { name: "Hi Nanna", rating: 5 },
  ]);
  ```

### Read: Finding Documents

- **Find all documents**:
  Use `db.collection.find()` to retrieve all documents in a collection.

  ```javascript
  db.movies.find();
  ```

- **Find documents matching a filter**:
  Pass a query object to `find()` to filter results.

  ```javascript
  // Finds all movies with a rating of 4
  db.movies.find({ rating: 4 });
  ```

### Update: Modifying Documents

- **Update a single document**:
  Use `db.collection.updateOne()` with a filter and an update operator like `$set`.

  ```javascript
  // Changes the rating of "Hi Nanna" to 9
  db.movies.updateOne({ name: "Hi Nanna" }, { $set: { rating: 9 } });
  ```

- **Update multiple documents**:
  Use `db.collection.updateMany()` to modify all documents matching the filter.

  ```javascript
  // Changes the rating of all movies with a rating of 4 to 3
  db.movies.updateMany({ rating: 4 }, { $set: { rating: 3 } });
  ```

### Delete: Removing Documents

- **Delete a single document**:
  Use `db.collection.deleteOne()` to remove the first document that matches the filter.

  ```javascript
  db.movies.deleteOne({ name: "Extraction" });
  ```

- **Delete multiple documents**:
  Use `db.collection.deleteMany()` to remove all documents matching the filter.

  ```javascript
  // Deletes all movies with a rating of 5
  db.movies.deleteMany({ rating: 5 });
  ```

---

## 3. Querying in Detail

### Query Selectors (`$lt`, `$gt`)

Use query operators to perform comparisons.

- `$lt`: Less than
- `$lte`: Less than or equal to
- `$gt`: Greater than
- `$gte`: Greater than or equal to

```javascript
// Find movies with a rating less than 5
db.movies.find({ rating: { $lt: 5 } });
```

### Projections: Limiting Fields

Use projections to control which fields are returned in your query results.

- `1`: Include the field.
- `0`: Exclude the field.

```javascript
// Returns only the 'name' field (and _id by default)
db.movies.find({}, { name: 1 });

// Returns all fields EXCEPT 'name'
db.movies.find({}, { name: 0 });
```

### Sorting, Skipping, and Limiting

Chain these methods to `find()` to control the output.

- `sort()`: Orders the results. `1` for ascending, `-1` for descending.
- `limit()`: Restricts the number of documents returned.
- `skip()`: Skips a specified number of documents.

```javascript
// Get the second movie when sorted alphabetically by name
db.movies
  .find({}, { name: 1 })
  .sort({ name: 1 }) // Sort by name ascending
  .skip(1) // Skip the first document
  .limit(1); // Return only one document
```

---

## 4. Introduction to Mongoose

### What is Mongoose?

Mongoose is an **Object Data Modeling (ODM)** library for Node.js and MongoDB. It provides a higher-level abstraction that makes working with MongoDB easier by:

- Managing relationships between data.
- Providing schema validation to enforce data structure.
- Translating between objects in your code and their representation in MongoDB.

### Schemas & Models

- **Schema**: The blueprint that defines the structure and rules for documents in a collection. It specifies field types, default values, and validators.
- **Model**: A constructor function compiled from a Schema definition. An instance of a model is a document that can be saved to the database. Models are responsible for all create, read, update, and delete (CRUD) operations.

---

## 5. Working with Mongoose in Node.js

This section is based on the code in the `Web Dev/Moongoose` directory.

### Connecting to the Database

First, you need to establish a connection to your MongoDB instance. This is typically done once when your application starts.

**File: `db/connectDB.js`**

```javascript
import mongoose from "mongoose";

const connectDB = async (DATABASE_URL) => {
  try {
    await mongoose.connect(DATABASE_URL);
    console.log("Database connected...");
  } catch (error) {
    console.log(error);
  }
};

export default connectDB;
```

### Defining a Schema

A schema defines the shape of your documents. You can specify types, validation rules, and more.

**File: `models/Movies.js`**

```javascript
import Mongoose from "mongoose";

const movieSchema = new Mongoose.Schema({
  name: { type: String, required: true, trim: true },
  ratings: { type: Number, required: true, min: 1, max: 9 },
  money: {
    type: Mongoose.Decimal128,
    required: true,
    validate: (v) => v >= 10, // Custom validator
  },
  genre: { type: Array },
  isActive: { type: Boolean },
  comments: [
    { value: { type: String }, published: { type: Date, default: Date.now } },
  ],
});
```

You can also add `timestamps: true` as a second argument to the schema to automatically add `createdAt` and `updatedAt` fields.

**Example from `mongodb.txt`:**

```javascript
const userSchema = new mongoose.Schema(
  {
    username: {
      type: String,
      required: true,
      unique: true,
      lowercase: true,
    },
    email: {
      type: String,
      required: true,
      unique: true,
      lowercase: true,
    },
    password: {
      type: String,
      required: true,
    },
  },
  {
    timestamps: true, // Automatically adds createdAt and updatedAt
  }
);
```

### Creating a Model

Once you have a schema, you compile it into a model. The model is your primary tool for interacting with the collection.

```javascript
// Creating a Model for the 'movies' collection
const MovieModel = Mongoose.model("Movie", movieSchema);
// Mongoose automatically looks for the plural, lowercased version of your model name.
// In this case, "Movie" becomes the "movies" collection.
```

### Performing Mongoose Operations

The code in `models/Movies.js` shows various ways to interact with the database using the `MovieModel`.

- **Create Documents**:

  ```javascript
  const m1 = new MovieModel({ name: "Dune", ratings: 8, money: 500 });
  const result = await m1.save(); // Saves a single document

  // Or insert many
  const result = await MovieModel.insertMany([m1, m2, m3]);
  ```

- **Read Documents**:

  ```javascript
  const result = await MovieModel.find(); // Get all documents
  const result = await MovieModel.findById("65e6115fb6b3db0e268fc2af"); // Find by ID
  const result = await MovieModel.find({ name: "Hi Nanna" }); // Find by filter
  ```

- **Update Documents**:

  ```javascript
  const result = await MovieModel.updateOne(
    { _id: id },
    { name: "Mrunal Thakur" }
  );
  const result = await MovieModel.findByIdAndUpdate(id, { ratings: 9 });
  ```

- **Delete Documents**:
  ```javascript
  const result = await MovieModel.findByIdAndDelete("65e6115fb6b3db0e268fc2b5");
  const result = await MovieModel.deleteOne({ name: "Hi Nanna" });
  const result = await MovieModel.deleteMany({ ratings: 4 });
  ```
