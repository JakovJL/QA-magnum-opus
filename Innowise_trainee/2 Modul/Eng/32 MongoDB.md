# MongoDB

## Table of Contents

- [[#What is MongoDB]]
- [[#Documents and Collections]]
- [[#Queries and Indexes]]
- [[#Embedded Data]]
- [[#What to Check as a Tester]]
- [[#Common Mistakes]]
- [[#Interview Questions]]
- [[#Practical Questions]]

**Related notes:** [[AQA Java eng]]

---

## What is MongoDB

**MongoDB** is a document-oriented database. It stores data in JSON-like documents; internally MongoDB uses the BSON format.

**Goal:** convenient storage of data with a flexible or nested structure.

MongoDB does not require every document to have the same set of fields, but in a production project the structure rules should still be defined by contract or validation.

---

## Documents and Collections

- **Document** — a single data object, similar to a JSON object;
- **Collection** — a set of documents of the same type;
- **Database** — a set of collections;
- `_id` — the unique identifier of a document.

```json
{
  "_id": "user-42",
  "name": "Ana",
  "roles": ["customer"],
  "address": {"city": "Minsk"}
}
```

In a relational database, address and roles would often be in separate tables. In MongoDB they can be stored inside one document if they are usually read and changed together.

---

## Queries and Indexes

A query searches for documents by fields and conditions:

```javascript
db.users.find({ name: "Ana", roles: "customer" })
```

An **index** speeds up search and sorting but slows down writes and takes up space. Indexes are needed for frequent queries, especially on filter and sort fields.

> [!caution] Index does not replace query verification
> Even a fast query can return wrong data if the filter, field type, or nested path is incorrect.

---

## Embedded Data

MongoDB supports two main approaches:

- **embedding** — store related data inside the document;
- **referencing** — store the identifier of another document.

Embedding is convenient for small data that is used together. Referencing is better when data is large, reused often, or changes independently.

---

## What to Check as a Tester

- whether a document was created after an API request or event;
- whether types are saved correctly: number, string, date, array, nested object;
- whether only the right fields are updated;
- whether a duplicate is created on a repeated operation;
- whether filtering, sorting, and pagination work correctly;
- whether there is an index for a critical query if it is a performance requirement.

---

## Common Mistakes

| Mistake | How to do better |
|---|---|
| Comparing the entire document with dynamic fields | Check meaningful fields and types |
| Not accounting for nested objects and arrays | Add separate checks for their structure |
| Assuming no schema means no rules | Verify contract and data validation |
| Using one test set for all scenarios | Isolate and clean test data |

---

## Interview Questions

### Beginner Questions

**1. What is MongoDB?**

MongoDB is a document-oriented database that stores data in JSON-like documents (BSON format).

**2. What is the difference between a Document and a Collection?**

A Document is a single data object. A Collection is a set of documents of the same type.

**3. What is _id in MongoDB?**

The unique identifier of a document.

**4. What approaches does MongoDB support for related data?**

Embedding (storing inside the document) and referencing (storing the identifier of another document).

### Intermediate Questions

**1. Why are indexes needed in MongoDB?**

Indexes speed up search and sorting but slow down writes and take up space.

**2. When should you use embedding vs referencing?**

Embedding is good for small data that is read and changed together. Referencing is better when data is large, reused often, or changes independently.

**3. What should a tester check when working with MongoDB?**

Whether the document was created after a request; whether types are saved correctly; whether only the right fields are updated; whether there are duplicates; whether filtering and pagination work correctly.

### Advanced Questions

**1. Why does the absence of a required schema in MongoDB not mean there are no rules?**

In a production project, document structure must be defined by contract or validation, otherwise unexpected data formats and processing errors can occur.

**2. Why should you avoid comparing an entire document in tests?**

Documents may contain dynamic fields (dates, IDs, counters). Comparing the entire document makes the test brittle. Only meaningful fields and types should be checked.

**3. Why does an index not replace query verification?**

A fast query can still return wrong data if the filter, field type, or nested path is incorrect.

### Code Questions

**1. What does this query find?**

```javascript
db.users.find({ name: "Ana", roles: "customer" })
```

**Answer:** All documents in the users collection where name equals "Ana" and roles contains "customer".

**2. What is the difference between this MongoDB document and a relational table record?**

```json
{
  "_id": "user-42",
  "name": "Ana",
  "roles": ["customer"],
  "address": {"city": "Minsk"}
}
```

**Answer:** In a relational database, roles and address would usually be in separate tables. In MongoDB, they can be part of one document, which is convenient when they are read and changed together.

### Practical Questions

**1. How do you verify a document was saved after an API call?**

Query the MongoDB collection directly using the Java driver or a test utility:

```java
Document doc = collection.find(eq("email", "ana@example.com")).first();
assertNotNull(doc);
```

**2. How do you check that only the correct fields were updated?**

Fetch the document after the update and assert specific field values, not the entire document:

```java
Document doc = collection.find(eq("_id", "user-42")).first();
assertEquals("New Name", doc.getString("name"));
assertNull(doc.getString("oldField")); // should not exist
```

**3. How do you test filtering and pagination in queries?**

Insert several documents with different field values, then run FindIterable with filters, skip, and limit, and assert the results.

**4. How do you assert on embedded documents?**

Use dot notation in the query or extract the embedded subdocument:

```java
Document address = (Document) doc.get("address");
assertEquals("Minsk", address.getString("city"));
```

**5. How do you ensure no duplicate documents are created?**

Insert the same data twice and assert that `countDocuments(filter)` returns 1, not 2.

**6. How do you test that a document was deleted correctly?**

Call the delete endpoint, then assert that `collection.find(eq("_id", id)).first()` returns null.

**7. How do you verify data types in MongoDB tests?**

```java
assertTrue(doc.get("price") instanceof Number);
assertTrue(doc.get("createdAt") instanceof Date);
```

**8. How do you test with a real MongoDB vs an embedded one?**

Use a real MongoDB instance (e.g., Testcontainers) for integration tests. Embedded MongoDB may behave differently in areas like indexing and replication.

**9. How do you clean up test data between tests?**

Drop the test collection or delete specific documents in `@BeforeEach` / `@AfterEach`:

```java
collection.deleteMany(Filters.empty());
```

**10. How do you check that an index is being used?**

Run `collection.find(filter).explain()` and check the `stage` field in the result. If it says `COLLSCAN`, the index is not used.
