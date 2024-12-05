[Home](../README.md) > [Anti-Patterns](#)

# Anti-Patterns

The [Anti-Patterns](https://www.mongodb.com/developer/products/mongodb/schema-design-anti-pattern-summary/) in MongoDB define what to avoid.

- [Massive arrays](https://developer.mongodb.com/article/schema-design-anti-pattern-massive-arrays): storing massive, unbounded arrays in your documents.

Example:
```json
{
   "_id": "book002",
   "title": "Organic Chemistry",
   "reviews": [
     {
        "user_id": "author290",
        "name": "Jane James"
        "comment": "This is a awesome book"
     }
     ...
   ]
}
```

- [Massive number of collections](https://developer.mongodb.com/article/schema-design-anti-pattern-massive-number-collections): storing a massive number of collections (especially if they are unused or unnecessary) in your database.

- [Unnecessary indexes](https://developer.mongodb.com/article/schema-design-anti-pattern-unnecessary-indexes): storing an index that is unnecessary because it is (1) rarely used if at all or (2) redundant because another compound index covers it.

- [Bloated documents](https://developer.mongodb.com/article/schema-design-anti-pattern-bloated-documents): storing large amounts of data together in a document when that data is not frequently accessed together.

- [Separating data that is accessed together](https://developer.mongodb.com/article/schema-design-anti-pattern-separating-data): separating data between different documents and collections that is frequently accessed together.

- [Case-insensitive queries without case-insensitive indexes](https://developer.mongodb.com/article/schema-design-anti-pattern-case-insensitive-query-index): frequently executing a case-insensitive query without having a case-insensitive index to cover it.
