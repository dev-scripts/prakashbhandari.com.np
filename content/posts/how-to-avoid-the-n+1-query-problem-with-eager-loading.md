---
title: "How to Avoid the N+1 Query Problem With  Eager Loading"
date: 2024-09-11T20:07:25+10:00
showToc: true
TocOpen: true
tags : [
  "SQL",
  "Database",
  "Eager Loading",
  "Lazy Loading",
  "Performance Issue",
]
categories : [
  "SQL"
]
keywords: [
  "How to Avoid the N+1 Query Problem With  Eager Loading",
  "N+1 Query Problem",
  "N+1 Query Issue",
  "Eager Loading",
  "Performance Issue",
  "Lazy Loading", 
  "How to Avoid the N+1 Query Problem",
  "N+1 Query Problem Explained",
  "Eager Loading vs Lazy Loading",
  "Eager Loading in SQL",
  "Fixing N+1 Query Issue",
  "Performance Optimization SQL",
  "Reducing Database Queries with Eager Loading",
  "N+1 Query Issue in APIs",
  "Database Performance Issues",
  "Object-Relational Mapping Performance",
  "Solving N+1 Query Problem",
  "Example of Eager Loading",
  "N+1 Query Example in Laravel Eloquent",
  "N+1 Query Example in Entity framework",
  "Eager Loading Example with Entity Core",
  "Eager Loading Example with Entity framework",
  "Eager Loading Example with Laravel Eloquent",
]
cover:
  image: "images/posts/how-to-avoid-the-n+1-query-problem-with-eager-loading/how-to-avoid-the-n+1-query-problem-with-eager-loading.png"
  alt: "How to Avoid the N+1 Query Problem With  Eager Loading"
  relative: false,
  caption: "How to Avoid the N+1 Query Problem With  Eager Loading"
author: "Prakash Bhandari"
description: "N+1 queries is a performance problem where the application makes multiple database queries in a loop, instead of retrieving the related data with a single query. Where N is the number of queries executed inside the loop"
---

In this post, I will define the N+1 query problem and explain how to avoid it using eager loading.

Throughout my professional career, I have enhanced the response time of numerous APIs by refactoring N+1 queries.

I have seen the N+1 issue in many times that use ORMs, as well as in those that use raw queries. 
In this blog post, I will use raw queries to demonstrate the N+1 problem and Eager Loading to solve it.
Also, show examples of how Laravel Eloquent and C# Entity Framework (EF) ORM (Object-Relational Mapping) can result in N+1 issues

# N+1 query?
The **N+1** query problem occurs when an application makes a database call to fetch rows from the database, followed by an additional query for each row to fetch related data.
Here, the first query is **N** and the additional queries are **N+1**.

By default, many ORMs load main data and related data lazily, which can lead to the N+1 query problem.
## Lazy Loading
This approach delays fetching related data until the main query's properties are accessed. 
The related data are fetched only when needed (in demand). 

However, this approach can sometimes lead to an N+1 issue.
In cases where you need to load all related data for each main query,
an additional query is executed in a loop to fetch the related data, which causes N+1 select query problem.


In below examples I will show you how lazy load leads to the n+1 problem

### Scenario
Here is the scenario to demonstrate the N+1 query problem.

For example, imagine you are writing API for an online bookstore where you want to fetch a list of authors along with their books. 
Each author has one or more books (a one-to-many relationship).

Let's say, in a relational database, you have two tables: `authors` and `books`, 
storing the authors and their respective books.


The N+1 problem occurs when you execute a query to fetch a list of authors 
from the `authors` table and then, for each author, run an additional query to fetch that author's books from the `books` table. 
Where N is the number of authors.

### N+1 Query Example with raw query

```sql
SELECT * FROM `authors`;
```

For each author (N authors), another query is executed to fetch their books from `books` table.
```sql
SELECT * FROM `books` WHERE `author_id` = 1;
SELECT * FROM `books` WHERE `author_id` = 2;
......
SELECT * FROM `books` WHERE `author_id` = n;
```

Let's put above queries into actual code to demonstrates the N+1 query problem. 

```javascript
async function fetchAuthorsWithBooks() {
  // Query to get all authors
  db.query('SELECT * FROM authors', (error, authors) => {
    if (error) throw error;
    // For each author, fetch their books (N+1 queries)
    authors.forEach(author => {
      db.query('SELECT * FROM books WHERE author_id = ?', [author.id], (err, books) => {
        if (err) throw err;
        author.books = books;
        console.log(author);
      });
    });
  });
}

// function call
fetchAuthorsWithBooks();
```

In the example above, we see that a single query is executed to fetch a list of all authors from the database.

After retrieving the authors, the function runs other queries  to fetch the books associated with those authors.
If you have 10 authors in the `authors` table, an additional 10 queries will be executed in a loop to fetch their books from the `books` table.

As the number of authors grows in the `authors` table, the number of inner queries will also grow exponentially, leading to significant performance issues.
Each additional query consumes more resources, adds latency to the response which impact the performance of overall application. 

### N+1 Query Example with Laravel Eloquent
Here is the example of lazy loading in Laravel Eloquent which will cause N+1 Query problem

```php
$authors = Authors::all();
foreach ($authors as $author) {
    $books = $author->books; // A separate query for each post to load books for each authors
}
```
In above example, a new query will be executed for each author to load each authors books,
Which will leads to n+1  issues  and will have performance issue if we have a large number of records.

### N+1 Query Example with Entity Core
Here is the example of lazy loading in Entity Core which will cause N+1 Query problem
```dockerfile

// Load all authors
var authors = context.Authors.ToList();

// Load books for each author
foreach (var author in authors)
{
  var books = context.Books.Where(b => b.AuthorId == author.Id).ToList();
}
```

In above example, a new query will be executed for each author to load each authors books,
Which will leads to n+1  issues  and will have performance issue if we have a large number of records.

This N+1 query issue can be solved by Eager loading.

# Eager Loading

Eager Loading means preload all your main data and related relationship data with minimum SQL query from database before iterating.

## Pros
1. Reduces the number of database queries
2. Quicker access to data after initial load.
## Cons
1.  Could increases initial load time if there is large amount of data.
2.  Can be resource-intensive.

I will take above Scenario
## Eager Loading with raw query 
**Step 1** : The first query fetches all authors form `authors` table:

```sql
SELECT * FROM `authors`;
```
**Step 2** :  Extract the author ids fetched form above query.

extracted author ids : `[1,2,2]`

**Step 3** : Query to get all books form `books` table for the authors in a single batch.

**Step 4** : Write logic to group books by authorId and attach books to the corresponding author.

```sql 
SELECT * FROM `books` WHERE `author_id` IN (1, 2, 3);
``` 

With Eager Loading approach, we execute only two queries instead of executing **N** numbers of queries in the loop.
Here is sample code to solve N+1.
```javascript
async function fetchAuthorsWithBooksEagerLoading() {
    //1. The first query fetches all authors form `authors` table:
    db.query('SELECT * FROM authors', (error, authors) => {
        if (error) throw error;

        // 2. Second, extract the author ids fetched form above query.
        const authorIds = authors.map(author => author.id);

        // 3. Query to get all books form `books` table for the authors in a single batch.
        db.query('SELECT * FROM books WHERE author_id IN (?)', [authorIds], (err, books) => {
            if (err) throw err;
        // 4. Logic to group books by authorId and attach books to the corresponding author.
            // a. Group books by authorId
            const booksByAuthor = books.reduce((acc, book) => {
                if (!acc[book.author_id]) {
                    acc[book.author_id] = [];
                }
                acc[book.author_id].push(book);
                return acc;
            }, {});

            // b. Attach books to the corresponding author
            const authorsWithBooks = authors.map(author => ({
                ...author,
                books: booksByAuthor[author.id] || [] // If no books, return an empty array
            }));

            // c. Finally, print the authors with their books
            console.log(authorsWithBooks);
        });
    });
}

// function call
fetchAuthorsWithBooksEagerLoading();
```

In the example above, we see that a single query is executed to fetch a list of all authors from the database.
After retrieving the authors, the function extracts their IDs, which are then used to fetch the books associated with those authors.

Instead of running a separate query for each author (which would cause the N+1 problem), the second query retrieves all books for all authors in a single batch.

Finally, the books are grouped by authorId and attached to their corresponding authors.

## Eager Loading Example with Laravel Eloquent
Laravel allows eager loading using the `with` method. This will also generate the query like above raw query. 
```php
$authors = Author::with('books')->get();
```
In above example `with()` function is used to load books
for all the retrieved authors in a single query, rather than running one query to fetch the authors data and then N numbers of additional queries to fetch the books for each author.

## Eager Loading Example with Entity Core
 Entity Framework Core allows eager loading using the `Include` method. This will also generate the query like above raw query.
```csharp
var authors = context.Authors.Include(b => b.Books).ToList();
```
In above example `Include()` function is used to load books
for all the retrieved authors in a single query, rather than running one query to fetch the authors data and then N numbers of additional queries to fetch the books for each author.


With this approach, you will be executing two queries regardless of how many authors or books there are,
ensuring better performance and avoiding the N+1 query problem.


The N+1 query problem can significantly impact your application's performance by causing excessive database queries. 
Eager Loading helps to improve the performance by preloading all related data in fewer queries.