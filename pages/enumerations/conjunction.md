---
layout: default
sidebar: enumerations
title: "Conjunction"
description: "A enumeration that defines the conjunctions to be used when composing a query expression."
permalink: /enumeration/conjunction
tags: [repodb, conjunction]
parent: ENUMERATIONS
---

# Conjunction

---

This enum defines the conjunction used when grouping expressions in a [QueryGroup](/class/querygroup) object.

## Enum Values

| Name | Description |
|:-----|:------------|
! And | Is used to define an `AND` expression during the grouping. This is the default setting. |
| Or | Is used to define an `OR` expression during the grouping |

## Usability

Pass the value in the constructor when instantiating a [QueryGroup](/class/querygroup) object.

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

The query group above produces the following SQL statement.

```csharp
> WHERE ([Id] >= @Id AND [CreatedDateUtc] >= @CreatedDateUtc)
```

To use `Or` instead of the default `And`, pass `Conjunction.Or` explicitly.

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

Which produces the following SQL statement.

```csharp
> WHERE ([Id] = @Id OR [Id] = @Id2)
```
