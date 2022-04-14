---
title: "SQL GOUP BY"
date: "2022-03-11T22:20:10.284Z"
template: "post"
draft: false
slug: "sql-group-by"
category: "Development"
tags:
  - "SQL"
  - "Postgre"
  - "Statements"
description: "rAllows us to aggregate columns per some category"
socialImage: ""
---
## GROUP BY

Allows us to aggregate columns per some category

```sql
GROUP BY per category
```

![group_by](/media/group_by.jpg)

[https://www.simplilearn.com/tutorials/sql-tutorial/group-by-in-sql#:~:text=The Group By statement is,MAX() and SUM()](https://www.simplilearn.com/tutorials/sql-tutorial/group-by-in-sql#:~:text=The%20Group%20By%20statement%20is,MAX()%20and%20SUM()).

## Aggregate functions

### AVG() with ROUND()

### COUNT()

### SUM()

```sql
SELECT MAX(replacement_cost), MIN(replacement_cost), SUM(replacement_cost) FROM film;
SELECT ROUND(AVG(replacement_cost), 3), MIN(replacement_cost) FROM film;
```

**ERROR Message: column "film.replacement_cost" must appear in the GROUP BY clause or be used in an aggregate function**

- In the SELECT statement, columns must either have an aggregate function or be in the GROUP BY command

```sql
// The total amount per customer id
SELECT customer_id, SUM(amount) FROM payment
GROUP BY customer_id
ORDER BY SUM(amount) DESC

// The total transaction per customer id
SELECT customer_id, COUNT(amount) FROM payment
GROUP BY customer_id
ORDER BY COUNT(amount) DESC

// With date 
SELECT DATE(payment_date), SUM(amount) FROM payment
GROUP BY DATE(payment_date)
ORDER BY SUM(amount)

SELECT rating, AVG(replacement_cost) from film
GROUP BY rating;

// top 5 customer
SELECT customer_id, SUM(amount) from payment
GROUP BY customer_id
ORDER BY SUM(amount) DESC
LIMIT 5
```

## HAVING

Allows us to filter after an aggregation

```sql
// 40 or more transaction payment
SELECT customer_id, COUNT(*) from payment
GROUP BY customer_id
HAVING COUNT(*) >= 40;
```
