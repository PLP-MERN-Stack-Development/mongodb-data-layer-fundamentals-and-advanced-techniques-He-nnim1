# README

This repository contains two example Node.js scripts for working with a MongoDB `plp_bookstore` database and a `books` collection:

* `insert_books.js` — inserts a set of sample books into the database (drops collection first if it already exists).
* `queries.js` — runs a variety of example queries, aggregations, sorting, pagination, indexing, and `explain()` checks.

---

## Prerequisites

1. **Node.js** installed (v14+ recommended). Verify with:

   ```powershell
   node -v
   npm -v
   ```

2. **MongoDB**

   * A local MongoDB server running on `mongodb://localhost:27017` **OR** an Atlas cluster connection string (replace `uri` in the scripts).
   * If using MongoDB Compass, point it to the same URI to inspect the data visually.

3. From the project folder, install required npm package:

```bash
npm init -y
npm install mongodb
```

---

## Files

* `insert_books.js` — inserts a number of sample book documents into `plp_bookstore.books`. The script will drop the collection if it already has documents, then insert the sample data.

* `queries.js` — connects to the same DB and demonstrates:

  * `find()` queries (filters, case sensitivity notes)
  * Projections (`{ projection: { ... } }`) and how to hide `_id`
  * Sorting, pagination (skip/limit)
  * Aggregation pipelines (group, avg, grouping by decade)
  * Index creation with `createIndex()`
  * Using `.explain('executionStats')` on queries to inspect performance

> Tip: The scripts assume the fields are named `published_year`, `price`, `genre`, `author`, `title`, and `in_stock`. If your documents use different field names, update the queries accordingly.

---

## How to run

From the repository folder (where the `.js` files live):

### 1. Start MongoDB (if local)

* Windows (installed as a service): MongoDB may already be running.
* If you run mongod manually: open a terminal and run `mongod`.

### 2. Insert sample data

```bash
node insert_books.js
```

Expected outcome: script logs connection, drops collection if needed, inserts documents and lists inserted titles.

### 3. Run example queries

```bash
node queries.js
```

Expected outcome: the script connects, runs queries, logs results for the `find`, `aggregate`, `sort`, `pagination`, creates indexes, and prints simplified `explain()` stats (execution time, docs examined, keys examined).

---

## Common problems & fixes

* **`SyntaxError: Unexpected end of input`**

  * Usually a JavaScript syntax problem (missing `}` or unclosed string). Ensure files are saved and braces/quotes are balanced.

* **`ReferenceError: Cannot access 'db' before initialization`**

  * Use `const db = client.db('plp_bookstore')` (i.e. call `client.db(...)`, not `db.database(...)`).

* **Connection refused / failed to connect**

  * Make sure `mongod` is running locally, or update `uri` to your Atlas connection string (include credentials and required query params).

* **Empty query results (`[]`)**

  * Check field names and types. e.g., `published_year` must be a number to use `$gt: 2012`. Queries are case-sensitive by default (`"Fantasy"` ≠ `"fantasy"`).

* **`createIndex(...).explain()` error**

  * `createIndex()` returns an index name (string). Call `.explain()` on a query cursor, e.g. `collection.find({ title: 'X' }).explain('executionStats')`.

* **Large `explain()` output**

  * Use `plan.executionStats.executionTimeMillis`, `plan.executionStats.totalDocsExamined`, and `plan.executionStats.totalKeysExamined` to print only the key numbers, or write the full explain to a file with `fs.writeFileSync()` for inspection.

---

## Customizing for Atlas

If you want to use MongoDB Atlas instead of a local mongod:

1. Replace the `uri` variable in both scripts with your Atlas connection string (wrap it in quotes). Example:

```js
const uri = "mongodb+srv://<user>:<password>@cluster0.example.mongodb.net/?retryWrites=true&w=majority";
```

2. Ensure your IP address is whitelisted in Atlas or use the 0.0.0.0/0 temporary access while testing (not recommended for production).

3. Install any necessary CA certs if your environment requires it (most setups will work without extra steps).
