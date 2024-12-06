[Home](../README.md) > [Patterns](#)

# Index
- [Patterns](#patterns)
    - [On the database side](#on-the-database-side)
        - [Attribute](#attribute)
    - [On the application side](#on-the-application-side)
        - [Aproximation](#aproximation)
# Patterns
In 2019, MongoDB released an article talking about some patterns that you should use to model you database. This article was called [Building with Patterns: A Summary](https://www.mongodb.com/blog/post/building-with-patterns-a-summary).

In this article they have shown the power of patterns, how each of them could be used, the advantages and disadvantages as well as some use cases.

![summary of some mongodb design patterns](https://webassets.mongodb.com/_com_assets/cms/patternsmatrix-xv1kqjlrpb.png)

## On the database side

## Attribute
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

## Bucket
TO DO

| Pros                        | Cons                              |
|-----------------------------|-----------------------------------|
| | |
| | |
| | |

## Computed
TO DO

## Document Versioning
TO DO

## Extended reference
TO DO

## On the application side
### Aproximation
It's useful when **expensive calculations** are frequently done and when **the precision** of those calculations **is not the highest priority**.

Example: Website views. Generally speaking, it isn't vital to know if 700,000 people visited the site, or 699,983. Therefore we could build into our application a counter and update it in the database when our thresholds are met.

| Pros                        | Cons                              |
|-----------------------------|-----------------------------------|
| Fewer writes to the database | Exact numbers aren’t being represented |
| Maintain statistically valid numbers | Implementation must be done in the application |


## Outlier
TO DO

## Preallocated
TO DO

## Polymorphic
TO DO

## Schema Versioning
TO DO

## Subset
TO DO

## Tree and Graph
TO DO

[Home](../README.md#anti-patterns) > [Patterns](#)
