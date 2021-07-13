---
title: "DB Table Relationship"
date: "2021-07-02T21:22:55.284Z"
template: "post"
draft: false
slug: "database-relationship"
category: "Development"
tags:
  - "JAVA"
  - "DataBase"
  - "Table"
description: "JAVA Persistent 객체 생성할 때 참고"
socialImage: ""
---

## One to One

- One record in a table
- The record is associated with one and only one record in another table
- A primary key

## One to Many

- One record in a table
- The record can be associated with one or more records in another table

## Many to Many

- Multiple records in a table
- They are associated with multiple records in another table
- Example: Student - Class
- many to many → one to many by using join table
  - The primary key Student ID uniquely identifies each student in the Students table. The primary key Class ID uniquely identifies each class in the Classes table. The Enrollments table contains the foreign keys Student ID and Class ID.
  - [https://fmhelp.filemaker.com/help/18/fmp/en/index.html#page/FMP_Help%2Fmany-to-many-relationships.html%23](https://fmhelp.filemaker.com/help/18/fmp/en/index.html#page/FMP_Help%2Fmany-to-many-relationships.html%23)

**Record : One set of fields in a database table.**
