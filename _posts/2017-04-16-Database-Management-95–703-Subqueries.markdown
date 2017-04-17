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

## Single-row subquery in a select clause

We want to compare prices of each book against the average price of all books and need to find between each price and the
average.

```sql
SELECT title, TO_CHAR(retail, '$990.99') "Price",
TO_CHAR( retail - (SELECT AVG(retail) FROM books) ,'$999990.99') "Price - Avg"
FROM books;
```
# Single-row subquery in a **FROM** clause

Which book has retail price above the median retail price of all books?

```sql
SELECT  title, TO_CHAR(retail, '$990.99') "Price",
        TO_CHAR(X,'$990.99') "Median"
FROM    books, (SELECT MEDIAN(retail) X FROM books)
WHERE retail > X;

```

# Single-row subquery in a HAVING

List books categories that have a higher average expected profit than the average expected profit from books in
Literature category

```sql
SELECT category,
TO_CHAR(AVG(retail-cost), '$99990.99' "Avg Profit"
FROM books
GROUP BY category
HAVING AVG(retail-cost) > (SELECT AVG(retail-cost)
                           FROM books
                           WHERE category = 'LITERATURE');
```

## Multiple-row subqueries

- Most common in the IN operator

- Other ops are ANY, ALL and EXISTS.

List the title, retail price and category of books with the retail price that matches the maximum retail price of books
in each category.

```sql
SELECT  title
        TO_CHAR(retail, '$990.99') "Price", category
        FROM books
        WHERE retail IN (SELECT     MAX(retail)
                         FROM       books
                         GROUP BY   category)
ORDER BY category;
```


# ALL and ANY operators

Find the title of a book which retail price is greater than the retail price of all books in the Cooking category

```sql
SELECT  title, TO_CHAR(retail, '$990.99') Price
FROM    books
WHERE   retail > ALL (  SELECT  retail
                        FROM    books
                        WHERE   category = 'COOKING')
ORDER BY 2;
```

# Alternative to ALL operator, more efficient

```sql
SELECT  title, TO_CHAR(retail, '$990.99') Price
FROM    books
WHERE   retail >     (  SELECT  MAX(retail)
                        FROM    books
                        WHERE   category = 'COOKING')
ORDER BY 2;
```

## Multiple row subquery - ANY operator

FInd the title of book having a retail price lower than the retail price of any book in the cooking cat.

```sql
SELECT  title, TO_CHAR(retail, '$990.99') Price
FROM    books
WHERE   retail < ANY (SELECT    retail
                      FROM      books
                      WHERE     category = 'COOKING')
AND category <> 'COOKING';
```
## Multiple-row subquery in a HAVING

Which order has a total greate than the totals of all individual orders placed by customers from Florida?

```sql
SELECT order#,
TO_CHAR(SUM(paideach*quantity), '$990.99') "O. Total"
FROM orderitems
GROUP BY order#
HAVING  SUM(paideach*quantity) > ALL
        (SELECT SUM(paideach*quantity)
        FROM orders JOIN orderitems USING (order#)
                    JOIN customers  USING (customer#)
        WHERE state = 'FL'
        GROUP BY order#);
```

## Multiple-column queries

- Return more than one column in results and possibly more than one row.

- If used in a **WHERE** clause:

    - The **IN** operator is required.

    - Columns listed on the left side of the IN operator must be enclosed within parentheses and in the same order as the
    columns listed in the subquery.

# Example

List the most expensive book in each category

```sql
SELECT  title, TO_CHAR(retail, '$990.99') Price, category
FROM    books
WHERE (category, retail) IN (SELECT category, MAX(retail)
                             FROM   books
                             GROUP BY category)
ORDER BY category;
```

## Multiple-column subquery in a FROM clause

List all books that have higher than average price in their category.

```sql
SELECT      title, TO_CHAR(retail, '$990.99') Price,
            category, TO_CHAR(cataverage, '$990.99') AVG
FROM        books   JOIN    (SELECT  category, AVG(retail)   cataverage
                            FROM books
                            GROUP BY category)
                    USING (category)
WHERE   retail > cataverage;
```

## Correlated subqueries

# EXISTS operator

List titles of all ordered books

```sql
SELECT  title
FROM    books
WHERE   EXISTS (SELECT  isbn
                FROM    orderitems
                WHERE   books.isbn = orderitems.isbn);
```
# Example EXISTS

List order number and customer number for orders placed on March 31, 2009 that included a book with ISBN = 8843172113

```sql
SELECT  order#, customer#
FROM    orders o
WHERE   orderdate = '31-MAR-09'
AND     EXISTS (SELECT *
                FROM    orderitems OI
                WHERE OI.order# = O.order#
                AND isbn = '8843172113');

```

**NOTE** professor advises to not use correlated subqueries.

## TOP-N Analysis

- Performed utilizing a subquery (i.e., an inline view)

- **ORDER BY** clause within inline view is used to identify/arrange top values:

    - Descending order for highest values
    - Ascending order for lowest values

- Data is extracted based on **ROWNUM** pseudo column, that identifies the "location" of rows in the inline view.

List the title and profit of the 5 books with the highest expected profit

```sql
SELECT  ROWNUM as Rank, title, TO_CHAR(profit, '$990.99') Profit
FROM    (SELECT title, (retail - cost) profit
         FROM books
         ORDER BY profit DESC)
WHERE ROWNUM < 6;
```

## NULL values in subqueries **TODO**

## Nesting subqueries

- Max of 255, if nested in the WHERE clause.

- No limit if used in the FROM clause.

Find the name of the customer(s) who has ordered the most books from JustLee Nooks (not including multiple quantities of
the same book) on one order.

```sql
SELECT  customer# || ': ' || firstname || ' ' || lastname
        AS "Customer"
FROM    customers JOIN orders USING (customer#)
WHERE   order# IN ( SELECT order#
                    FROM    orderitems
                    GROUP BY order#
                    HAVING COUNT(*) = ( SELECT MAX(COUNT(*))
                                        FROM    orderitems
                                        GROUP BY order#));
```

## Generating Ranges of Values

# Using UNION

```sql
SELECT *
    FROM    (   SELECT 1 value FROM DUAL UNION ALL
                SELECT 2  FROM DUAL UNION ALL
                SELECT 3  FROM DUAL UNION ALL
                SELECT 4  FROM DUAL UNION ALL
                SELECT 5  FROM DUAL);
```

# Using GROUP BY and CUBE

This works similar way as a UNION since CUBE generates many rows:

```sql
SELECT  rownum
FROM    (SELECT 1 FROM dual GROUP BY CUBE(1,2,3))
WHERE   rownum <= 5;
```

# Using "big enough" table as a source

```sql
SELECT  rownum
FROM    books
WHERE   rownum <= 5;
```

# Using the CONNECT BY clause

We could use either rownum or level pseudo column

```sql
SELECT  rownum
FROM    dual
CONNECT BY rownum <= 5;
```

List 7 consecutive letters starting with A

```sql
SELECT CHR(ASCII('A' + level - 1) Letters
FROM    dual
CONNECT BY level <= 7;

LETTERS
----------
A
B
C
D
E
F
G
```

List cates of the 5 consecutive Sundays starting with 20-Mar-2016

```sql
SELECT date '2016-03-20' + (level -1 ) *7) Sundays
FROM dual
CONNECT BY level <= 5;

SUNDAYS
----------
20-MAR-16
27-MAR-16
03-APR-16
10-APR-16
17-APR-16
```

List names of months that are between 2 given dates (e.g., between March 23 and August 31.2016)

```sql
SELECT TO_CHAR(ADD_MONTHS(DATE, '2016-03-23', level -1), 'Month')
    AS Months
FROM dual
CONNECT BY level <= MONTHS_BETWEEN( TRUNC(DATE, '2016-08-31','MM'),
                                    TRUNC(DATE, '2016-03-23'.'MM')) + 1;

MONTHS
------------
March
April
May
June
July
August
```

For each day of the week list the number of orders. Include all 7 days.

```sql
SELECT      Day_of_week, COUNT(order#) "# of Orders"
FROM        orders RIGHT OUTER JOIN
            (SELECT TO_CHAR((SYSDATE + level), 'Day' Day_of_week
             FROM dual
             CONNECT BY level <= 7)
                    ON TO_CHAR(orderdate, 'Day') = Day_of_week
GROUP BY Day_of_Week
ORDER BY 2 DESC;


DAY_OF_WEEK         # of Orders
----------------------------------
Friday                  5
Saturday                4
...
Monday                  0

```