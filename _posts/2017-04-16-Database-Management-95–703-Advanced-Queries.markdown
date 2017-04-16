---
layout: post
title:  "Database Management 95-703 - Advanced queries"
date:   2017-04-15 10:00:00 -0500
categories:  programming oracle general motors tep technical education program sql database db casteel Oracle 11g
---

## Outline

- Extension to **GROUP BY** clause:

    - GROUPING SETS,
    - ROLLUP
    - CUBE

- DECODE function and CASE expressions

- Pivoting a table

- Hierarchical Queries

## Extensions to **GROUP BY**

**GROUPING SETS ** - defines criteria for several **GROUP BY** aggregations.

## Combining "grouping" results

List publisher's name, book category, number of books published and average book price:

- For each category
- For each publisher and
- For each publisher within each category

Then add the total number of books and average book price for all books. Include all four result sets in one list.

```sql
1)      SELECT name, category, COUNT(isbn), AVG(retail)
        FROM publisher JOIN books USING (pubid)
        GROUP BY name, category
        UNION ALL
2)      SELECT name, NULL, COUNT(isbn), AVG(retail)
        FROM publisher JOIN books USING (pubid)
        GROUP BY name
        UNION ALL
3)      SELECT NULL, category, COUNT(isbn), AVG(retail)
        FROM books
        GROUP BY category
        UNION ALL
4)      SELECT NULL, NULL, COUNT(isbn), AVG(retail)
        FROM books
        GROUP BY ();
```

Same query in 4 lines:

```sql
SELECT  name, category, COUNT(isbn), AVG(retail)
FROM    publisher JOIN books USING (pubid)
GROUP BY GROUPING SETS
            ((name,category), name, category, ());
```

## ROLLUP extension

For each region and state provide number of orders and the total profit generated from sales. Provide the aggregated
values for each region, then for each state within the region. Also provide the total number of orders and overall profit.

```sql
SELECT region "Region", state "State", COUNT (DISTINCT order#) "# orders",
                TO_CHAR(SUM((paideach-cost) * quantity), '$9990.99')
                AS "Profit"
FROM books  JOIN orderitems USING (isbn)
            JOIN orders     USING (order#)
            JOIN customers  USING (customer#)
GROUP BY ROLLUP (region,state)
ORDER BY 1,2;

Region  State   # orders    Profit
--------------------------------------
N       IL      1           $49.10
N       MI      1           $55.28
N       WY      2           $32.00
N               4           $136.38
NE      NJ      3           $106.90
NE              3           $106.90
NW      ID      1           $14.90
NW      WA      2           $22.90
NW              3           $37.60
....
                21          $670.82
```

In this example, 3 levels of increasing aggregation are performed:

    1. Number of orders (and the total profit) for each combination of Region and State columns.

    2. Number of orders (and the total profit) only for each Region.

    3. "Grand total" of the number of orders and the profit generated from sales.

## CUBE extensions

For each order, publisher and category, list publisher's name, category, month of the order (month and year together),
number of books ordered, and the average price for for each of the groups identified by the order, publisher and category.

Provide aggregated values (i.e. number of books and the average price) for combination of all 3 "columns", for any two
"columns", any single "column" and grand totals.

```sql
SELECT  name, category
        TO_CHAR(orderdate, 'MM-YYYY') Month,
        COUNT(isbn) "#",
        TO_CHAR(AVG(retail), '$90.99') "AVG Retail"
FROM    publisher   JOIN    books       USING (pubid)
                    JOIN    orderitems  USING (isbn)
                    JOIN    orders      USING (order#)
GROUP BY CUBE (name, category, TO_CHAR(orderdate,'MM-YYYY'))
ORDER BY name, category, Month;
```

## Grouping date - equivalent statements

Equivalence of ROLLUP/CUBE and GROUPING SETS

```sql
ROLLUP/CUBE option      Equivalent GROUPING SET option

GROUP BY                GROUP BY
    ROLLUP(a,b,c)       GROUPING SETS
                        ((a,b,c), (a,b), a, ())

GROUP BY                GROUP BY
    CUBE(a,b,c)         GROUPING SETS
                        ((a,b,c), (a,b), (a,c), (b,c), a, b, c, ())

```

Equivalence of GROUPING SETS and GROUP BY statements

```sql
GROUPING SETS option                Equivalent GROUP BY clause

GROUP BY GROUPING SETS (a,b,c)      GROUP BY a ... UNION ALL
                                    GROUP BY b ... UNION ALL
                                    GROUP BY c

GROUP BY GROUPING SETS (a,b,())     GROUP BY a ... UNION ALL
                                    GROUP BY b ... UNION ALL
                                    GROUP BY ()

GROUP BY GROUPING SETS(a,b,(b,c((   GROUP BY a ... UNION ALL
                                    GROUP BY b ... UNION ALL
                                    GROUP BY b, c

GROUP BY GROUPING SETS((a,b,c))     GROUP BY a,b,c

GROUP BY GROUPING SETS(a,           GROUP BY a      ... UNION ALL
                     ROLLUP(b,c))   GROUP BY b, c   ... UNION ALL
                                    GROUP BY b      ... UNION ALL
                                    GROUP BY ()
```

## DECODE function

DECODE(c,v1,r1,v2,r2,...,[d])

c - input
v1 - first value
r1 - result to be returned if c and v1 are equivalent
d - default value

# Example:

JustLee Books is required to collect taxes from customers living in Florida or California but not from customers residing
in other states. Create a column with the tax rate for the two states.

```sql
SELECT  customer#, state, DECODE(state,  'CA', 0.09,
                                        'FL', 0.07,
                                                 0) "Sales Tax Rate"
FROM    customers;

CUSTOMER#   ST    Sales Tax Rate
------------------------------------
1015        FL              .07
1019        NJ                0
...
1016        CA              .09
```

## CASE expressions

```sql
SELECT  customer#, state,
        CASE    state
                WHEN 'CA' THEN 0.09
                WHEN 'FL' THEN 0.07
                ELSE 0.0
        END AS "Sales Tax Rate"
FROM    customers;
```

## Search CASE expressions

Assume that a book is cheap when the retail price is less than $30. Determine which book is check and which is expensive.

```sql
SELECT      title, retail,
            CASE
                WHEN retail < 30    THEN 'Cheap'
                ELSE 'Expensive'
            END "Is it cheap?"
FROM        books
ORDER BY retail;

TITLE                   RETAIL      Is it cheap?
-------------------------------------------------
```

## Pivot table with "Totals"

```sql
SELECT DECODE(category, NULL, 'Publisher Total', category) "Category",
       SUM(DECODE(pubID, 1, 1)) "#1",
       SUM(DECODE(pubID, 2, 1)) "#2",
       SUM(DECODE(pubID, 3, 1)) "#3",
       SUM(DECODE(pubID, 4, 1)) "#4",
       SUM(DECODE(pubID, 5, 1)) "#5",
       COUNT(isbn) "Category Total"
FROM   books
GROUP BY GROUPING SETS (category, ())
ORDER BY category;

Category        #1  #2  #3  #4  #5  Category Total
---------------------------------------------------
BUSINESS        1                               1
CHILDREN            1           1               2
....
SELF HELP                   1                   1
Publisher Total 2   2   3   4   3               14
```

# Replacing NULL Values

```sql
SELECT DECODE(category, NULL, 'Publisher Total', category) "Category",
       SUM(DECODE(pubID, 1, 1, 0)) "#1",
       SUM(DECODE(pubID, 2, 1, 0)) "#2",
       SUM(DECODE(pubID, 3, 1, 0)) "#3",
       SUM(DECODE(pubID, 4, 1, 0)) "#4",
       SUM(DECODE(pubID, 5, 1, 0)) "#5",
       COUNT(isbn) "Category Total"
FROM   books
GROUP BY GROUPING SETS (category, ())
ORDER BY category;

Category        #1  #2  #3  #4  #5  Category Total
---------------------------------------------------
```

**SELECTIVITY** number of unique values in a column

Is column has low selectivity, it is a candidate to be turned into columns

## Hierarchical Queries

Special queries called hierarchical queries have a mechanism to recognize and represent trees.

recursive "One-to-Many" relationship that forms a tree structure.

# Complete tree structure

Build organizational chart

```sql
SELECT      employee_ID, manager_ID, first_name, last_name, department_id
FROM        employee2
START WITH  employee_ID = 100
CONNECT BY PRIOR employee_ID = manager_ID;
```

# Pseudo column LEVEL

```sql
SELECT LEVEL
    LPAD('',3 * (LEVEL - 1)) || employee_ID || '    ' || first_name || ' '
    || last_name "Employee", department_id
    FROM employee2
    START WITH employee_ID = 100
    CONNECT BY PRIOR employee_ID = manager_ID;

LEVEL       Employee                                            DEPARTMENT_ID
-----------------------------------------------------------------------------
1       100 Steven King                                                 90
2           101 Neena Kochhar                                           90
3               108 Nancy Greenberg                                    100
4                   109 Daniel Fav                                     100
4                   110 John Chen                                      100
4                   111 Ismael ..                                      100
4                   112 Jose ..                                        100
4                   113 Luis .,                                        100
3               205 Shelly ..                                          110
```
# Showing more than one sub-tree

```sql
SELECT LEVEL
    LPAD('',3 * (LEVEL - 1)) || employee_ID || '    ' || first_name || ' '
    || last_name "Employee", department_id
    FROM employee2
    START WITH employee_ID = 101 OR employee_ID = 102
    CONNECT BY PRIOR employee_ID = manager_ID;
```

# Traversing the tree upward

```sql
SELECT LEVEL
    LPAD('',3 * (LEVEL - 1)) || employee_ID || '    ' || first_name || ' '
    || last_name "Employee", department_id
    FROM employee2
    START WITH employee_ID IN (206,107,113)
    CONNECT BY PRIOR manager_ID = employee_ID;
```

# Pruning sub-tree

```sql
SELECT      employee_ID, manager_ID, first_name, last_name, department_id
FROM        employee2
START WITH  employee_ID = 100
CONNECT BY PRIOR employee_ID = manager_ID;
AND (employee_ID != 103 AND employee_ID != 205);
```

# Showing specific nodes - IT Dept.

```sql
SELECT      employee_ID, manager_ID, first_name, last_name, department_id
FROM        employee2
WHERE department_ID = 60
START WITH  employee_ID = 100
CONNECT BY PRIOR employee_ID = manager_ID;
```