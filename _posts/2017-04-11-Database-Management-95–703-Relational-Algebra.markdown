---
layout: post
title:  "Database Management 95-703 - Relational Algebra"
date:   2017-04-11 10:00:00 -0500
categories:  programming oracle general motors tep technical education program sql database db casteel Oracle 11g
---


Created by Dr Codd @ IBM.

## Properties of relations

- The relation has a name that is distinct from all other relations.

- Each tuple is distinct, there are no duplicate tuples.

- The order of tuples has no significance, theoretically.

- Each attribute has a distinct name within a relation.

- The order of attributes has no significance.

- The values of an attribute are all from the same domain.

## Relational Algebra

Relational algebra operations work on one or two relations that define another relation without changing the original
relation(s).

# Operations

![Relational Algebra]({{ site.url }}/assets/relationalAlgebraOps.png)

## Selection

σ<sub>predicate</sub>(R)

- Works on a single relation R and defines a relation that contains onl those tuples (rows) of relation R that satisfy
the specified condition.

- A simple condition is:
a column name, comparison operator another column.

- **Compound** conditions - formed by connecting simple conditions using **AND, OR and NOT** operators.

# Example:

List all books in the "Computer" category:

σ<sub>Category = 'COMPUTER'</sub>(Books)

## Projection:

Works on a single relation R and defines a relation that contains a "vertical subset" of R.

# Example:

Create a list of publishers, showing only the publisher ID and name

π<sub>PubID, Name</sub>(Publisher)

## Union

R ∪ S

- Union of two relations R and S with I and J tuples, respectively, is obtanined by
"combining" them into one relation. All duplicate tuples are eliminated. Relations R and S
must be union compatible.

- Two relations are union compatible if they have the same number of attributes with each pair of
corresponding attributes having the same domain.

# Example:

Create a list of customer names from either Washington of Wyoming state.

π<sub>Customer#, Firstname, Lastname, State</sub>(σ<sub>State = 'WA'</sub>(Customers))

**∪**

π<sub>Customer#, Firstname, Lastname, State</sub>(σ<sub>State = 'WY'</sub>(Customers))

## Intersection

R ∩ S

- The intersection consists of the set of all tuples that are in both R and S

- R and S must be union-compatible

# Example:

List ID of publishers that published both family life books and children books.

π<sub>PubID</sub>(σ<sub>Category = 'FAMILY LIFE'</sub>(Books))

**∩**

π<sub>PubID</sub>(σ<sub>State = 'CHILDREN'</sub>(Books))

## Set Difference

R - S

- Relations S and S must be union-compatible

# Example:

List order number, customer number, order date and the ship date for all orders shipped to Georgia except the ones shipped
to Atlanta, Georgia.

π<sub>Order#,Customer#,OrderDate,ShipDate</sub>

(σ<sub>Shipstate = 'GA'</sub>(Orders)) - (σ<sub>Shipcity = 'Atlanta' AND Shipstate = 'GA'</sub>(Orders))

## Cartesian Product

R × S

- The Cartesian product defines a relation that is the concatenation of every tuple (row) of relation R with every tuple (row)
of relation S.

# Example

JustLee Books needs to perform a manual book inventory in all three warehouses. Therefore, we need an inventory sheet
listing each book with each warehouse.

π<sub>ISBN,Title</sub>(Books) × π<sub>Location</sub>(Warehouses)

## Cartesian Product & Selection

List book title and the publisher name that published each book (i.e. use Selection over Cartesian Product)

T <- σ<sub>Publisher.PubID = Books.PubID</sub> ((π<sub>Title,PubID</sub>(Books)) × (π<sub>Name,PubID</sub>(Publisher)))

π<sub>Title,Name</sub>(T))

Cartesian Product & Selection can be reduced to a single operation called **join**

## Renaming Relations

- Changing the name of a relation only:

**Assignment** operation:

T <- π<sub>Name, PubID</sub>(Publisher)

**Rename** operation : ρ

ρ<sub>R</sub>(T)

Renaming both the relation and its attributes

ρ<sub>R(PName,PCode)</sub>(T)

Renaming only attributes

ρ<sub>(PName, PCode)</sub>(T)

## Join Operations

Join operations are equivalent to performing a selection, over the Cartesian product of two operand relations.

This is one of the reasons why relational systems have performance problems.

There are various forms of join operation

- Theta-join ⋈

- Equi-join

- Natural join

- Outer join (⋊, ⋉, ⟗) (Left,Right,Full)

- Semi-join (▷)

## Theta-join (⋈) (θ-join)

We can rewrite the theta-join in terms of the basic Selection and Cartesian product:

R ⋈ <sub>F</sub> S = σ <sub>F</sub> (R × S)

Degree of a theta-join is a sum of the degrees of the relations R and S.

If predicate F contains only equality (=), the term equi-join is used.

# Example: Theta-join

List the book title and the publisher name of all books published by those publishers.

(π<sub>PubID, Title</sub> (Books))
    ⋈ <sub>Books.PubID = Publisher.PubID</sub>(π<sub>PubID, Name</sub> (Publisher))

## Natural Join

R ⋈ S

- Is an equi-join of the two relations R and S over all common attributes. One occurrence of each common attribut is eliminated
from the result.

# Example - Natural Join

List the book title and the publisher name of all books published by those publishers.

π<sub>PubID,Title,Name</sub>(Books  ⋈ Publisher)

## Outer Join (⋊, ⋉, ⟗) (Left,Right,Full)

Often in joining two relations there is no matching value in the join columns.

To display rows in the result that do not have matching values in the join column, we use the outer join operation:

R ⋊<sub>F</sub> S

Left outer join ⋊

Right outer join ⋉

Full outer join ⟗

If no condition is specified, we called natural outer join, if there's a condition, we called theta outer join.

# Example - Left Outer Join

List all customers. For those who placed orders, show the corresponding order number.

π<sub>FirstName, LastName, Order#</sub>(Customers ⋊ Orders)

## Semi - join (▷)

Defines a relation that contains the tuples of R that participate in the theta join of R with S.

## Division (÷)

R ÷ S

- The division consists of the set of tuples from R defined over the common attributes C that match the combination of
every tuple in S.

# Example:

List customer number and last name of the customers who ordered all books in the "Family Life" category in the past (but
not necessarily on the same order)


π<sub>Customer#,LastName, ISBN</sub>((Customers ⋈ Orders) ⋈ OrderItems)

÷

π<sub>ISBN</sub>(σ<sub>Category = "FAMILY LIFE"</sub> (Books))