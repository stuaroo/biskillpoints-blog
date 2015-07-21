---
layout: post
title: "Quick thoughts after taking Microsoft Exam 70-461"
date: 2014-05-01 15:02:08 -0700
comments: true
categories: 
- SQL
tags: 
- t-sql
- microsoft
- certification exams
---
- 44 questions, with 2 hours provided to answer the questions and a 700 score needed to pass

- To study I primarily used the official training kit ([Amazon link](http://www.amazon.com/Training-Kit-Exam-70-461-Microsoft/dp/0735666059)) and drew from what I learned on the job writing queries for 9 months. 

- Learned a lot from the book, but practice tests on the included CD were WORTHLESS in preparing for the test.  The only two points they helped on 1) Make sure you pay attention to the actual question asked (e.g. if it asks for a `MAX` don't choose the answer with an `AVG`), and 2) some of the questions/answers deal with vocabulary terms you should become familiar with (transaction isolation levels for me).  The thing that bothered me most about the CD practice test: its ease makes you underestimate the real test.  

- [Microsoft's outline](https://www.microsoft.com/learning/en-us/exam-70-461.aspx#skills) really does a good job of laying out what to expect.  The areas I really struggled with were a lot of things I use infrequently or have never used: Triggers, transaction isolation levels, XML output.  I've pasted in the MS study outline and **bolded** parts that I think would have helped a bunch had I been more familiar with them.

<!-- more -->

## Create database objects (24%)
* Create and alter tables using T-SQL syntax (simple statements)
    * Create tables without using the built in tools; ALTER; DROP; ALTER COLUMN; CREATE
* Create and alter views (simple statements)
    * Create indexed views; create views without using the built in tools; CREATE, ALTER, DROP
* Design views
    * Ensure code non regression by keeping consistent signature for procedure, views and function (interfaces); security implications
* Create and modify constraints (simple statements)
    * Create constraints on tables; define constraints; unique constraints; default constraints; primary and foreign key constraints
* Create and alter DML triggers.
    * **Inserted and deleted tables; nested triggers; types of triggers**; update functions; handle multiple rows in a session; performance implications of triggers

## Work with data (27%)
* Query data by using SELECT statements
    * Use the ranking function to select top(X) rows for multiple categories in a single query; write and perform queries efficiently using the new (SQL 2005/8->) code items such as synonyms, and joins (**except, intersect**); implement logic which uses dynamic SQL and system metadata; write efficient, technically complex SQL queries, including all types of joins versus the use of derived tables; determine what code may or may not execute based on the tables provided; given a table with constraints, determine which statement set would load a table; use and understand different data access technologies; case versus isnull versus coalesce
* Implement sub-queries
    * Identify problematic elements in query plans; pivot and unpivot; apply operator; cte statement; with statement
* Implement data types
    * Use appropriate data; understand the uses and limitations of each data type; impact of GUID (newid, newsequentialid) on database performance, when to use what data type for columns
* Implement aggregate queries
    * **New analytic functions; grouping sets; spatial aggregates; apply ranking functions**
* Query and manage XML data
    * **Understand XML datatypes and their schemas** and interop w/, limitations and restrictions; implement XML schemas and handling of XML data; XML data: how to handle it in SQL Server and when and when not to use it, including XML namespaces; import and export XML; XML indexing

## Modify data (24%)
* Create and alter stored procedures (simple statements)
    * Write a stored procedure to meet a given set of requirements; branching logic; create stored procedures and other programmatic objects; techniques for developing stored procedures; different types of storeproc result; create stored procedure for data access layer; program stored procedures, triggers, functions with T-SQL
* Modify data by using INSERT, UPDATE, and DELETE statements
    * Given a set of code with defaults, constraints, and triggers, determine the output of a set of DDL; **know which SQL statements are best to solve common requirements; use output statement**
* Combine datasets
    * **Difference between UNION and UNION all**; case versus isnull versus coalesce; modify data by using MERGE statements
* Work with functions
    * Understand deterministic, non-deterministic functions; **scalar and table values**; apply built-in scalar functions; create and alter user-defined functions (UDFs)

## Troubleshoot and optimize (25%)
* Optimize queries
    * Understand statistics; read query plans; plan guides; DMVs; hints; statistics IO; dynamic vs. parameterized queries; describe the different join types (HASH, MERGE, LOOP) and describe the scenarios they would be used in
* Manage transactions
    * Mark a transaction; understand begin tran, commit, and rollback; implicit vs explicit transactions; **isolation levels**; scope and type of locks; trancount
* Evaluate the use of row-based operations vs. set-based operations
    * When to use cursors; **impact of scalar UDFs**; combine multiple DML operations
* Implement error handling
    * **Implement try/catch/throw**; use set based rather than row based logic; transaction management