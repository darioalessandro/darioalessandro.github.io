---
layout: post
title:  "Database Management 95-703 - Single Table Queries"
date:   2017-04-11 10:00:00 -0500
categories:  programming oracle general motors tep technical education program sql database db casteel Oracle 11g
---

## Outine

- **SELECT** statement

- Special operators:

    - Concatenation
    - ALL/ANY
    - IN
    - BETWEEN
    - LIKE
    - IS NULL

- Sorting data

## SELECT statement

```sql
SELECT  [DISTINCT] *,
        column [alias], ...
FROM table [alias], ...
[WHERE row_condition]
[GROUP BY group_by_expression]
[HAVING group_condition]
[ORDER BY columns | expressions];
```

## Retrieve all info from a table

```sql
SQL> SELECT * FROM Publisher;
```

## WHERE clause

## Comparison operators

| Operator     | Description  |
| :-------------|:-------------|
| =       | Equal to|
| >       | Greater than|
| <       | Less than|
| <>.!= or ^=       | Not equal to|
| <=       | Less than or equal to|
| >=       | Greater than or equal to|
| ANY/ALL      | Compares one value with values in a list|

```sql
SQL> SELECT title, retail FROM books WHERE title > 'HO';
```

## Computed columns

Determined/calculated from values in existing columns by using arithmetic ops

```sql
SQL> SELECT title, (retail - cost) Profit FROM books WHERE (retail - cost) < 0.2 * cost;
```

## Concatenation

```sql
SQL> SELECT customer# || ': ' || firstname || ' ' || lastname AS "Customer", state FROM customers
WHERE state = 'CA' OR state = 'FL';
```
## ALL operator

```sql
SQL> SELECT customer# || ': ' || firstname || ' ' || lastname AS "Customer", state FROM customers
WHERE state <> ALL ('CA', 'FL', 'GA', 'IL', 'NJ');
```

**ALL** is used together with <,>,<=,>=,=,!=

```sql
e.g.,

20 < ALL(2,11,25) => FALSE
20 > ALL(2,11,25) => FALSE
20 > ALL(2,11,25) => FALSE
20 > ALL(2,11,25) => TRUE
```

## ANY operator

List customer number and his/her name for customers who have the id number less than any of the provided numbers.

```sql
SQL> SELECT customer# || ': ' || firstname || ' ' || lastname AS "Customer", state FROM customers
WHERE customer# < ANY(1001,1002,1003);
```

**ANY** is used together with <,>,<=,>=,=,!=

```sql
e.g.,

20 < ANY(2,11,25) => TRUE
20 > ANY(2,11,25) => TRUE
20 > ANY(2,11,25) => FALSE
20 > ANY(2,11,25) => TRUE
```

## OTHER OPERATORS

**[NOT] IN (z,y,...)**

Analogous to OR logical operator combining simple equality conditions.

**[NOT] BETWEEN x AND y**

Comparison with a range of values.
Can be used with numbers, textual values and dates.

**[NOT] LIKE**
Search for partial matches

**IS [NOT] NULL
Comparison with NULL value

## IN operator

- The in operator provides concise way to compare one value with values in a set.

- Can be used with any data type.

- List of values must be included in parentheses and separated by commas.

```sql
SELECT title, pubid FROM books WHERE pubid IN (1,2,5);
```

## BETWEEN x AND y operator

```sql
SELECT title FROM books WHERE title BETWEEN 'B' AND 'D'
```

## LIKE operator

**LIKE** is used when partial matches are satisfactory.

used with wildcard characters:

% - marches any string of zero or more characters

_ (underscore) - marches any individual character.

The ESCAPE option is used for the actual % or _ symbol.

# Example:

Find any customer whose last name starts with letter 'P'

```sql
SELECT lastname FROM customers WHERE lastname LIKE 'P%';
```

# Example 2:

You have difficulty reading a report because someone spilled coffe on it. You can only tell the first two digits ('10')
of the customer# and the last digit ('9'). The third digit is hard to read. Can you find the customer?

```sql
SQL> SELECT customer# || ': ' || firstname || ' ' || lastname AS "Customer", state FROM customers
WHERE customer# LIKE '10_9';
```


```sql
SQL> SELECT customer# || ': ' || firstname || ' ' || lastname AS "Customer", state FROM customers
WHERE lastname LIKE '%MI%'
   OR firstname LIKE '%NI%';
```

## ESCAPE option

What is the name of a publisher that has underscore character(s) in its name?

```sql
SQL> SELECT name
     FROM Publisher
     WHERE name LIKE '%\_%' ESCAPE '\';
```

What is the name of a publisher that has underscore character(s) in its name or an apostrophe in the contacts' name?

```sql
SQL> SELECT name
     FROM Publisher
     WHERE contact LIKE '%''%'
     OR name LIKE '%^_%' ESCAPE '^';
```

## Searching for null

```sql
SQL> SELECT order#, customer#, shipdate
FROM orders
WHERE shipdate IS NULL;
```

## Logical operators

- Used to combine simple conditions.

Evaluated in the following order:

NOT, AND, OR

## The order of operations

1. Parenthesis
2. Arithmetic operators
3. Concatenation
4. Comparision conditions
5. IS[NOT] NULL, [NOT] LIKE, [NOT] IN
6. [NOT] BETWEEN
7. NOT
8. AND
9. OR

## Sorting data

The **ORDER BY** clause can be used to list data in any desired order.

- The column on which data is to be sorted is called a **sort key**.

-  To sort in descending order use the **DESC** operator default is **ASC**.

```sql
SELECT lastname, firstname, city, state
FROM customers
WHERE state = 'FL' OR state = 'CA'
ORDER BY state DESC, city;

```

Order by state descending AND then, by city ascending.

We can also use the position of columns in the **SELECT** clause

```sql
SELECT lastname, firstname, city, state
FROM customers
WHERE state = 'FL' OR state = 'CA'
ORDER BY 4 DESC, 3;
```

## ORDER BY with expressions

List book title and expected profit. Sort the results in descending order of the profit.

```sql
SELECT title, (retail - cost) profit
FROM books
ORDER BY (retail - cost) DESC;
```

## NULLS FIRST OR NULLS LAST

```sql
SELECT firstname, lastname, state, referred
FROM customers
WHERE state = 'CA'
ORDER BY referred NULLS FIRST;
```

## Pseudocolumns

**Rownum** is an integer number identifying each row of a table as it is retrieved.

```sql
SELECT ROWNUM, gift, minretail, maxretail
FROM Promotion;
```

## Distinct Values

List book categories currently represented in the books table

```sql
SELECT DISTINCT category FROM books;
```

## Calculating expressions

```sql
SELECT SYSDATE + 30 "Next Month" FROM dual;
```

dual is used for DUMMY computations.
