# MongoDB Design Patterns
MongoDB design patterns are strategies to structure your schema and data effectively based on your application's requirements.

Since MongoDB is a NoSQL database, it offers flexibility to design schemas tailored to your use case.

## Recap
Before we get started, it's important to remember some concepts:

### Golden rule

```
Data that are accessed together should be stored together.
```

### Data modeling

Data modeling refers to the organization of data within a database and the links between related entities. Data in MongoDB has a flexible schema model, which means:
- Documents within a single collection are not required to have the same set of fields.
- A field's data type can differ between documents within a collection.

To ensure that your data model has a logical structure and achieves optimal performance, plan your schema prior to using your database at a production scale. To determine your data model, use the following schema design process:

- [Identify your application's workload](https://www.mongodb.com/docs/manual/data-modeling/schema-design-process/identify-workload/#std-label-data-modeling-identify-workload).
- [Map relationships between objects in your collections](https://www.mongodb.com/docs/manual/data-modeling/schema-design-process/map-relationships/#std-label-data-modeling-map-relationships).
- [Apply design patterns](https://www.mongodb.com/docs/manual/data-modeling/schema-design-process/apply-patterns/#std-label-data-modeling-apply-patterns).

This image shows how the steps below compose the schema on MongoDB:

![structure of steps to compose a mongodb schema](./data.modeling.drawio.png)

#### Workload
Identify and describe the workload in the first step to model your database. You should:
- Identify and quantify the database entities and attributes:
    - A **entity** is a thing that exist in our database and it's unique and independent of each other.
    - A **attribute** is a property that describe an entity.
- Then, you should write the number of documents you expect for each entity (at least for the first year). Example:

| Entity       | Quantity  |
|--------------|-----------|
| Users        | 25,000    |
| Authors      | 50,000    |
| Books        | 200,000   | 
| Publishers   | 500       |
| Reviews      | 2,500,000 |

- Now, we should identify and quantity how the application will be **reading** and **writing** the entities.


| Action       | Query Type  | Information | Frequency | Priority  |
|--------------|-------------|-------------|-----------|-----------|
| The action that a user takes to trigger the query. | Read or write | The document fields that are either written or returned by the query. | How frequently your application runs the query. Queries that are run frequently benefit from indexes and should be optimized to avoid lookup operations. | How critical the query is to your application.

Example:
| Action                  | Query Type       | Information                   | Frequency          | Priority  |
|-------------------------|------------------|-------------------------------|--------------------|-----------|
| Fetch book details      | Read             | Book details + rating         | 1,000 / sec        | High      |
| Fetch reviews from book | Read             | Reviews + Reviewer rating     | 200 / sec          | High      |
| Fetch reviews from book | Read             | Reviews + Reviewer rating     | 200 / sec          | High      |
| Fetch user details      | Read             | User profile and preferences  | 5 per minute       | Medium    |
| Add/Update Book         | Write            | Book details                  | 20 per hour        | Medium    |
| Add/Update User         | Write            | Name, Email, Password         | 100 / day          | Low       |
| Submit Review           | Write            | Rating, Comments, Book ID     | 50 / sec           | High      |

It will help you:
- Identify which data is accessed together (and stored together).
- Determine which data we need for the application.
- Identify if our application performns more reads or writes and which are the hottest entities.
- In summary, it helps you to define relationships and models the collections.

#### Relationship

In MongoDB, you can use [references](https://www.mongodb.com/docs/manual/data-modeling/concepts/embedding-vs-references/#std-label-data-modeling-referencing) or [embedded documents](https://www.mongodb.com/docs/manual/data-modeling/concepts/embedding-vs-references/#std-label-data-modeling-embedding) to express relationship.

Using references:

![document that shows relationship using reference](https://www.mongodb.com/docs/manual/images/data-model-normalized.bakedsvg.svg)


Using embed:

![document that shows relationship using embed](https://www.mongodb.com/docs/manual/images/data-model-denormalized.bakedsvg.svg)

Types of relationship:
- Modeling one-to-one (1-1)
- Modeling one-to-many (1-N)
- Modeling many-to-many (N-N)

| Relationship | Relational database                                                                   | MongoDB                            |
|--------------|---------------------------------------------------------------------------------------|------------------------------------|
| 1:1 | Two tables: Each record in Table A is related to one record in Table B, and vice-versa | Embed or reference                |
| 1:N | Two tables: Each record in Table A can be related to multiple records in Table B, but each record in Table B is related to only one record in Table A. | Embed or reference |
| N:N | Three tables: Each record in Table A can be related to multiple records in Table B, and each record in Table B can also be related to multiple records in Table A. Create an association table (junction table) to represent the relationship. | Embed or reference |


## Patterns
[Click here](./PATTERNS.md)

## Anti patterns
[Click here](./ANTI_PATTERNS.md)

## References
- [Data modeling overview](https://www.mongodb.com/docs/manual/data-modeling/)
- [Building with patterns a summary](https://www.mongodb.com/blog/post/building-with-patterns-a-summary)

