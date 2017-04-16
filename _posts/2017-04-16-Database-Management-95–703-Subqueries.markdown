---
layout: post
title:  "Database Management 95-703 - Subqueries"
date:   2017-04-16 5:00:00 -0500
categories:  programming oracle general motors tep technical education program sql database db casteel Oracle 11g
---

## Outline

- Defining and using subqueries:
    - Single row
    - Multiple row
    - Multiple column
    - Correlated

- Top-N Analysis
- Compensating for NULL values
- Generating ranges.

## Subqueries

- query nested inside another query

- Must be enclosed in parentheses

- Inner query is evaluated first

- Most common use is in **WHERE**

## Where can we use subqueries?

- Subqueries can be used in:
    - **SELECT**

    - **FROM**

    - **WHERE**

    - **HAVING**

- They can also be used within other statements:

    - **CREATE TABLE**
    - **INSERT**
    - **UPDATE**
    - **DELETE**

## Typical subquery

```sql
SELECT  select_list
FROM    table(s)
WHERE   expr operator
                        (SELECT select_list
                         FROM table(s)
                         WHERE exmpr);
```

# Subquery in a WHERE clause

Which books cost more than 'Database implementation'?

```sql
SELECT  title, TO_CHAR(cost, '$990.99') "Cost"
FROM    books
WHERE   cost >
            (SELECT vost
             FROM books
             WHERE title = 'DATABASE IMPLEMENTATION')
AND     category = 'COMPUTER';
```

Determine which book has a retail price that is less than the average retail price of all books.

```sql
SELECT title, TO_CHAR(retail, '$990.99') "Price"
FROM books
WHERE retail < (SELECT AVG(retail)
                FROM books);
```

**NOTE** always start with subquery.

