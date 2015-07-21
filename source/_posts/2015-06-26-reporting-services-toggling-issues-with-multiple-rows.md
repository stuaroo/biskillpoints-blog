title: "Reporting Services: toggling issues with multiple rows"
date: 2015-06-26 23:16:36
categories: 
- Reporting
tags:
- ssrs
---
## The Problem

Using row visibility to enable toggling on more than one tablix row causes odd behavior.  Drill downs do not work as expected, and all data shows when the report is initially run

### Some Background 

A colleague of mine recently encountered this issue and it brought me back to when I was first learning SSRS and experienced similar odd behavior.  Reporting Services has a *unique* way of doing things sometimes, which makes it a challenging learning process to figure your way around problems. So I'll recreate how you might run into this problem, and show the general way to fix this.

## Problem Setup

First we'll need a query/dataset to build our tablix on.  I've been messing around with the [StackExchange databases](https://archive.org/details/stackexchange) ever since I found out they are downloadable, so this will be my demo database.  I'm working with one of the smaller ones, [DBA Stack Exchange](http://dba.stackexchange.com/), in this example.  (footnote?) As an aside, for a great article on downloading SE databases and importing data to your SQL Server, see [this article on brentozar.com](http://www.brentozar.com/archive/2014/01/how-to-query-the-stackexchange-databases/). 

<!-- more -->

Alright, so we've got our data, now we need a query to build this report around.  Let's say I want to see some basic user data including location:

```sql
SELECT COALESCE(u.Location, 'No Location') AS Location
    , CAST(u.CreationDate AS DATE) AS CreationDate
    , u.DisplayName
    , u.Id
    , u.Reputation
    , u.UpVotes
    , u.DownVotes
    , u.[Views] AS ProfileViews
FROM Users AS u
ORDER BY CAST(u.CreationDate AS DATE) DESC
    , u.Location 
    , u.Reputation DESC
```

This will show us some basic stats for a user along with their location and the day they created their account.  Some users do not fill out the Location field, so I'm using 'No Location' when this field is NULL.

After setting our data source and embedding our query into the report, let's setup a basic stepped table to use with our drilldowns.  We are going to drilldown from CreationDate to Location and finally to an individual user's details.  If you're unsure how to do this, start with a new blank tablix and click on the dropdown on Details row group > Add Group > Parent Group and create the Location row group.  

![Adding a new parent group][1]

Then add another parent group for CreationDate.  Here's what our report looks like:

![Basic layout of the tablix][2]

Now we'll add our drilldown toggles, starting with the location drilldown.  Right-click the detail row and select Row Visibility.  We want these rows to initially be hidden, but be toggle-able by the Location textbox.

![Toggling row visibility on the details row][3]

I sorted my Location and CreationDate groups descending to see more interesting results and with our new toggle on we can drill down to see our users, excellent

![Previewing the detail row drilldown][4]

Now we'll add another toggle to roll everything up to the CreationDate level.  Following the same process as before: right-click on our row with Location and set our toggle to use the CreationDate textbox.

![Toggling row visiblity on the location row][5]

Now when we run the report, our drilldown toggles are ignored and all our data is visible.

![Previewing report and drilldowns broken][6]

So what's the problem?  The reason we are seeing such odd behavior is because we set up our toggling on rows and we have textboxs that span multiple rows.  Look again when we select the location row:

![Location textbox spans multiple rows][7]

Toggling off these multi-row textboxes can cause all sorts of strange behavior, and it's not recommended to use these cells for a multi-level toggle.

## The Solution

We need to set our toggling and visibility on the Group level instead of on the Row level.  First we'll clear out our row level toggles (on the location and details rows, right-click > Row Visibility and select Show and untick Display can be toggled by this report item).  Now we can click the dropdown on Location row group > Group Properties.  Clicking the Visibility tab will show the same options we saw on row visibility.  Setting our toggling options here will result in Reporting Services hiding sections of the report by group instead of row.

![Toggling on row groups instead][8]

Repeat this process with the Details row group and now your toggles should work as expected.

![Previewing report with correct drilldowns][9]

TL;DR Set visibility and toggling on Groups rather than on Rows

[1]: http://res.cloudinary.com/biskillpoints/image/upload/v1435365317/toggling-problems-new-parent-group_yr07kb.jpg
[2]: http://res.cloudinary.com/biskillpoints/image/upload/v1435365317/toggling-problems-tablix-layout_qqxz6t.jpg
[3]: http://res.cloudinary.com/biskillpoints/image/upload/v1435365317/toggling-problems-row-visibility_vyfcid.jpg
[4]: http://res.cloudinary.com/biskillpoints/image/upload/v1435365317/toggling-problems-first-drill_t0te9w.jpg
[5]: http://res.cloudinary.com/biskillpoints/image/upload/v1435365317/toggling-problems-row-visibility-2_n5okio.jpg
[6]: http://res.cloudinary.com/biskillpoints/image/upload/v1435365317/toggling-problems-second-drill-broken_gtyb51.jpg
[7]: http://res.cloudinary.com/biskillpoints/image/upload/v1435365317/toggling-problems-textbox-multi-rows_g5pazw.jpg
[8]: http://res.cloudinary.com/biskillpoints/image/upload/v1435365317/toggling-problems-row-group-visibility_vidnxa.jpg
[9]: http://res.cloudinary.com/biskillpoints/image/upload/v1435365317/toggling-problems-fixed_ow4py0.jpg