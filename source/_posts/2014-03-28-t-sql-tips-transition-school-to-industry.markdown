---
layout: post
title: "T-SQL Tips for Transitioning from School to Industry"
date: 2014-03-28 15:53:57 -0600
comments: true
categories: 
- SQL
tags: 
- t-sql
---
It's been 9 months since I've gone from a pure student who had only learned T-SQL in a classroom to someone who uses it everyday as a part of my job.  In that time I've learned a few lessons that I think are worth sharing.

How helpful this will be to you will of course depend upon the way your program chose to teach this subject, as well as your previous background experience.

## Conditions on JOINs

With a standard `INNER JOIN`, you can list additional conditions after the `INNER JOIN` or in the `WHERE` clause and you'll have the same query generating the same results:

```sql
--Conditions in the WHERE clause
SELECT *
FROM People AS p
    INNER JOIN Addresses AS a ON p.PeopleID = a.PeopleID
WHERE p.LastName = 'Baratheon'

--Same query/results using conditions on the INNER JOIN
SELECT *
FROM People AS p
    INNER JOIN Addresses AS a ON p.PeopleID = a.PeopleID
        AND p.LastName = 'Baratheon'
```

However, when working with an `OUTER JOIN`, conditions in the `WHERE` clause and conditions on the `OUTER JOIN` can result in very different results:

<!-- more -->

```sql
--Conditions in the WHERE clause
SELECT *
FROM People AS p
    LEFT JOIN Addresses AS a ON p.PeopleID = a.PeopleID
WHERE p.LastName = 'Baratheon'

--Very different using conditions on the LEFT JOIN
SELECT *
FROM People AS p
    LEFT JOIN Addresses AS a ON p.PeopleID = a.PeopleID
        AND p.LastName = 'Baratheon'
```

The key difference here is that the `WHERE` clause filters (removes) rows, while the condition on the join specifies that all rows from the left table should be returned but data from the right table should only be displayed when the two conditions (PeopleID and LastName = 'Baratheon') match.

Using our examples above, the first with the `WHERE` clause will `LEFT JOIN` Addresses to People and will then remove all rows where the LastName is not 'Baratheon'.  Our second example with the condition on the join will `LEFT JOIN` Addresses to People but will display all rows in the People table and only show addresses when LastName is 'Baratheon'.  Here is an [SQL Fiddle](http://sqlfiddle.com/#!3/39efc/5) where you can see some example output.  [Query 1](http://sqlfiddle.com/#!3/39efc/6) gives us two rows (for Robert and Joffrey Baratheon) with an address for Robert, while [query 2](http://sqlfiddle.com/#!3/39efc/5) lists all People but again only lists an address for Robert.

The main point to note: though these queries look similar, their results are vastly different.  

## Be careful with `NOT` and <>

Sometimes you forget a little of the theory that you are drilled on in school when putting knowledge into practice.  Case in point for me was the use of `NOT`.  When I first started out writing queries at my job, it seemed like the simplest thing in the world when someone asked to see all results except for one or two particular types.  I would just throw my `NOT IN` in the `WHERE` clause and go on my merry way.  However, remember in SQL we are working with 3-valued logic which means that an expression can evaluate to true, false or undefined.  Due to this, I started to run into problems when I used a `NOT` on columns that could contain `NULL`s, because when you compare NULL to any value the result is undefined and that row is not included in your result set.

Example time: here is another [SQL Fiddle](http://sqlfiddle.com/#!3/f7197b/1) listing a few US Presidents and their first, middle, and last names.  When we run our query for presidents who do not have a middle name of Jefferson (using `WHERE MiddleName NOT IN ('Jefferson')`), we don't quite get what we expect.  Why?  Because we are working with 3-valued logic, and comparing NULL to a value results in undefined and will not include that row in our result set.  So since Abe and Woodrow Wilson do not have middle names, we do not see them in our results.  This is a very simple example (and one easily corrected by adding `OR MiddleName IS NULL` to our `WHERE` clause), but often you will have more complicated problems involving multiple tables when you want to use `NOT`.  My go-to solution in these situations is to use a `LEFT JOIN`:

```sql
--Using LEFT JOIN instead of NOT keyword
SELECT p1.FirstName, p1.MiddleName, p1.LastName
FROM Presidents AS p1
    LEFT JOIN Presidents AS p2 ON (p1.PresidentID = p2.PresidentID)
        AND p2.MiddleName = 'Jefferson'
WHERE p2.PresidentID IS NULL
```

There are several other options you can use (here are a [few more solutions](http://www.dbatodba.com/sql-server/how-tos/typical-solutions-to-avoid-using-not-in-on-sql-server/) to try).

## Some developers alias without the keyword `AS`

When you are trying to find a solution to a problem by searching online, you can quickly get confused by the coding styles some developers use.  The biggest one for me was that the `AS` keyword for aliasing is optional and some developers do not choose to use it.

```sql
--Optional AS for aliasing can get confusing
SELECT peep.FirstName GivenName, peep.LastName SurName, addr.Street HighRoad, addr.City Castle, addr.State Continent
FROM People peep
    INNER JOIN Addresses addr ON peep.PeopleID = addr.PeopleID
WHERE peep.LastName = 'Stark'
```

You can puzzle this mess out if you've been developing for a while, but as a relative beginner this can start to look like gibberish in a hurry.  While there is little you can do to fix most of the examples on the internet, I'd encourage you to continue using `AS` for aliasing even as you improve your query writing.  Think of it as a way to pay it forward to future students who will use your code to learn.  So to make the above a little more clear:

```sql
--AS for aliasing = good!
SELECT peep.FirstName AS GivenName, peep.LastName AS SurName, addr.Street AS HighRoad, addr.City AS Castle, addr.State AS Continent
FROM People AS peep
    INNER JOIN Addresses AS addr ON peep.PeopleID = addr.PeopleID
WHERE peep.LastName = 'Stark'
```

## Using the `APPLY` operator

I learned how to use the `APPLY` operator my first day on the job, and I've been amazed since then that it was never covered in my formal education.  You can attempt to make sense of the [MSDN article on Using APPLY](http://technet.microsoft.com/en-us/library/ms175156.aspx), but I typically use it in a simple way: if I'm looking to use a `TOP 1` subquery in my `SELECT` clause, then it's time to use an `APPLY`.  But why bother you ask?  One word: performance.  Whenever I've compared execution times and plans, the version with the `APPLY` outperforms a `TOP 1` subquery almost every time.  

Going back to our people and addresses example from earlier, I've added a column, IsPreferred, for people with multiple addresses.  Now using a typical `TOP 1` subquery, our query would look like this: [SQL Fiddle](http://sqlfiddle.com/#!3/42ceb/1)

```sql
--TOP 1 subquery
SELECT p.FirstName
    , p.LastName
    , (SELECT TOP 1 Street + ' ' + City + ' ' + State
       FROM Addresses AS a
       WHERE a.PeopleID = p.PeopleID
       ORDER BY a.IsPreferred) AS Address
FROM People AS p
WHERE p.LastName = 'Stark'
    AND p.FirstName = 'Ned'
```

Now here's the `APPLY` version: [SQL Fiddle](http://sqlfiddle.com/#!3/42ceb/2):

```sql
--Using APPLY
SELECT p.FirstName
    , p.LastName
    , addr.Address
FROM People AS p
    OUTER APPLY(SELECT TOP 1 Street + ' ' + City + ' ' + State AS Address
                FROM Addresses AS a
                WHERE a.PeopleID = p.PeopleID
                ORDER BY a.IsPreferred) AS addr
WHERE p.LastName = 'Stark'
    AND p.FirstName = 'Ned'
```

These two queries look very similar, so you may be wondering why one is generally more performant than the other.  Part of the difference is due to the order of statement execution.  `SELECT` is one of the last clauses that get processed, so any subqueries in the `SELECT` list will take place well after the `FROM` and `WHERE` clauses have been processed.  But the `APPLY` version moves this subquery to the `FROM` clause, meaning it is executed very early and gives the query optimizer a lot more flexibility to produce an ideal execution plan.  (For a more complete list of the order of statement execution, see [SQL Authority](http://blog.sqlauthority.com/2009/04/06/sql-server-logical-query-processing-phases-order-of-statement-execution/)).  