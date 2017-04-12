---
layout: post
title:  "Database Management 95-703 - Multiple Table Queries"
date:   2017-04-11 11:00:00 -0500
categories:  programming oracle general motors tep technical education program sql database db casteel Oracle 11g
---

## Outine

- Retrieving data from more than one table:

    - **WHERE** clause
    - **NATURAL JOIN**
    - **JOIN ... USING**
    - **JOIN ... ON**
    - **OUTER JOIN**
    - Self Join

- Set operations
    - **UNION**
    - **INTERSECT**
    - **MINUS**

## Types of Joins

- **Cartesian Product** Also called Cartesian Join or Cross Join.

- **Equality Joins ** Join condition uses the equality comparison; also called Inner Joins.

- **Non-Equality Joins ** join condition does not use the equality comparison.

- **Outer Joins** Left, Right, Full

- **Self-join** table joined with itself

## Cross Join keyword

We need to perform a manual book inventory in all three warehouses. Therefore, we need an inventory sheet
listing each book with each warehouse along with a column to record the count fo books.

```sql
Select isbm, title, location, 'N= ' "Count"
FROM books CROSS JOIN warehouses
ORDER BY location, title;
```

## Equality join

Rows are joined using equality comparisons that involve "common" columns from two tables (also called inner joins)

Created by using:

- Equality condition in the **WHERE** clause.

- **JOIN** keyword in the **FROM** clause:

```sql
NATURAL JOIN
JOIN ... USING
JOIN ... ON
```

# Example

```sql
SELECT title, pubid, name FROM books NATURAL JOIN publisher;
```

# Example 2

```sql
SELECT title, pubid, name FROM books JOIN publisher USING (pubid);
```

## Equality joins: JOIN...ON

required when column names used for a join a different


```sql
SELECT title, pubid, name FROM books JOIN publisher ON (id = pubid);
```

## Joining several tables

List customer names along with the titles of books purchased by the customers

```sql
SELECT lastname, firstname, title
FROM customers, orders, orderitems, books
WHERE customers.customer# = orders.customer#
AND orders.order# = orderitems.order#
AND orderitems.isbn = books.isbn
ORDER BY lastname, firstname;
```

## Joining several tables - JOIN...

List customer names along with the titles of books purchased by the customers.

```sql
SELECT  lastname, firstname, title
FROM    customers   JOIN    orders      USING   (customer#)
                    JOIN    orderitems  USING   (order#)
                    JOIN    books       USING   (ISBN)
ORDER BY lastname, firstname;
```

## Non-equality joins

- In **WHERE** clause, using any comparison other than the equality.

- In **FROM** clause, using **JOIN ... ON** with a non-equality condition.

# Example:

Determine the gift the customers could receive for any of the books currently represented in the inventory


PROMOTION:
| GIFT     | MINRETAIL  | MAXRETAIL |
| :-------------|:-------------|:-------------|
| BOOKMARKER       | 0| 12|
| BOOK LABELS       | 12.01| 25|
| BOOK COVER       | 25.01| 56|
| FREE SHIPPING       | 56.01| 999.99|

BOOKS:
| ISBN          | TITLE         | PUBDATE       | PUBID         | COST          | RETAIL        | CATEGORY      |
| :-------------|:--------------|:--------------|:--------------|:--------------|:--------------|:--------------|


```sql
SELECT  title, gift
FROM    books, promotion
WHERE   retail BETWEEN minretail AND maxretail;
```

|TITLE           | GIFT          |
|:---------------|:--------------|
|SOME TITLE      |BOOKMARKER

## Self-Join

Since all column name are ambiguous, self joins require aliases

# Example:

List customer ID and customer name of those customers who were referred by another customer to JustLee Books. Also include
the ID and last name of the referring customer.

```sql
SELECT  c.customer# "#", c.lastname, c.firstname,
        r.customer$ "#", r.lastname, r.lastname "Referred By"
  FROM  customers c JOIN customers r
    ON  c.referred = r.customer#;
```

For each pair of customers located in the same city, list the customer number, customer last name and the city

```sql
SELECT  F.customer# || ' - ' || F.lastname "Customer 1",
        S.customer# || ' - ' || S.lastname "Customer 2",
        S.city
FROM    customers F JOIN customers S
  ON    F.customer# < S.customer#
WHERE   F.city = S.city
  AND   F.state = S.state;
```

## Outer Join

Outer joins include all rows that are correctly matched plus the rows that do not have a match in the other table.

Left outer join - all unmatched rows from the table on the left are to be also included.

Right outer join - all unmatched rows from the table on the right are to be also included.

Full outer join - all unmatched rows from both tables are to be also included.

# Example:

List customer number & name of each customer. For those who placed orders before, show the corresponding order number.

```sql

SELECT  customer#, lastname, firstname, order#
FROM    customers   LEFT OUTER JOIN orders USING (customer#)
ORDER BY customer#;

```

Using ON

```sql
SELECT  C.customer#, lastname, firstname, order#
FROM    customers C  LEFT OUTER JOIN orders O
        ON C.customer# = O.customer#
ORDER BY customer#;
```

## Set Operations

Set two operations are used to combine the results of two **SELECT** statements:

**UNION** - returns the combined results of two queries and eliminate duplicate rows.

**UNION ALL** - returns the combined results of two queries but does not eliminate duplicate rows.

** INTERSECT ** - returns only those rows that are in the results of both queries and eliminates duplicate rows.

** MINUS ** returns rows selected by the first query that are not in the result of the second query and eliminates diplicate
rows.

- Two tables are union compatible if:

    - They have the same number of columns.
    - The corresponding columns have the same data types.

- All operations have equal precedence. Oracle evaluates them from left to right, top to bottom.

- By default (without the ** ORDER BY** clause), the result is sorted in ascending order of the first column (except the
**UNION ALL**)

- The **ORDER BY** clause:
    - Can appear only one, at the end of the whole statement.
    - Accepts column names or aliases from the first **SELECT** statement, or the positional notation.

- Column names from the first query are used as headers of the result set.

## UNION example

List author ID and name of the authors of books that are in either family life or children categories.

```sql
SELECT  authorid, fname, lname
FROM    books   JOIN bookauthor USING   (isbn)
                JOIN author     USING   (authorID)
WHERE category = 'FAMILY LIFE'
UNION
SELECT  authorid, fname, lname
FROM    books   JOIN bookauthor USING   (isbn)
                JOIN author     USING   (authorID)
WHERE category = 'CHILDREN'
```

## INTERSECT example

List customers (ID and name) who live in Florida who have placed orders that were shipped to Florida.

```sql
SELECT  customer# || ' - ' || lastname || ', ' || firstname
        AS "Customer"
FROM    customers
WHERE   state = 'FL'
INTERSECT
SELECT  customer# || ' - ' || lastname || ', ' || firstname
        AS "Customer"
FROM customers JOIN orders USING (customer#)
WHERE shipstate = 'FL';

```

## MINUS example

List customer number and name of those customers who did not place any order.

```sql
SELECT  customer# || ' - ' || lastname || ', ' || firstname
        AS "Customer"
FROM customers
MINUS
SELECT  customer# || ' - ' || lastname || ', ' || firstname
        AS "Customer"
FROM orders JOIN customers USING (customer#);
```

## Comparing two tables

In testing environments we may need to compare a set of data generated by a new SQL programmer with a set of known data:

```sql
(SELECT * FROM known_data
MINUS
SELECT * FROM test_data)
UNION ALL

(SELECT * FROM test_data
MINUS
SELECT * FROM known_data)
