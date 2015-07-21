---
layout: post
title: "T-SQL date range from the current quarter"
date: 2013-12-19 01:43:38 -0700
comments: true
categories: 
- SQL
tags:
- t-sql
- sargable
---
A [recent question on StackOverflow](http://stackoverflow.com/q/20672016/1657324) got me pondering about dates and quarters.  The user wanted to return data where the date is in the current quarter.  After some Google searches, I found this great solution to [find the first and last day of the quarter](http://www.sqlservercurry.com/2009/05/find-first-and-last-day-of-current.html) from SQL Server Curry.  From there I just added 1 day to the last day of the quarter (to make sure we are [truly capturing all date/time values](http://www.kebabshopblues.co.uk/2009/08/30/one-second-to-midnight-datetimes-in-sql-server-2005/) in the quarter) and put it all in the `WHERE` clause:

``` sql
SELECT *
FROM YourTable
WHERE DateColumn >= DATEADD(qq,DATEDIFF(qq,0,GETDATE()),0)
    AND DateColumn < DATEADD(dd,1,DATEADD(qq,DATEDIFF(qq,-1,GETDATE()),-1))
```

You may be wondering 'why not just compare the quarter and year of the DateColumn to the current date?' e.g. `WHERE YEAR(DateColumn) = YEAR(GETDATE()) AND DATEPART(QUARTER,DateColumn) = DATEPART(QUARTER,GETDATE())`  You can do that and it works fine for small datasets, but using functions on your column in a `WHERE` clause will result in a non-[sargable](http://en.wikipedia.org/wiki/Sargable) query, meaning it can't take advantage of an index on `DateColumn`.  So go with the uglier, sargable query if you are looking to optimize performance.[^1]

[^1]: I think sargability is a super interesting optimization topic.  In case you were curious, sargable is short for Search ARGument ABLE, and here's a few more articles on the topic if you would like to know more: [Sargable functions in SQL Server by Rob Farley](http://msmvps.com/blogs/robfarley/archive/2010/01/22/sargable-functions-in-sql-server.aspx) and [stackoverflow: what makes a sql statement sargable](http://stackoverflow.com/q/799584/1657324).