---
title: "SQL Statement Fundamentals"
date: "2022-03-09T22:21:10.284Z"
template: "post"
draft: false
slug: "sql-fundamentals"
category: "Development"
tags:
  - "Postgre"
  - "Statements"
description: "retrieve information from a table with statements"
socialImage: ""
---

# SELECT

retrieve information from a table

# DISTINCT

return only the distinct values in a column

```sql
SELECT DISTINCT column FROM table;
SELECT DISTINCT rating FROM film;
```

# COUNT

return the number of rows in a table 

```sql
SELECT COUNT(*) FROM table;
SELECT COUNT(*) FROM film;      // same
SELECT COUNT(rating) FROM film; // same
```

normally use COUNT with DISTINT

```sql
SELECT COUNT(DISTINCT rating ) FROM film;
```

# WHERE

### Comparison Operators

### Logical Operators

```sql
SELECT COUNT(*) FROM film 
WHERE rental_rate > 4 AND replacement_cost >= 19.99
AND rating='R';
```

# ORDER BY

filtering with ASC or DESC

```sql
SELECT store_id, first_name, last_name FROM customer
ORDER BY store_id DESC, first_name ASC;
```

# LIMIT

at the very end of a query request (last command)

the number of the result are limited by LIMIT command 

```sql
SELECT * FROM payment
ORDER BY payment_date DESC 
LIMIT 5;
```

# BETWEEN

- same as comparison operator
- used with dates

```sql
SELECT * FROM payment
WHERE amount BETWEEN 8 AND 9;

SELECT COUNT(*) FROM payment
WHERE amount NOT BETWEEN 8 AND 9;

SELECT * FROM payment
WHERE payment_date BETWEEN '2007-02-01' AND '2007-02-15';
```

# IN

create a condition if a value is included in a list of multiple options

```sql
SELECT DISTINCT (amount) FROM payment
WHERE amount NOT IN (0.99, 1.98, 1.99)
```

# LIKE

pattern matching against string data with the use of wildcard characters

- ILIKE : case-insensitive
- Percent % : matches any sequence of characters
- Underscore _ : matches any single character