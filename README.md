Here is the **complete MongoDB query list** for all **CRUD operations** — **Create**, **Read**, **Update**, and **Delete** — including various use cases like filtering, updating with conditions, projections, and logical operators.

---

# 📘 MongoDB Complete CRUD Query List

---
#
show databases
show collections

use flipkart
db.products.insertMany()

db.createCollection("dbname")

## 🔸 C - CREATE

### 🔹 Insert a single document

```js
db.products.insertOne({ name: "Shirt", price: 499, category: "men's clothing" });
```

### 🔹 Insert multiple documents

```js
db.products.insertMany([
  { name: "Jeans", price: 999, category: "men's clothing" },
  { name: "Dress", price: 799, category: "women's clothing" }
]);
```

---

## 🔸 R - READ

### 🔹 Get all documents

```js
db.products.find();
```

### 🔹 Get documents with a condition

```js
db.products.find({ price: 499 });         // exact match
db.products.find({ category: "books" });  // match by category
```

### 🔹 Comparison operators

```js
db.products.find({ price: { $gt: 500 } });    // price > 500
db.products.find({ price: { $gte: 500 } });   // price >= 500
db.products.find({ price: { $lt: 1000 } });   // price < 1000
db.products.find({ price: { $lte: 1000 } });  // price <= 1000
db.products.find({ price: { $ne: 999 } });    // price != 999
```

### 🔹 Logical operators

```js
db.products.find({
  $and: [
    { price: { $gt: 500 } },
    { category: "men's clothing" }
  ]
});

db.products.find({
  $or: [
    { price: { $lt: 500 } },
    { category: "books" }
  ]
});

db.products.find({
  price: { $not: { $gte: 1000 } }  // price < 1000
});
```

### 🔹 Projection (return specific fields)

```js
db.products.find({}, { name: 1, price: 1, _id: 0 });
```

### 🔹 Limit and Sort

```js
db.products.find().limit(5);                      // Limit to 5 results
db.products.find().sort({ price: 1 });            // Sort ascending
db.products.find().sort({ price: -1 });           // Sort descending
```

---

## 🔸 U - UPDATE

### 🔹 Update one document

```js
db.products.updateOne(
  { name: "Shirt" },
  { $set: { price: 599 } }
);
```

### 🔹 Update many documents

```js
db.products.updateMany(
  { category: "men's clothing" },
  { $set: { discount: true } }
);
```

### 🔹 Replace a document

```js
db.products.replaceOne(
  { name: "Shirt" },
  { name: "T-Shirt", price: 499, category: "men's clothing" }
);
```

### 🔹 Increment/Decrement a value

```js
db.products.updateOne(
  { name: "Shirt" },
  { $inc: { price: 100 } }     // increase price by 100
);
```

### 🔹 Update using \$unset (remove field)

```js
db.products.updateOne(
  { name: "Shirt" },
  { $unset: { discount: "" } }
);
```

---

## 🔸 D - DELETE

### 🔹 Delete one document

```js
db.products.deleteOne({ name: "Shirt" });
```

### 🔹 Delete many documents

```js
db.products.deleteMany({ category: "expired" });
```

---

## 🔸 Extra: Count, Exists, etc.

### 🔹 Count documents

```js
db.products.countDocuments();                          // total count
db.products.countDocuments({ category: "books" });     // count with condition
```

### 🔹 Check if a field exists

```js
db.products.find({ discount: { $exists: true } });
```

---

## 🔸 Create Collection & Drop

### 🔹 Create collection manually

```js
db.createCollection("products");
```

### 🔹 Drop a collection

```js
db.products.drop();
```

### 🔹 Drop a database

```js
use mydb;
db.dropDatabase();
```

---

Great! Based on the dataset you provided (a product catalog with fields like `title`, `price`, `category`, `rating.rate`, `rating.count`, etc.), here’s a **complete explanation of important MongoDB aggregation functions** using this structure.

---

# 📊 MongoDB Aggregation Functions — Full Guide (with Examples)

The **Aggregation Pipeline** in MongoDB transforms documents into aggregated results through multiple stages like `$match`, `$group`, `$project`, `$sort`, etc.

---

## 🧱 Basic Structure

```js
db.products.aggregate([
  { stage1 },
  { stage2 },
  ...
]);
```

---

## 🔍 1. `$match` — Filter Documents (like WHERE)

```js
// All products in "electronics" category
db.products.aggregate([
  { $match: { category: "electronics" } }
]);
```

---

## 🧮 2. `$group` — Group Data and Apply Accumulators

```js
// Average price per category
db.products.aggregate([
  {
    $group: {
      _id: "$category",
      avgPrice: { $avg: "$price" },
      totalProducts: { $sum: 1 }
    }
  }
]);
```

✅ Common Accumulators:

* `$sum`: Total
* `$avg`: Average
* `$min`: Minimum
* `$max`: Maximum
* `$push`: Push values into an array
* `$addToSet`: Add unique values into an array

---

## 📤 3. `$project` — Show/Transform Specific Fields

```js
// Show only title and price, and create a new field
db.products.aggregate([
  {
    $project: {
      _id: 0,
      title: 1,
      price: 1,
      discountedPrice: { $multiply: ["$price", 0.9] } // 10% off
    }
  }
]);
```

---

## 📶 4. `$sort` — Sort Results

```js
// Sort by price descending
db.products.aggregate([
  { $sort: { price: -1 } }
]);
```

---

## 🔢 5. `$count` — Count Total Documents

```js
db.products.aggregate([
  { $count: "totalProducts" }
]);
```

---

## 🔄 6. `$limit` and `$skip` — Pagination

```js
// Skip first 5, then show 5
db.products.aggregate([
  { $skip: 5 },
  { $limit: 5 }
]);
```

---

## 🧠 7. `$unwind` — Flatten Arrays (not needed in this dataset unless you have arrays)

If each product had a `tags: ["electronics", "ssd"]`, then:

```js
{ $unwind: "$tags" }
```

---

## 🔎 8. `$match` with comparison operators

```js
// Products with rating.rate > 4
db.products.aggregate([
  { $match: { "rating.rate": { $gt: 4 } } }
]);
```

---

## 🥇 9. Top Rated Products per Category

```js
db.products.aggregate([
  { $sort: { "rating.rate": -1 } },
  {
    $group: {
      _id: "$category",
      topProduct: { $first: "$title" },
      topRating: { $first: "$rating.rate" }
    }
  }
]);
```

---

## 💯 10. Average Rating per Category

```js
db.products.aggregate([
  {
    $group: {
      _id: "$category",
      avgRating: { $avg: "$rating.rate" },
      totalRatings: { $sum: "$rating.count" }
    }
  }
]);
```

---

## 💰 11. Highest Priced Product in Each Category

```js
db.products.aggregate([
  { $sort: { price: -1 } },
  {
    $group: {
      _id: "$category",
      mostExpensive: { $first: "$title" },
      maxPrice: { $first: "$price" }
    }
  }
]);
```

---

## 🧾 12. Products under ₹100 with High Ratings (>4)

```js
db.products.aggregate([
  { $match: { price: { $lt: 100 }, "rating.rate": { $gt: 4 } } },
  { $project: { title: 1, price: 1, "rating.rate": 1, _id: 0 } }
]);
```

---

## 📊 13. Count of Products by Rating Buckets

```js
db.products.aggregate([
  {
    $bucket: {
      groupBy: "$rating.rate",
      boundaries: [0, 2, 3, 4, 5],
      default: "other",
      output: {
        count: { $sum: 1 },
        titles: { $push: "$title" }
      }
    }
  }
]);
```

---

## 🆚 14. Compare Men’s vs Women’s Clothing Average Price

```js
db.products.aggregate([
  {
    $match: { category: { $in: ["men's clothing", "women's clothing"] } }
  },
  {
    $group: {
      _id: "$category",
      avgPrice: { $avg: "$price" }
    }
  }
]);
```

---

## 🛍️ 15. Products with rating count > 400 sorted by rating

```js
db.products.aggregate([
  { $match: { "rating.count": { $gt: 400 } } },
  { $sort: { "rating.rate": -1 } },
  { $project: { title: 1, "rating.rate": 1, "rating.count": 1, _id: 0 } }
]);
```

---

## 🧼 Clean Version — Just Titles of Products < ₹50

```js
db.products.aggregate([
  { $match: { price: { $lt: 50 } } },
  { $project: { _id: 0, title: 1 } }
]);
```

---

Let me know if you want all this exported as a `.md` or `.pdf` file, or added to a Notion page or your README.


---

Let me know if you want this saved as a Notion page, exported as a PDF, or added to your README file.
