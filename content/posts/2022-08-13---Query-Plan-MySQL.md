---
title: "Query plan in mysql"
date: "2022-08-13T22:20:10.284Z"
template: "post"
draft: false
slug: "query-plan"
category: "Development"
tags:
  - "SQL"
  - "MySQL"
description: "쿼리 실행 계획 확인하기"
socialImage: ""
---


- explain keyword (and describe are synonyms)
- used to obtain a query execution plan (how Mysql would execute a query)


## Explain output
[https://dev.mysql.com/doc/refman/8.0/en/explain-output.html](https://dev.mysql.com/doc/refman/8.0/en/explain-output.html)

without index
![without_index](/media/without_index.jpg)

with index
![with_index](/media/with_index.jpg)

****EXPLAIN Join Types****

The `type` column of `[EXPLAIN](https://dev.mysql.com/doc/refman/8.0/en/explain.html)` output describes how tables are joined. In JSON-formatted output, these are found as values of the `access_type` property. The following list describes the join types, ordered from the best type to the worst:

- `[system](https://dev.mysql.com/doc/refman/8.0/en/explain-output.html#jointype_system)`
    
    The table has only one row (= system table). This is a special case of the `[const](https://dev.mysql.com/doc/refman/8.0/en/explain-output.html#jointype_const)` join type.
    
- `[const](https://dev.mysql.com/doc/refman/8.0/en/explain-output.html#jointype_const)`
    
    The table has at most one matching row, which is read at the start of the query. Because there is only one row, values from the column in this row can be regarded as constants by the rest of the optimizer. `[const](https://dev.mysql.com/doc/refman/8.0/en/explain-output.html#jointype_const)` tables are very fast because they are read only once.
    
Only rows that are in a given range are retrieved, using an index to select the rows. The `key` column in the output row indicates which index is used. The `key_len` contains the longest key part that was used. The `ref` column is `NULL` for this type.

- `[range](https://dev.mysql.com/doc/refman/8.0/en/explain-output.html#jointype_range)` can be used when a key column is compared to a constant using any of the `[=](https://dev.mysql.com/doc/refman/8.0/en/comparison-operators.html#operator_equal)`, `[<>](https://dev.mysql.com/doc/refman/8.0/en/comparison-operators.html#operator_not-equal)`, `[>](https://dev.mysql.com/doc/refman/8.0/en/comparison-operators.html#operator_greater-than)`, `[>=](https://dev.mysql.com/doc/refman/8.0/en/comparison-operators.html#operator_greater-than-or-equal)`, `[<](https://dev.mysql.com/doc/refman/8.0/en/comparison-operators.html#operator_less-than)`, `[<=](https://dev.mysql.com/doc/refman/8.0/en/comparison-operators.html#operator_less-than-or-equal)`, `[IS NULL](https://dev.mysql.com/doc/refman/8.0/en/comparison-operators.html#operator_is-null)`, `[<=>](https://dev.mysql.com/doc/refman/8.0/en/comparison-operators.html#operator_equal-to)`, `[BETWEEN](https://dev.mysql.com/doc/refman/8.0/en/comparison-operators.html#operator_between)`, `[LIKE](https://dev.mysql.com/doc/refman/8.0/en/string-comparison-functions.html#operator_like)`, or `[IN()](https://dev.mysql.com/doc/refman/8.0/en/comparison-operators.html#operator_in)` operators:

```sql
SELECT * FROM *tbl_name*WHERE *key_column* = 10;

SELECT * FROM *tbl_name*WHERE *key_column* BETWEEN 10 and 20;

SELECT * FROM *tbl_name*WHERE *key_column* IN (10,20,30);

SELECT * FROM *tbl_name*WHERE *key_part1* = 10 AND *key_part2* IN (10,20,30);
```
…

- `[ALL](https://dev.mysql.com/doc/refman/8.0/en/explain-output.html#jointype_all)`
    
    A full table scan is done for each combination of rows from the previous tables. This is normally not good if the table is the first table not marked `[const](https://dev.mysql.com/doc/refman/8.0/en/explain-output.html#jointype_const)`, and usually *very* bad in all other cases. Normally, you can avoid `[ALL](https://dev.mysql.com/doc/refman/8.0/en/explain-output.html#jointype_all)` by adding indexes that enable row retrieval from the table based on constant values or column values from earlier tables.
    

**Filtered**

- `filtered` (JSON name: `filtered`)
    
    The `filtered` column indicates an estimated percentage of table rows that are filtered by the table condition. The maximum value is 100, which means no filtering of rows occurred. Values decreasing from 100 indicate increasing amounts of filtering. `rows` shows the estimated number of rows examined and `rows` × `filtered` shows the number of rows that are joined with the following table. For example, if `rows` is 1000 and `filtered` is 50.00 (50%), the number of rows to be joined with the following table is 1000 × 50% = 500.


## Reference
[https://dev.mysql.com/doc/refman/8.0/en/explain.html](https://dev.mysql.com/doc/refman/8.0/en/explain.html)
