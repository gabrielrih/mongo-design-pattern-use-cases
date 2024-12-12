[Home](../README.md) > [Patterns](#)

# Index
- [Patterns](#patterns)
    - [Application and document changes](#application-and-document-changes)
        - [Attribute](#attribute)
        - [Computed](#computed)
        - [Bucket](#bucket)
        - [Document Versioning](#document-versioning)
        - [Extended reference](#extended-reference)
        - [Schema Versioning](#schema-versioning)
        - [Subset](#subset)
        - [Outlier](#outlier)
    - [Just on application](#just-on-application)
        - [Aproximation](#aproximation)
        - [Preallocated](#preallocated)
        - [Polymorphic](#polymorphic)
        - [Tree and Graph](#tree-and-graph)
# Patterns
In 2019, MongoDB released an article talking about some patterns that you should use to model you database. This article was called [Building with Patterns: A Summary](https://www.mongodb.com/blog/post/building-with-patterns-a-summary).

In this article they have shown the power of patterns, how each of them could be used, the advantages and disadvantages as well as some use cases.

![summary of some mongodb design patterns](https://webassets.mongodb.com/_com_assets/cms/patternsmatrix-xv1kqjlrpb.png)

## Application and document changes
Some design patterns that require changes on the application side and also on the document structure.

### Attribute
It's useful for problems that are based around having **big documents with many similar fields but there is a subset of fields that share common characteristics** and we want to sort or query on that subset of fields. When the fields we need to sort on are only found in a small subset of documents. Or when both of those conditions are met within the documents.

| Pros                        | Cons                              |
|-----------------------------|-----------------------------------|
| Fewer indexes are needed | |
| Queries become simpler to write and are generally faster | |
| The document gets more organized | |
| The document become prepared for new unpredictable values on that fields | |

Bad example:
```json
{
    "title": "Star Wars",
    "director": "George Lucas",
    "release_US": ISODate("1977-05-20T01:00:00+01:00"),
    "release_France": ISODate("1977-10-19T01:00:00+01:00"),
    "release_Italy": ISODate("1977-10-20T01:00:00+01:00"),
    "release_UK": ISODate("1977-12-27T01:00:00+01:00"),
    ...
}
```

Using the Attribute pattern:
```json
{
    "title": "Star Wars",
    "director": "George Lucas",
    "releases": [
        {
            "location": "USA",
            "date": ISODate("1977-05-20T01:00:00+01:00")
        },
        {
            "location": "France",
            "date": ISODate("1977-10-19T01:00:00+01:00")
        },
        {
            "location": "Italy",
            "date": ISODate("1977-10-20T01:00:00+01:00")
        },
        {
            "location": "UK",
            "date": ISODate("1977-12-27T01:00:00+01:00")
        }
    ]
}
```

### Computed
It's usefull when there are very **read intensive data access** patterns and that data needs to be **repeatedly computed** by the application.

| Pros                        | Cons                              |
|-----------------------------|-----------------------------------|
| Reduction in CPU workload for frequent computations | It may be difficult to identify the need for this pattern |
| Queries become simpler to write and are generally faster | Applying or overusing the pattern should be avoided unless needed |

```json
{
  "_id": "order001",
  "user_id": "user123",
  "items": [
    { "product_id": "prod001", "name": "Laptop", "price": 1000, "quantity": 1 },
    { "product_id": "prod002", "name": "Mouse", "price": 50, "quantity": 2 }
  ],
  "total_price": 1100,
  "order_date": "2024-12-06T10:00:00Z"
}
```

### Bucket
It's a great solution for when needing to manage **streaming data**, such as time-series, real-time analytics or Internet of Things (IoT) applications. 

| Pros                        | Cons                              |
|-----------------------------|-----------------------------------|
| Reduces the overall number of documents in a collection. | |
| Improves index performance.|
| Can simplify data access by leveraging pre-aggregation. | |

Instead of...
```json
{
   "sensor_id": 12345,
   "timestamp": ISODate("2019-01-31T10:00:00.000Z"),
   "temperature": 40
},
{
   "sensor_id": 12345,
   "timestamp": ISODate("2019-01-31T10:01:00.000Z"),
   "temperature": 40
}
```

Consider to use this one...
```json

{
    "sensor_id": 12345,
    "start_date": ISODate("2019-01-31T10:00:00.000Z"),
    "end_date": ISODate("2019-01-31T10:59:59.000Z"),
    "measurements": [
       {
        "timestamp": ISODate("2019-01-31T10:00:00.000Z"),
        "temperature": 40
       },
       {
        "timestamp": ISODate("2019-01-31T10:01:00.000Z"),
        "temperature": 40
       }
    ],
   "transaction_count": 2,
   "sum_temperature": 80,
   "avg_temperature": 40
}
```

### Document Versioning
When you are faced with the need to **maintain previous versions of documents** in MongoDB, the Document Versioning pattern is a possible solution. 

This pattern is all about keeping the version history of documents available and usable.

Two collections: one for the latest document and another one to keep the previous ones.

| Pros                        | Cons                              |
|-----------------------------|-----------------------------------|
| Easy to implement, even on existing systems. | It may doubles the number of writes. |
| No performance impact on queries on the latest revision. | Queries need to target the correct collection. |
| | Extra disk size |
| | It could be a problem if you starts to have a lot of reviews |

Example: 

Collection: policy
```json
{
    "_id": ObjectId<ObjectId>,
    "name": "Bilbo Baggins",
    "revision": 2,
    "items_insured": ["Elven-sword", "One Ring"]
}
```

Collection: policy_revisions
```json
{
    "_id": ObjectId<ObjectId>,
    "name": "Bilbo Baggins",
    "revision": 1,
    "items_insured": ["Elven-sword"]
}

{
    "_id": ObjectId<ObjectId>,
    "name": "Bilbo Baggins",
    "revision": 2,
    "items_insured": ["Elven-sword", "One Ring"]
}
```

### Extended reference
It's useful when your application is experiencing **lots of JOIN operations** to bring together frequently accessed data. 

| Pros                        | Cons                              |
|-----------------------------|-----------------------------------|
| Improves performance when there are a lot of JOIN operations | Data duplication |
| Faster reads and a reduction in the overall number of JOINs |  Extra cost to update data |

Example:
Consider two collections: Customer and Order

```json
{
    "_id": ObjectID(""),
    "date": ISODate("2024-12-01"),
    "customer_id": 123,
    "products": [
        {
            "product": "pen",
            "quantity": 10,
            "cost": {
                "value": 5,
                "currency": "BRL"
            }
        }
    ]
}
```

```json
{
    "_id": 123,
    "name": "Maria João de Albuquerque",
    "street": "123 Main st",
    "city": "Somewhere",
    "country": "Some country"
}
```

... consider to use this one on Order collection
```json
{
    "_id": ObjectID(""),
    "date": ISODate("2024-12-01"),
    "customer": {
        "id": 123,
        "shipping_address": {
            "name": "Maria João de Albuquerque",
            "street": "123 Main st",
            "city": "Somewhere",
            "country": "Some country"
        }
    }
    "products": [
        {
            "product": "pen",
            "quantity": 10,
            "cost": {
                "value": 5,
                "currency": "BRL"
            }
        }
    ]
}
```

### Schema Versioning
It's usefull when changes on the data schema are frequently. This pattern allows to more than one document schema exist within the same collection.

| Pros                        | Cons                              |
|-----------------------------|-----------------------------------|
| No downtime needed for migration | Might need two indexes for the same field during migration |
| Control of schema migration | Extra code is needed |
| Reduced future technical debt | Complexity if you must keep a lot of versions |

Example:
```json
{
    "_id": ObjectId(""),
    "schema_version": "1",
    "name": "Anakin Skywalker (Retired)",
    "contact": "503-555-0210"
}

```json
{
    "_id": ObjectId(""),
    "schema_version": "2",
    "name": "Anakin Skywalker (Retired)",
    "contact_method": [
        { "work": "503-555-0210" },
        { "mobile": "503-555-0220" },
        { "twitter": "@anakinskywalker" },
        { "skype": "AlwaysWithYou" }
    ]
}
```

### Subset
It's an alternative to solve the problem of having the working set **exceed the capacity of RAM** due to **large documents**.

In this scenario, probably much of the data in the document is not being used by the application.

| Pros                        | Cons                              |
|-----------------------------|-----------------------------------|
| Reduction in the overall size of the working set | The application must manage the subset |
| Shorter disk access time for the most frequently used data | Pulling in additional data requires additional trips to the database |

Example: Imagine an e-commerce site that has a list of reviews for a product. It contains hundreds of reviews in the product document.

```json
{
    "_id": ObjectId(""),
    "name": "Super pen",
    "description": "This is the best pen ever",
    "reviews": [
        {
            "review_id": 1,
            "review_author": "Kristina",
            "review_text": "This is indeed the best pen ever",
            "published_date": ISODate("2024-12-01")
        },
        ... hundreds of reviews here...
    ]
}
```

You could separate it in two collections: product (containing just the most used reviews, for example the last 10, and another collection for all the reviews).

Collection: product
```json
{
    "_id": ObjectId(""),
    "name": "Super pen",
    "description": "This is the best pen ever",
    "reviews": [
        {
            "review_id": 1,
            "review_author": "Kristina",
            "review_text": "This is indeed the best pen ever",
            "published_date": ISODate("2024-12-01")
        },
        ... the last 10 reviews...
    ]
}
```

Collection: reviews
```json
{
    "review_id": 1,
    "review_author": "Kristina",
    "review_text": "This is indeed the best pen ever",
    "published_date": ISODate("2024-12-01")
}
... all the reviews here ...
```

### Outlier
Do you find that there are a **few queries or documents that don’t fit into the rest of your typical data patterns**? If so, the Outlier Pattern is a wonderful solution to this situation.


| Pros                        | Cons                              |
|-----------------------------|-----------------------------------|
| Prevents a few documents or queries from determining an application’s solution | Often tailored for specific queries, therefore ad hoc queries may not perform well |
| Queries are tailored for “typical” use cases, but outliers are still addressed | Much of this pattern is done with application code |

Example: You may have a book collection and the names of the customers that purchases this book as an array.

```json
{
    "_id": ObjectID("507f1f77bcf86cd799439011")
    "title": "A Genealogical Record of a Line of Alger",
    "author": "Ken W. Alger",
    "customers_purchased": ["user00", "user01", "user02"]
}
```

It may works as expected in the most scenarios, but, what about the "best sellers". This ones could contains millions of customers. In this case we could use a extra field called *has_extra* and move the overflow information to a new document likned with the books's id.

```json
{
    "_id": ObjectID("507f191e810c19729de860ea"),
    "title": "Harry Potter, the Next Chapter",
    "author": "J.K. Rowling",
    "customers_purchased": ["user00", "user01", "user02", …, "user999"],
    "has_extras": "true"
}

{
    "_id": ObjectID("607f191e810c19729de860ea"),
    "linked_book_id": ObjectID("507f191e810c19729de860ea"),
    "title": "Harry Potter, the Next Chapter",
    "author": "J.K. Rowling",
    "customers_purchased": ["user1000", "user1001", "user10012"],
    "has_extras": "true"
}
```

> There are other methods to solve this problem, you could create an extra collection to move the overflow information for example.


## Just on application
Patterns that require changes just on the application side.

### Aproximation
It's useful when **expensive calculations** are frequently done and when **the precision** of those calculations **is not the highest priority**.

Example: Website views. Generally speaking, it isn't vital to know if 700,000 people visited the site, or 699,983. Therefore we could build into our application a counter and update it in the database when our thresholds are met.

| Pros                        | Cons                              |
|-----------------------------|-----------------------------------|
| Fewer writes to the database | Exact numbers aren’t being represented |
| Maintain statistically valid numbers | Implementation must be done in the application |

### Preallocated
TO DO

### Polymorphic
TO DO

### Tree and Graph
TO DO

[Home](../README.md#anti-patterns) > [Patterns](#)
