---
layout: post
title:  "Database Management 95-703 - Managing DB Tables and Data Integrity"
date:   2017-04-09 10:00:00 -0500
categories:  programming oracle general motors tep technical education program sql database db casteel Oracle 11g
---

## Outline

Managing database tables:

- Creating tables.
- Modifiying structures.
- Deleting data and dropping tables.
- Recovering deleted tables.

Managing constraints.

## Database tables

- Consists of columns and rows.
- Can be created and modified using "data definition" (DDL) statements.

### Table and column names:

- Can contain max 30 chars & no spaces.
- Must begin with a letter.
- Can contain digits, underscore (_), and #.
- Must be unique within db.
- Column names must be unique within table.
- No reserved words are allowed.

## Creating a table


```sql
CREATE TABLE tablename
(columnname datatype [constraint],
columnname datatype [constraint],
...);
```

constraints are optional.

Max 1000 columns per table (Oracle)

### Example: Account Manager table

The management of JustLee Books needs a table for account managers employed by the company

```sql
SQL> CREATE TABLE acctmanager

    2 (amid     CHAR(4),
    3  amfirst  VARCHAR2(12),
    4  amlast   VARCHAR2(12),
    5  amedate  DATE DEFAULT SYSDATE,
    6  amsal    NUMBER(8,2),
    7  amcomm   NUMBER(7,2) DEFAULT 0,
    8  region   CHAR(2) );

Table created.
```

region is not namespaced to be used in join ops with other tables.

A table structure can ve modified using **ALTER TABLE** statements.

With the **ALTER TABLE** statement we can:

- Add/Delete a column.
- Change column characteristics.
- Create/Delete a constrain.
- Modify a constraint's definition.

### Adding a column to a table

```sql
SQL> ALTER TABLE publisher
   2 ADD (ext number(4));

Table altered.
```

- The new column is added at the end of the table and it is **NULL** for all existing rows.
- Default value can be assigned.

### Modifiying an existing column

The modification cannot affect already existing data. (Oracle)

- Typically, we can increase the width of a numeric or character column but cannot decrease it.
- Adding a default refers only to future values.

The last name of account managers could be up to 18 chars long.


```sql
SQL> ALTER TABLE acctmanager
  2  MODIFY (amlast VARCHAR2(18));

Table altered.
```

### Deleting a column.

```sql
ALTER TABLE publisher
DROP COLUMN ext;
```

### Delaying deleting a column

Dropping a column from a large table can be slow.

```sql
SQL> ALTER TABLE publisher
    SET UNUSED (ext);
```

Operation can not be undone.

```sql
SQL> ALTER TABLE publisher
    DROP UNUSED COLUMNS;
```

### Renaming tables

```sql
SQL> ALTER TABLE tablename
RENAME oldtablename TO newtablename;
```

### Dropping a table

```sql
SQL> DROP table tablename;

Table dropped.
```

## Constraints in SQL

- Rules that the data in the database must follow.
- Values are verified whenever a row is to be inserted, updated or deleted.
- Prevent the deletion of a table if there are dependencies from other tables (i.e., FKs)
- Assigning names to constraints helps with their identification.

### Constraint types

**PRIMARY KEY** - a column (or group of columns) that uniquely identifies each row in a table, it enforces uniqueness
and not null property.

**FOREIGN KEY** (or referential integrity) - value must match an existing value of a candidate key in a related table.

**CHECK** defines a condition that a column values must satisfy.

**UNIQUE** - requires that not two rows in a table have the same values in the specified column(s).

**NOT NULL** - ensures that a column contains no null values.

### Constraints can be defined at:

column level:

```sql
columname datatype [CONSTRAINT constraintname] constrainttype,
```

table level:

```sql
[CONSTRAINT constraintname]
constrainttype (columnname, ...)
```

### Examples:

Column-level constraint:
```sql
CREATE TABLE Author
(AuthorID   VARCHAR2(4) PRIMARY KEY,
Lname       VARCHAR2(10),
Fname       VARCHAR2(10));
```

Table-level constraint:

```sql
CREATE TABLE    BookAuthor
(ISBN           VARCHAR2(10),
 AuthorID       VarCHAR2(4),
 CONSTRAINT     bookauthor_pk
                PRIMARY KEY (isbn, authorid));
```


### Foreign Key constraint

A Foreign key is defined in child table.

The foreign key definition in the child table:

**FOREIGN KEY** - defines the column(s) in the child table.
**REFERENCES** - identifies the parent table and column(s) in that table.
    **ON DELETE CASCADE** - when a row in the parent table is deleted, the dependent rows in the child table are
    also deleted. **<<-- Dangerous**
    **ON DELETE SET NULL** - when a row in the parent table is deleted, the FK values in the child table are set to null.

### Check constraint

Defines a condition that must be satisfied by every row.

A single column may have serveral **CHECK** constraints.

The **NOT NULL** constraint is a special case of **CHECK** constraint.

### Common constraint indicators

**Constraint type**         **ID**
Primary Key                 PK
Foreign Key                 FK
Check                       CK
Not Null                    NN
Unique                      UK

### Adding constraints after table is created

With **ALTER TABLE** we can:

- Add/drop constraints
- Enable / disable constraints

```sql
ALTER TABLE <table_name>
ADD [CONSTRAINT <constraint_name>]
type (<column_name>);
```

### Adding Primary Key

```sql
SQL>    ALTER TABLE promotion
        ADD [CONSTRAINT promotion_gift_pk]
        PRIMARY KEY (gift);

Table altered.
```

### Adding Foreign Keys


```sql
SQL>    ALTER TABLE orders
        ADD CONSTRAINT orders_customer#_fk
        FOREIGN KEY (customer#)
        PREFERENCES Customers (customer#);

Table altered.
```

### Adding more constraints

Unique:

```sql
SQL>    ALTER TABLE publisher
2       ADD
3       CONSTRAINT publisher_name_uk UNIQUE (name);

Table altered.
```
Not null:

```sql
SQL>    ALTER TABLE books
2       MODIFY
3       (pubid CONSTRAINT books_pubid_nn NOT NULL);

Table altered.
```

Check:

```sql
SQL>    ALTER TABLE orders
        ADD
        CONSTRAINT orders_shipdate_ck
        CHECK (orderdate <= shipdate);

Table altered.
```

### Adding multiple constraints on a single column

**NOTE** Oracle doe not verify the semantics!!

```sql
SQL>    CREATE TABLE acctmanager2
        .
        .
        .
        amsal   NUMBER(8,2)
                CONSTRAINT acctmanager_amsal NOT NULL
                CONSTRAINT acctmanager_amsal_ck CHECK (amsal > 0),
        .
        .
        .

Table altered.
```

### Dropping Constraints

Use the **ALTER TABLE** statement with the **DROP CONSTRAINT** clause

When we drop a constraint, that constraint is no longer enforced by Oracle Server.

```sql
SQL>    ALTER TABLE acctmanager2
   2    DROP CONSTRAINT acctmanager_amfirst_nn;

Table altered.
```

### Disabling Constraints

```sql
SQL>    ALTER TABLE acctmanager2
        DISABLE CONSTRAINT cacctmanager_region_ck;

Table altered.
```

### Enabling Constraints

```sql
SQL>    ALTER TABLE acctmanager2
        ENABLE CONSTRAINT cacctmanager_region_ck;

Table altered.
```

### Drop Column - Cascading Constraints

```sql
SQL>  CREATE TABLE test
  2   (pk       NUMBER(3) PRIMARY KEY,
  3     fk      NUMBER(3) REFERENCES test (pk),
  4     atr1    NUMBER(7),
  5     atr2    VARCHAR2(15),
  6     CONSTRAINT check1_CK
  7          CHECK (pk > 0 and atr1 > 0));

```

The following statements give errors:

```sql
SQL> ALTER TABLE test DROP (pk);

cannot drop parent key column.
```sql

```sql
SQL> ALTER TABLE test DROP (atr1);

column is referenced in a multi-column constraint.
```sql


```sql
SQL> ALTER TABLE test
   2 DROP (pk) CASCADE CONSTRAINTS;
```sql

if other columns used by the constraints defined on the dropped column are also dropped within the same statement, then
**CASCADE CONSTRAINTS** is not required.

```sql
SQL> ALTER TABLE test
   2 DROP (pk, fk, atr1);
```sql