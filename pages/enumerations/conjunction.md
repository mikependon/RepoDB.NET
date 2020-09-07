---
layout: navpage
sidebar: enumerations
title: "Conjunction"
description: "A enumeration that defines the conjunctions to be used when composing a query expression."
permalink: /enumeration/conjunction
tags: [repodb, class, conjunction, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# Conjunction

This is an enum used to define a conjunction when grouping an expression via [QueryGroup](/class/querygroup) object.

#### Enum Values

- `And (Default)` - is used to define an `AND` expression during the grouping.
- `Or` - is used to define an `OR` expression during the grouping.

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
> WHERE ([Id] = @Id OR [Id] = @Id2)
```
