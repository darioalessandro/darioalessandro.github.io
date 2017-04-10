---
layout: post
title:  "Database Management 95-703 - An Introduction to SQL and SQL*Plus"
date:   2017-04-09 10:00:00 -0500
categories:  programming oracle general motors tep technical education program sql database db casteel Oracle 11g
---


# Outline

- Introducing SQL Statements

- Comparing SQL Statements and SQL*Plus

- Creating Table

- Identifying data types

- Adding and Deleting rows

- Deleting / Dropping a table

- Creating a simple **SELECT** Statement

- Using **NULL** values

# Intro

SQL: Designed by Chamberlin and Boyce on 1974 @ IBM.

Relational Algebra: Edgar F. Codd @ IBM.

SQL* Propietary Oracle dev environment.

In the 1980's renamed from SEQUEL to SQL.

# SQL statements

- Data Definition Language (DDL)

** CREATE, ALTER, DROP, RENAME **

- Data Manipulation Language (DML)

** INSERT, UPDATE, DELETE, TRUNCATE, MERGE **

- Transaction Control (TC)

** COMMIT, ROLLBACK, SAVEPOINT **

- Data Retrieval

** SELECT **

- Data Control Language (DCL)

** GRANT, REVOKE **

They can be palced on one or more lines.

Clauses are usually placed one per line.

The end of a statement is indicated by a semicolon.

# SQL*PLUS environment

- Execute SQL statements, commands.

- Format results of queries.

- Create files/scripts

# SQL*Plus commands

```sql

SET ECHO [ON | OFF]

SET FEEDBACK [ON | OFF]

SET PAGESIZE n

SET LINESIZE n

SET LONG n

SET TIMING [ON | OFF]

```

## File commands


```sql

RUN or /                    //Run whatever is in the buffer

SAVE filename               //Save buffer into file

GET filename                //load file into buffer

START filename or @filename //running a script

SPOOL filename              //start capture the session.

SPOOL OFF                   //finish spooling

EXIT

```

## Common data types

| Data type     | Description  |
| :-------------|:-------------|
| CHAR(n)       | fixed-length string of n chars long|
| VARCHAR2(n)   | variable-length string with max n chars     |
| NUMBER(p,s)   | variable length numeric data having precision p and s decimal places |
| DATE          | Includes: year, month, day, hour, minute and second, (default format is DD-MON-YYYY) |

## Creating tables

```sql
SQL > CREATE TABLE warehouse
    2 (WH_ID    NUMBER(2),
    3 LOCATION VARCHAR2(12));
```

## Displaying a table structure

```sql
SQL> DESCRIBE customers
```

## Inserting data

```sql
INSERT INTO warehouse
VALUES (10, 'BOSTON')
```

## Inserting partial information

```sql
INSERT INTO warehouse
(Customer#, Lastname, Firstname)
VALUES
(1050, 'O''Brian', 'Tina') //Escape ' using ''
```
## Deleting a row

```sql
DELETE FROM warehouse
WHERE wh_id = 30;
```

## Simple queries

```sql
SELECT  column1, column2, ...
FROM    table
WHERE   condition;
```

## Set Operations

**UNION, INTERSECT, MINUS ** require "union compatibility"

## Column aliases

List the alias after the column name.

```sql
SELECT contact AS "Pub1. Contact", phone FROM publisher;
```

### Using arithmetic ops

Lilst book title and expected profit for books with the expected profit smaller than $20

```sql
SELECT title, (retail - cost) profit
FROM books
WHERE (retail - cost) < 20;
```
You can not use alias in any clause.

## Concatenation

We can combine text data from one column with text data from another columns as well as with string
literals using the concatenation operator: **||**

List name (concatenated first and last name) of customers from Florida.

```sql
SELECT firstname || ' ' || lastname "Customer"
FROM customers
WHERE state = 'FL';
```

## Retrieving data from two tables

```sql
SELECT title, name
FROM books, publisher
WHERE books.pubID = publisher.pubID;
```