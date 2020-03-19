---
layout: page
title: "Conjunction (RepoDb)"
permalink: /enumeration/conjunction
tags: [repodb, class, conjunction, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

## Conjunction

This is an enum used to define a conjunction when grouping an expression via [QueryGroup](/class/querygroup) object.

> In this tutorial, we will use the `SQL Server` as the database and `C#` as the programming language.

#### How to Use?

Simply pass it in the constructor when instantiating a [QueryGroup](/class/querygroup) object.

```csharp
var query = new QueryGroup
(
        new []
        {
                new QueryField("Id", Operation.GreaterThanOrEqual, 10000),
                new QueryField("CreatedDateUtc", Operation.GreaterThanOrEqual, DateTime.UtcNow.Date.AddMonths(-3))
        },
        Conjunction.And
);
```

The query group above would result a SQL statement below.

```csharp
> WHERE ([Id] >= @Id AND [CreatedDateUtc] >= @CreatedDateUtc)
```

Although by default, the value is `And`, you can override it by passing the `Or`.

```csharp
var query = new QueryGroup
(
        new []
        {
                new QueryField("Id", 10045),
                new QueryField("Id", 10057)
        },
        Conjunction.Or
);
```

Which would result a SQL statement below.

```csharp
> WHERE ([Id] = @Id AND [Id] = @Id2)
```
