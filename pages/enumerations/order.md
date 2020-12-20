---
layout: navpage
sidebar: enumerations
title: "Order"
description: "A enumeration that defines the list of ordering when querying a data from the database."
permalink: /enumeration/order
tags: [repodb, class, order, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# Order

This enum is used to define an ordering of the query results. It is usually used in [Query](/operation/query), [QueryAll](/operation/queryall) and [BatchQuery](/operation/batchquery) operations.

#### Enum Values

- `Ascending` - is used to define an ascending order. This is the default setting.
- `Descending` - is used to define a descending order.

#### How to use?

Simply use the value when creating an [OrderField](/class/orderfield) object.

```csharp
var orderBy = new OrderField("LastName", Order.Ascending);
```

Or, when parsing a dynamic or class object via [OrderField](/class/orderfield) object.

```csharp
var orderBy = OrderField.Parse(new
{
    LastName = Order.Ascending,
    FirstName = Order.Descending
});
```

Or via `Ascending` or `Descending` method of the [OrderField](/class/orderfield) object.

```csharp
var orderBy = OrderField.Descending<Person>(p => p.LastName);
```