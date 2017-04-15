---
layout: post
title:  "Database Management 95-703 - Single Row SQL Functions"
date:   2017-04-15 10:00:00 -0500
categories:  programming oracle general motors tep technical education program sql database db casteel Oracle 11g
---

## Outline

- Char functions

- Date functions

- Numerical

- Conversion

- Functions involving **NULL** value

## Types of SQL functions

- Function: block of code that accepts arguments and returns a single value.

    - They do not change values in the base tables unless they are used in **UPDATE** statements.

    - Can be nested.

- Single-row functions

    - Accept a single value as an input and return one result per row in a query.

    - May return a value of different data type.

- Multiple-row functions

    - Accept a group of values as an input and return one result per group.

## Types of single-row functions

Case conversion : UPPER, LOWER, INITCAP

Char, Manipulation: SUBSTR, INSTR, LENGTH, LPAD, TRIP, REPLACE,..

DATE: ADD_MONTHS, NEXT_DAY,...

Numerical: ROUND, TRUNC, ...

Type conversion: TO_DATE, TO_CHAR, TO_NUMBER

Other: NVL, NVL2 (null value functions)


## Case conversion functions

- If used in **SELECT** clause they alter the appereance of queries.

- If used in **WHERE** clause they alter the values used in comparisons.

# Example:

List the name (first and last) of any customer whose last name spells 'nelson'

```sql
SQL>    SELECT  INITCAP(firstname) "First Name",
                UPPER(lastname) "Last Name"
        FROM    customers
        WHERE   LOWER(lastname) = 'nelson';
```

How the **INITCAP** works?

```sql
SQL> SELECT INITCAP('sAN fRanCIScO') "Initcap F." From dual;

Initcap F.
-----------------
San Francisco
```

## Character manipulation

**LENGTH(c)** - returns the number of chars.

**SUBSTR(c,p,1) - extracts a substring.

c = input
p = position of the extraction (can be negative, right to left)
l = length of the output string

# LENGTH function

```sql
SQL> SELECT MAX(LENGTH(address)) "Max length" FROM customers;

Max Length
-------------
20
```

# SUBSTR function

Extract the first three characters from customers zip codes in order to identify geographical areas

```sql
SQL> SELECT DISTINCT SUBSTR(zip,1,3) "Area" FROM customers;

Area
---------------
331
079
...

18 rows selected.
```

# INSTR(str1,str2,[p,o])

returns the position of the first character of str2 located within the str1,

where

str1 = search string
str2 = string to be located in str1
p = start position of the search
o = occurrence of interest

Calculate the position of the first comma within the contact name of publisher (i.e., the name in Contacts table), position
of a coma after the 9th character, and position of the second comma.

```sql
SQL>    SELECT name, INSTR(name,',')    "1st Comma",
                     INSTR(name,',',10) "Start at 10",
                     INSTR(NAME, ',',1,2) "2nd Comma"
        FROM contacts;

```

# Nesting functions

Extract last name and first name from the "contact name" of publisher.

```sql
SQL> SELECT name,
            SUBSTR(name,1,INSTR(name,',') - 1) "Last Name",
            SUBSTR(name, INSTR(name,',') + 1,
            INSTR(name, ',',1,2) - INSTR(name,',') -1) "First Name"
    FROM contacts;

    NAME                                Last Name       First Name
    -----------------------------------------------------------------
    LaFodant,Mike,934-384-3493          LaFodant        Mike
```

# LPAD

LPAD(c,l,s) - returns a padded string, where

c = input string to be padded on the left
l = length of the output string after being padded
s = character to be used as padding

For every customer with a first name starting with 'J', provide the first name padded with spaces and then padded with '*'
to a total width of 12 chars.

```sql
SQL> SELECT     firstname,
                LPAD(firstname, 12,' ') "Pad with spaces",
                LPAD(firstname, 12, '*') "Pad with *"
     FROM       customers
     WHERE      UPPER(firstname) LIKE 'J%';
```

# Character manipulation (cont.)

RTRIM(c,[s]) removes a string from the right,
where:
    c = character string to be trimmed
    s = string to be removed from the right of c, if no specified it removed the spaces.

REPLACE(c,s,r) - looks for occurrences of specific string and, if found, replaces it with another, where:

c = character string to be searched.
s = string to be found
r = string to be substituted for s

# LTRIM and RTRIM functions

For all customers who use "P.O Box" as address, list the customer name, the address and separately only the box number:

```sql
SQL>    SELECT  RTRIM(firstname) ||' ' || RTRIM(lastname) "Name",
                address, LTRIM(address, 'P.O. BOX ') "Box #"
        FROM    customers
        WHERE   address LIKE 'P.O. BOX%';

Name                    ADDRESS             Box #
--------------------------------------------------------
BONITA MORALES          P.O. BOX 651        651

```

# REPLACE function

Replace occurence of "P.O" in customer's address with "Post Office"

```sql
SQL> SELECT     address,
                REPLACE(address, 'P.O.', "Post Office") "Corrected addr."
     FROM       customers;

ADDRESS                 Corrected Addr.
----------------------------------------------------
P.O. BOX 651            Post Office BOX 651
P.O. BOX 9835           Post Office BOX 9835
```

# Date functions

MONTHS_BETWEEN(date1,date2)
    - Calculates the number of months between 2 dates.
    - Non - integer portion of the result represents a portion of a month.

ADD_MONTHS(date,n)
    - Adds n (number of months) to the date.

NEXT_DAY(date,x)
    - Determines the next occurrence of a specified day of the week (x) following the given **date**

    ```sql
    SELECT NEXT_DAY('17-APR-2015', 'MONDAY') AS "Renegotiation" FROM dual;

    Renegotia
    -------------
    20-APR-15
    ```

LAST_DAY(date)
    - Finds the date of the last day of the month that includes the given date.

EXTRACT( X FROM date)
    - Extracts and returns a year, month, day etc. from given date.
    - Result is a numberic value.

# Arithmetic ops with dates

- Adding or subtracting a number to or from a date results in a date value.

- Adding hours to a date is possible but the number of hours need to be given in terms of days (i.e., the number/24).

- Subtracting two dates results in a number of days between dates. The result can be expressed as number of weeks, (i.e. result/7)
months (result/30) or years (result/365).

# Numerical functions

ROUND(n,p) - rounds a numerical value to the stated precision, where:

    n = the value to be rounded.
    p = position of the digit to which the value **n** should be rounded.

TRUNC(n,p) truncates a numerical value to the stated precision, where:

    n = the value to be truncated.
    p = position of the digit to which the value of **n** should be truncated.

**p** could be positive (to the right of the decimal) or negative (the position to the left of the decimal).

# Numerical functions with dates

ROUND(d,f) - rounds dates into the specified level defined by the format, where: **d** is the date, and f is the format:

'YEAR', 'MONTH', 'DAY', etc.

# Implicit date type conversion

```sql

From                To
------------------------------------
NUMBER              VARCHAR2 or CHAR
DATE                VARCHAR2 or CHAR
VARCHAR2 or CHAR    NUMBER
VARCHAR2 or CHAR    DATE
```

## Example

```sql

SQL> SELECT     'Order ' || order# || 'from customer ' || customer# || ' was shipped on '
                || shipdate AS "Order information"
     FROM       orders
     WHERE      orderdate = '03-APR-09'
     AND        shipdate IS NOT NULL
     AND        shipcost > '0.0';

```

## Conversion functions

TO_DATE, TO_CHAR, TO_NUMBER.

# TO_DATE

TO_DATE(d,f)
d is a string representing date
f is the format

```sql
SQL> SELECT     order#, orderdate, shopdate
     FROM       orders
     WHERE      orederdate - TO_DATE('March 31, 2009', 'Month DD, YYYY');
```

# Date formats

![Date formats]({{ site.url }}/assets/dateformats.png)

# Time and number formats

![Date formats]({{ site.url }}/assets/timeandnumbersformats.png)

# TO_CHAR (d,f)

```sql
SQL> SELECT     title,
                TO_CHAR(pubdate,'MONTH DD, YYYY') "Publication Date",
                TO_CHAR(retail, '$990.99') "Price"
    FROM        books
    WHERE       isbn = '0401140733';

TITLE               Publication Date            Price
--------------------------------------------------------------
REVENGE OF MICKEY   DECEMBER 14, 2005           $22.00
```

## Using NULL values

Any comparison will null returns unknown.

Results of **WHERE** clause predicate is treated as false if it evaluates to unknown.

Expressions that involve **NULL** values result in a **NULL** value.

** Never compare with NULL using '= NULL'. use **IS NULL**

# "Three values" logic

![Date formats]({{ site.url }}/assets/threevalueslogic.png)

# NULL functions

NVL(x,z) substitutes a value for a **NULL** similar to a default value if null.

NVL2(x,y,z)

**y** is the value to be substituted if **x** is not **NULL**
**z** is the value to be substituted if **x** is **NULL**