# 13 Databases

## Table of Contents

- [[#Core Concepts]]
- [[#Types of Databases]]
	- [[#Relational Databases (SQL)]]
	- [[#NoSQL Databases]]
- [[#Relational Databases]]
	- [[#Key Concepts]]
	- [[#SQL Basics for QA]]
- [[#SQL Tools]]
	- [[#DBeaver]]
	- [[#MySQL Workbench]]
	- [[#pgAdmin]]
	- [[#TablePlus]]
- [[#MongoDB]]
	- [[#Document Model]]
	- [[#Basic Queries]]
	- [[#MongoDB Tools]]
- [[#Interview Questions]]
	- [[#Beginner Questions]]
	- [[#Intermediate Questions]]
	- [[#Advanced Questions]]
	- [[#Code Questions]]

**Related notes:** [[QA manual eng]]

---

## Core Concepts

**Database** — an organized collection of structured data stored electronically. Data is stored so it can be retrieved, queried, and updated efficiently.

**DBMS** (Database Management System) — software that manages the database. It handles storage, retrieval, security, concurrency, and backup.

**Examples of DBMS:** MySQL, PostgreSQL, SQLite, Oracle, Microsoft SQL Server, MongoDB, Redis.

**Why QA engineers need databases:**
- Verify that data is saved correctly after a user action
- Check data integrity — no duplicates, no orphaned records
- Validate business logic by querying data directly
- Reproduce and investigate bugs that involve data state
- Understand error messages that reference table names or constraints

---

## Types of Databases

### Relational Databases (SQL)

Data is organized in **tables** with fixed columns and rows. Tables are connected via relationships (foreign keys). Uses SQL (Structured Query Language) for all operations.

**Examples:** MySQL, PostgreSQL, SQLite, Oracle, Microsoft SQL Server

**Best for:** Structured data with well-defined relationships — user accounts, orders, transactions, inventory.

---

### NoSQL Databases

Flexible schema — structure can vary per record. Multiple sub-types for different use cases.

| Sub-type | Structure | Examples | Best for |
|---|---|---|---|
| Document | JSON-like documents | MongoDB, CouchDB | User profiles, catalogs, content |
| Key-Value | Key → Value pairs | Redis, DynamoDB | Caching, sessions, simple lookups |
| Column-family | Column-oriented | Cassandra, HBase | Time-series, analytics, large scale |
| Graph | Nodes and edges | Neo4j, Amazon Neptune | Social graphs, recommendation engines |

> [!tip] Relational vs NoSQL
> | | Relational (SQL) | NoSQL |
> |---|---|---|
> | **Schema** | Fixed — defined upfront | Flexible — can vary per record |
> | **Relationships** | Foreign keys, JOINs | Embedded data or application-level |
> | **Scaling** | Vertical (bigger server) | Horizontal (more servers) |
> | **Query language** | SQL (standardized) | Varies per system |
> | **Consistency** | Strong (ACID) | Often eventual consistency |
> | **Best for** | Complex relationships, transactions | Large scale, evolving schema |

---

## Relational Databases

### Key Concepts

**Table** — a collection of related data organized in rows and columns. Equivalent to a spreadsheet tab.

**Row (Record)** — a single entry in a table. Represents one instance of the entity.

**Column (Field / Attribute)** — a property of the entity. Every row has the same columns.

**Primary Key (PK)** — a column (or combination of columns) that uniquely identifies each row. Cannot be NULL, must be unique.

**Foreign Key (FK)** — a column in one table that references the Primary Key of another table. Creates a relationship between tables.

**Index** — a data structure that speeds up data retrieval. Without an index, a query scans the full table. Indexes trade faster reads for slower writes and more storage.

**NULL** — the absence of a value. Different from zero or empty string. `NULL != NULL` in SQL logic.

**Example — simple e-commerce schema:**

| users table | orders table |
|---|---|
| **id** (PK) | **id** (PK) |
| name | user_id (FK → users.id) |
| email | total_price |
| created_at | status |

`orders.user_id` points to `users.id` — this is the relationship.

---

### SQL Basics for QA

**SELECT** — retrieve data from a table.

```sql
SELECT * FROM users;                          -- all columns, all rows
SELECT name, email FROM users;                -- specific columns
SELECT * FROM users WHERE email = 'a@b.com'; -- filtered
```

**WHERE** — filter rows by condition.

```sql
SELECT * FROM orders WHERE status = 'pending';
SELECT * FROM orders WHERE total_price > 100;
SELECT * FROM users WHERE created_at >= '2024-01-01';
```

**JOIN** — combine data from two tables using the relationship.

```sql
-- Get each order with the user's name
SELECT orders.id, users.name, orders.total_price
FROM orders
JOIN users ON orders.user_id = users.id;
```

**JOIN types:** A plain `JOIN` (also called `INNER JOIN`) returns only rows that have a match in *both* tables. A `LEFT JOIN` keeps every row from the left table even when there is no match on the right (the missing columns come back as `NULL`). For a QA this matters: an `INNER JOIN` can silently drop rows that have no match, which can hide missing or orphaned data. A JOIN can also produce **duplicate rows** if the join key is not unique, so always check key uniqueness before trusting a joined result.

**ORDER BY / LIMIT** — sort and limit results.

```sql
SELECT * FROM orders ORDER BY created_at DESC LIMIT 10;
```

**COUNT / GROUP BY** — aggregate data.

```sql
SELECT status, COUNT(*) FROM orders GROUP BY status;
```

**INSERT / UPDATE / DELETE** — modify data (use carefully in test environments).

```sql
INSERT INTO users (name, email) VALUES ('Alice', 'alice@example.com');
UPDATE orders SET status = 'shipped' WHERE id = 42;
DELETE FROM users WHERE id = 99;
```

> [!caution] Database writes in testing
> Always verify the expected environment before running INSERT/UPDATE/DELETE. Running these on a production database by mistake can cause data loss. Use transactions (`BEGIN` / `ROLLBACK`) when testing destructive queries.

---

## SQL Tools

### DBeaver

**Free, cross-platform** database GUI. Supports MySQL, PostgreSQL, SQLite, Oracle, MSSQL, MongoDB, and more from one tool.

**Key features:**
- SQL editor with syntax highlighting and autocomplete
- Database browser (view tables, columns, indexes)
- Data viewer (view and edit table data)
- ER diagrams for visualizing schema
- Export data to CSV, Excel, JSON

**Best for:** QA engineers who work with multiple database types.

---

### MySQL Workbench

Official GUI for **MySQL** databases. Free.

**Key features:**
- Visual ER diagram editor
- SQL editor
- Server management and monitoring
- Data import/export

---

### pgAdmin

Official GUI for **PostgreSQL**. Free.

**Key features:**
- Query tool with explain/analyze plans
- Object browser
- Server dashboard and monitoring
- Backup and restore

---

### TablePlus

Modern, fast GUI supporting many databases. Available for macOS, Windows, Linux.

**Key features:**
- Clean, minimal UI
- Multiple connections in tabs
- Quick data editing
- Supports MySQL, PostgreSQL, SQLite, MongoDB, Redis and more

---

## MongoDB

### Document Model

MongoDB is a **document database** — data is stored as JSON-like documents (internally BSON — Binary JSON).

| Relational concept | MongoDB equivalent |
|---|---|
| Database | Database |
| Table | Collection |
| Row | Document |
| Column | Field |
| Primary Key | `_id` field (auto-generated) |
| JOIN | `$lookup` aggregation / embedded documents |

A **document** is a flexible JSON object — fields can vary per document, and nested objects/arrays are supported.

```json
{
  "_id": "64a3f1c2e3b4d5e6f7890123",
  "name": "Alice",
  "email": "alice@example.com",
  "orders": [
    { "item": "Laptop", "price": 1200 },
    { "item": "Mouse", "price": 25 }
  ]
}
```

**Advantages over relational:**
- No schema migration needed when adding fields
- Nested data avoids JOIN complexity
- Scales horizontally by design

**Disadvantages:**
- No JOINs by default — relationships are handled in application code or with `$lookup`
- No ACID transactions across multiple collections (added in MongoDB 4.0, but with limits)
- Duplication of data is common

---

### Basic Queries

```javascript
// Find all documents in a collection
db.users.find()

// Find with filter
db.users.find({ email: "alice@example.com" })

// Find with multiple conditions
db.orders.find({ status: "pending", total: { $gt: 100 } })

// Find one document
db.users.findOne({ name: "Alice" })

// Insert a document
db.users.insertOne({ name: "Bob", email: "bob@example.com" })

// Update a document
db.users.updateOne({ name: "Bob" }, { $set: { email: "new@example.com" } })

// Delete a document
db.users.deleteOne({ name: "Bob" })

// Count documents
db.orders.countDocuments({ status: "shipped" })
```

**Common query operators:**

| Operator | Meaning | Example |
|---|---|---|
| `$eq` | Equals | `{ age: { $eq: 30 } }` |
| `$gt` / `$gte` | Greater than / or equal | `{ price: { $gt: 100 } }` |
| `$lt` / `$lte` | Less than / or equal | `{ price: { $lt: 50 } }` |
| `$ne` | Not equal | `{ status: { $ne: "cancelled" } }` |
| `$in` | Value is in a list | `{ status: { $in: ["pending", "processing"] } }` |
| `$exists` | Field exists | `{ phone: { $exists: true } }` |

---

### MongoDB Tools

**MongoDB Compass** — official desktop GUI for MongoDB.
- Visual query builder
- Document viewer and editor
- Schema visualization
- Aggregation pipeline builder
- Index management

**mongosh** (MongoDB Shell) — official command-line interface. Successor to the old `mongo` shell. Uses JavaScript syntax.

**MongoDB Atlas** — cloud-hosted MongoDB service. Provides a web-based UI for managing databases, running queries, and monitoring.

---

## Interview Questions

### Beginner Questions

**1. What is the difference between a database and a DBMS?**
A database is an organized collection of data. A DBMS (Database Management System) is the software that manages it — handling storage, retrieval, security, concurrency, and backup. MySQL and MongoDB are DBMSs; the data they store is the database.

**2. What is a primary key and a foreign key?**
A primary key uniquely identifies each row in a table and cannot be NULL. A foreign key is a column that references the primary key of another table, creating a relationship between the two tables.

**3. What does a JOIN do?**
A JOIN combines rows from two or more tables based on a related column. For example, joining `orders` and `users` on `user_id` lets you show each order together with the name of the user who placed it.

**4. What does NULL mean in SQL?**
NULL means the absence of a value — it is not zero and not an empty string. In SQL logic, `NULL` is not equal to `NULL`, so comparisons with NULL need `IS NULL` / `IS NOT NULL`.

**5. Name some common database tools a QA might use.**
DBeaver (multi-database GUI), MySQL Workbench, pgAdmin (PostgreSQL), TablePlus, and MongoDB Compass for MongoDB. They let you browse data, run queries, and inspect schema.

---

### Intermediate Questions

**1. Why does a QA engineer need to know SQL?**
To verify that data is saved correctly after an action, check data integrity (no duplicates or orphaned records), validate business logic by querying directly, and reproduce or investigate data-related bugs.

**2. What is the difference between WHERE and a JOIN?**
WHERE filters rows by a condition within the result set. A JOIN combines columns from multiple tables based on a relationship. They are often used together: JOIN to connect tables, WHERE to filter the combined result.

**3. What is the difference between SQL and NoSQL databases?**
SQL (relational) databases store data in tables with a fixed schema and use SQL and JOINs. NoSQL databases have a flexible schema and come in types like document, key-value, column, and graph. SQL fits structured data with relationships; NoSQL fits large-scale or evolving data.

**4. How does MongoDB differ from a relational database?**
MongoDB is a document database — it stores JSON-like documents in collections instead of rows in tables. Its schema is flexible, fields can vary per document, and related data is often embedded rather than joined.

**5. What is an index and what is the trade-off?**
An index is a data structure that speeds up data retrieval, so a query does not scan the whole table. The trade-off is slower writes and extra storage, because the index must be updated and stored.

---

### Advanced Questions

**1. When would NoSQL be a worse choice than SQL?**
When the data has many strong relationships and needs transactions across them — for example, banking or orders with strict consistency. Relational databases enforce these with foreign keys and ACID transactions, while most NoSQL stores favor flexibility over strict consistency.

**2. How can a JOIN hide a bug?**
A JOIN can produce duplicate rows if the join key is not unique, or drop rows if you use an INNER JOIN where a LEFT JOIN was needed. A QA must understand the join type and key uniqueness to trust the result.

**3. In MongoDB, the same field is a string in one document and a number in another. Is that allowed, and why is it risky?**
Yes, MongoDB's flexible schema allows it. It is risky because queries and the application may expect one type, so mixed types cause subtle bugs, failed comparisons, or crashes. Schema validation or application-level checks are needed to avoid it.

**4. Why should QA be careful with UPDATE and DELETE queries?**
They change data, and a missing WHERE clause can affect every row. A QA should first check the target rows with SELECT and use a safe test database or transaction when destructive queries are needed.

**5. What can a database check prove that a UI check cannot?**
It can show whether data was actually saved, updated, or deleted in storage. This helps isolate whether a defect is in the UI, API, business logic, or database layer.

**6. Why are foreign keys useful for data quality?**
Foreign keys protect relationships between tables. They prevent records from pointing to missing parent records, such as an order linked to a user that does not exist.

**7. Why can flexible schemas in NoSQL be both useful and risky?**
They make it easy to store changing data without migrations, which is useful during fast development. The risk is inconsistent documents, unexpected field types, and application code that fails because it expects a stable structure.

---

### Code Questions

**1. Scenario — a user reports their order is missing from the UI. Confirm whether the data actually exists in the database.**

```sql
SELECT * FROM orders WHERE user_id = :user_id;
```

How would you interpret the result to isolate the defect?

**Answer:** If the row exists, the bug is in the UI or API layer; if it does not, the data was never saved. Querying the database directly isolates where the defect actually is.

**2. Given the `users` and `orders` tables, write a query to list each order together with the user's name.**

```sql
SELECT orders.id, users.name, orders.total_price
FROM orders
JOIN users ON orders.user_id = users.id;
```

**Answer:** This joins the two tables on the foreign key `orders.user_id → users.id`, so every order row is returned together with the matching user's name.

**3. You accidentally ran an UPDATE without a WHERE clause on a test database. What happened, and what should you do next time to prevent it?**

```sql
UPDATE orders SET status = 'shipped';   -- every row updated!
```

**Answer:** The UPDATE applied to every row. Next time, write the WHERE clause first, verify the target rows with a SELECT before updating, and wrap destructive queries in a transaction (`BEGIN` / `ROLLBACK`) so they can be undone.
