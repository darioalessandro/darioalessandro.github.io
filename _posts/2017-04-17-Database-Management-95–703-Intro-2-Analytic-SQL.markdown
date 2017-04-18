---
layout: post
title:  "Database Management 95-703 - Introduction to Analytic SQL Functions"
date:   2017-04-17 5:00:00 -0500
categories:  programming oracle general motors tep technical education program sql database db casteel Oracle 11g
---

## Outline

- Ranking functions:

    - ROW_NUMBER, RANK, DENSE_RANK, NTILE and PERCENT_RANK.

- Paritioning and Windowing clauses

- Window functions:

    - FIRST, LAST, LAG and LEAD

- Moving Average (MA) application.

## Analytic Functions

- Analytic function is performed on a table created by a query (or on each identified subset within the table)

- Allow creating queries that would otherwise require multiple intermediary steps.

- Present a simple solution to a complex query problem.

- Are used in SELECT clause

- Not allowed in WHERE and HAVING clauses.

- More than one analytic function can be used in a single SELECT clause.

- Available in Oracle:

    - Ranking functions
    - Windowing functions
    - Other

## Syntax

```sql
Analytic_Function([arguments]) OVER (<analytic_clause>)
<analytic_clause> :
    - Order
    - Partitioning
    - Windowing
    - combination
```

## Ranking Funcions

**ROW_NUMBER()** - provides a sequence of integer that defines the order of rows being retrieved based on a criteria (an
**ORDER BY** clause).

**RANK()** - similar to the above function but it gives the same rank when there's a tie and skips values after the
tie is considered.

**DENSE_RANK()** - ties receive the same rank and the rank number following a tie gets the next value.

**PERCENT_RANK()** - returns the percent rank of a value relative to a group of values.

**CUME_DIST()** - returns a position of a value relative to a group of values.

**NTILE()** - returns the group number that the given record belongs to when the whole set of values is divided into n
equal groups.


## Window functions

**FIRST_VALUE()** returns the first value in an ordered set (in a window).

**LAST_VALUE()** returns the last value in an ordered set (in a window).

**LAG() & LEAD()** return a value from a row that is a certain number of rows away from the current row.

## RANK

Rank sales of books (product type 1) by employees for Jan 2003

```sql
SELECT  emp_id, First_name, Last_name, amount,
        RANK() OVER (ORDER BY amount DESC) AS rank
FROM    all_sales JOIN employees2 ON emp_ID = employee_ID
WHERE   prd_type_id = 1
AND     month = 1 AND year = 2003;

EMP_ID      FIRST_NAME      LAST_NAME       AMOUNT      RANK
---------------------------------------------------------------
22          Roy             Red             11034.84        1
21          Steve           Green           10034.84        2
...
25          Gerald          Gold            1234.84         6

```

## ROW_NUMBER()

Rank sales of books (prod type 1) for Steve Green (ID = 21) for year 2003.

```sql
SELECT  ROW NUMBER() OVER (ORDER BY amount DESC) Row_No, amount, month
FROM    all_sales
WHERE   emp_id = 21 AND prd_type_id = 1 AND year = 2003;

ROW_NO          AMOUNT          MONTH
--------------------------------------
1               25057.45            4
2               21754.19            10
...
12              10034.84            1

```

## NULL  values

Rank sales of different product types for employee Steven Green(ID = 21) for Aug 2003

```sql
SELECT  Name Product, Amount,
        ROW_NUMBER() OVER (ORDER BY amount DESC NULLS LAST) rank
FROM    all_sales JOIN product_types
                    ON prd_type_id = product_type_ID
WHERE   emp_id = 21
AND     month = 8
AND     year = 2003;

PRODUCT         AMOUNT          RANK
------------------------------------
CD              21734.82           1
DVD             21734.82           2  <- Different rank for same amount?
Book            17434.82           3
Video            1734.82           4

```

## How to deal with ties?

Replace ROW_NUMBER -> RANK

```sql
SELECT  Name Product, Amount,
        RANK() OVER (ORDER BY amount DESC ) rank
FROM    all_sales JOIN product_types
                    ON prd_type_id = product_type_ID
WHERE   emp_id = 21
AND     month = 8
AND     year = 2003;

PRODUCT         AMOUNT          RANK
------------------------------------
CD              21734.82           1
DVD             21734.82           1  <- Skipping rank? (2)
Book            17434.82           3
Video            1734.82           4
```

But use DENSE_RANK() to avoid skipping values

```sql
SELECT  Name Product, Amount,
        DENSE_RANK() OVER (ORDER BY amount DESC ) rank
FROM    all_sales JOIN product_types
                    ON prd_type_id = product_type_ID
WHERE   emp_id = 21
AND     month = 8
AND     year = 2003;

PRODUCT         AMOUNT          RANK
------------------------------------
CD              21734.82           1
DVD             21734.82           1
Book            17434.82           2
Video            1734.82           3
```

## Top N analysis

Find the top 5 monthly sales in 2003 by Steve Green. List the month as well as the product type.

```sql
SELECT  *
FROM    (SELECT Name "Prod. Type" Month, Amount,
                RANK() OVER (ORDER BY amount DESC) rank
         FROM   all_sales JOIN product_types
                            ON prod_type_id = product_type_ID
         WHERE  emp_id = 21 AND year = 2003
           AND  amount IS NOT NULL)
WHERE   rank <= 5;

Prod. Type      MONTH       AMOUNT          RANK
--------------------------------------------------
DVD               10        32754.19          1
Book               4        25057.45          2
...
CD                 8        21734.82          5

```

## Other ranking functions

- PERCENT_RANK and CUME_DIST calculate the "relative standing" of the rank indicator relative to a group of values.

- Following formulas are used:

Percent_Rank = (Row's_Rank -1)/(Number_of_Rows - 1)

Cume_Dist = (Row's_Rank)/(Number_of_Rows)


# PERCENT_RANK()

What is the percent rank for sales of books (product type 1) for Steve Green for year 2003

```sql
SELECT      month, amount, RANK() OVER (ORDER BY amount Desc) AS Rank,
            TO_CHAR(100 * PERCENT_RANK()
               OVER (ORDER BY amount DESC), '9990.99') || '%' AS "Percent"
FROM        all_sales
WHERE       emp_id = 21
AND         prd_type_id = 1;


MONTH       AMOUNT      RANK        PERCENT
---------------------------------------------
4           25057.45        1           0.00%
10          21754.19        2           9.09%
3           20137.83        3           18.18%
9           19854.57        4           27.27%
...
12          10034.84        11          90.91%
1           10034.84        11          90.91%
```

## NTILE function

Before calculating any value, the function divides the number of retrieved rows into "equal" number of segments / groups

    -The NTILE  function identified the segment that a row belongs to

All rows are "evenly" distributed into the segments.

    - For example NTILE(4) will identify each row with value from 1 to 4 (as belonging to one of 4 segments) according
    to their rank as defined by the "order by" analytic clause.

```sql
SELECT  month, SUM(amount) AS "Amount",
        NTILE(4) OVER (ORDER BY SUM(amount) DESC) "4tile"
FROM    all_sales
GROUP BY month;

    MONTH   Amount          4tile
---------------------------------------
10                              1
8                               1
9                               1
4                               2
7                               2
3                               2
11                              3
5                               3
12                              3
6                               4
2                               4
1                               4

12 rows selected.
```

# Example:

The CIO wants to study the performance of employees based on their overall sales in 2003. He wants to know to which quarter
of sales amount each employee belongs to.

He also plans to give a bonus of $10,000 to each employee in the top quarter of sales and $5,000 to each employee in the
second quarter of sales.

```sql

SELECT first_name || ' ' || last_name "Employee",
        TO_CHAR(SUM(amount), '$999,999.99') "Total Sales",
        NTILE(4) OVER (ORDER BY SUM(amount) DESC)
            AS "Quarter",
        CASE    NTILE(4) OVER (ORDER BY SUM(amount) DESC)
                WHEN 1 THEN '$10,000.00'
                WHEN 2 THEN '$5,000.00'
                ELSE        '    $0.00'
        END "Bonus"
FROM    all_sales JOIN employees2 ON employee_id = emp_ID
WHERE   year = 2003 AND amount IS NOT NULL
GROUP BY employee_ID, first_name, last_name
ORDER BY 3;

Employee            Total Sales         Quarter Bonus
-------------------------------------------------------
Steve Green         $466,997.84         1        $10,000.00
Roy Red                                 1        $10,000.00
Gail Silver                             2        $5,000.00
Sandra Smith                            2        $5,000.00
Eileen Lane                             3        $0.00
Gerald Gold                             4        $0.00
```

## PARTITION BY

It is used when we need to divide result sets into subsets.

    - First, subsets/partitions of the data are defined.
    - Then, an analytic function is performed within each partition.

