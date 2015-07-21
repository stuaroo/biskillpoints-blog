---
layout: post
title: "Updating data using the Entity Framework"
date: 2013-11-24 17:47:45 -0700
comments: true
categories: 
- Web Development
tags:
- snippets
- c#
---
How do you update your database using C# and the Entity Framework?[^1]  Basically you map an object to your database record, perform your change (be it changing a property on an update, or removing an object on a delete), and save changes to the Entity.  Here are a few examples using LINQ to map objects:[^2]

```csharp with context as your Entity
Entities context = new Entities();
```

```csharp Update
Person p = (from p in context.Person
            where p.LastName == "Rodriguez"
            select p).FirstOrDefault();

p.FirstName = "Robert";

context.SaveChanges();
```

```csharp Delete
Person p = (from p in context.Person
            where p.LastName == "Rodriguez"
            select p).FirstOrDefault();

context.Person.DeleteObject(p);

context.SaveChanges();
```

```csharp Insert
Person p = new Person() { FirstName = "Robert", LastName = "Rodriguez" };

context.AddToPerson(p);

context.SaveChanges();
```

Sources:
[^1]: [Update an Entity with ObjectContext](http://www.entityframeworktutorial.net/update-entity-in-entity-framework.aspx)
[^2]: [ADO.NET Entity: Insert Update and Delete](http://blogs.msdn.com/b/wriju/archive/2008/08/21/ado-net-entity-insert-update-and-delete.aspx)