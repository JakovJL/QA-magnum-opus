# OpenSearch

## Table of Contents

- [[#What is OpenSearch]]
- [[#Index and Document]]
- [[#Full-Text Search]]
- [[#Data Indexing]]
- [[#What to Check as a Tester]]
- [[#Typical Mistakes]]
- [[#Interview Questions]]

**Related notes:** [[AQA Java eng]]

---

## What is OpenSearch

**OpenSearch** is a search engine and data store optimized for fast search, filtering, and analytics.

**Goal:** quickly find documents by text and fields when a regular database is not suitable for complex search.

OpenSearch is often used for searching products, logs, events, and analytical data.

---

## Index and Document

- **Index** — a set of documents of one type; similar to a table in purpose, but structured differently;
- **Document** — a data unit in JSON format;
- **Mapping** — field type definitions;
- **Shard** — a part of an index that helps distribute data;
- **Replica** — a copy of a shard for fault tolerance and read performance.

```json
{
  "id": 42,
  "name": "Wireless mouse",
  "category": "accessories",
  "price": 25.50
}
```

---

## Full-Text Search

In full-text search, a string is split into tokens. Therefore, searching a `text` field can find different word forms depending on the configured analyzer.

For exact filtering, a `keyword` field is typically used, while `text` is used for word search.

> [!tip] `text` vs `keyword`
>
> | Field type | Purpose |
> |---|---|
> | `text` | Word search in name or description |
> | `keyword` | Exact value, filter, sort, aggregation |

---

## Data Indexing

Data can be written to OpenSearch directly via API or asynchronously from the main database through a queue and a separate indexing service.

In the second case, there is a delay: the record already exists in the main system but has not yet appeared in the search index. This is called eventual consistency — consistency is not achieved instantly.

Tests must account for this: wait for the document to appear within a configured timeout rather than making a single immediate assertion.

---

## What to Check as a Tester

- whether a new or updated document appeared in the index;
- whether fields and their types are correctly transferred;
- whether the document is found by the expected query;
- whether it is NOT found by a query that should not match;
- whether filters, sorting, and pagination work correctly;
- whether the document disappears from search after deletion or hiding;
- whether the indexing delay meets requirements.

---

## Typical Mistakes

| Mistake | Better approach |
|---|---|
| Checking search immediately after writing | Wait for the document within a configured timeout |
| Expecting exact search from a `text` field | Use `keyword` for exact comparison |
| Checking only the result count | Also verify content and order of results |
| Treating the index as the primary database | Verify the data source and the search index separately |

---

## Interview Questions

### Beginner Questions

**1. What is OpenSearch?**

OpenSearch is a search engine and data store optimized for fast search, filtering, and analytics.

**2. What are the main components of OpenSearch?**

Index (a set of documents), Document (a JSON data unit), Mapping (field type definitions), Shard (a part of an index), Replica (a copy of a shard).

**3. What is the difference between text and keyword fields?**

text — for word search (analyzed into tokens), keyword — for exact values, filtering, and sorting.

**4. What is eventual consistency in the context of OpenSearch?**

The delay between writing to the main system and the data appearing in the search index.

### Intermediate Questions

**1. Why should you not check search results immediately after writing data?**

Due to eventual consistency, data may not appear in the index instantly. You need to wait within a configured timeout.

**2. What is a mapping in OpenSearch?**

A description of document fields and their types. It defines how data is stored and indexed.

**3. What should a tester check when working with OpenSearch?**

Whether the document appeared in the index; whether fields and types are correct; whether the document is found by the expected query; whether it is not found by a query that should not match; whether filters, sorting, and pagination work correctly.

### Advanced Questions

**1. Why is checking only the result count different from checking the content?**

Checking only the count can miss an error: the right number of documents is returned, but the wrong ones. Content and order must be verified as well.

**2. Why should OpenSearch not be treated as the primary database?**

OpenSearch is optimized for search, not transactional reliability. Data can be lost on failure if replicas are not configured. The data source and the search index should be verified separately.

**3. When is a text field unsuitable for search?**

When exact matching is needed. text splits strings into tokens, so searching for an exact value may not find the document. keyword should be used for exact comparison.

### Code Questions

**1. What does this document represent?**

```json
{
  "id": 42,
  "name": "Wireless mouse",
  "category": "accessories",
  "price": 25.50
}
```

**Answer:** An OpenSearch document example: id is a numeric identifier, name is the product name, category is the product category, price is the price.

**2. What mistake does this test make?**

```java
assertThat(searchResults.getTotalHits(), equalTo(3));
```

**Answer:** Only the number of results is checked, not their content or order. The test passes even if the wrong documents are returned.
