---
layout: default
sidebar: classes
title: "OrderField"
description: "A class that is being used to define an ordering of the results when querying a data from the database."
permalink: /class/orderfield
tags: [repodb, orderfield]
parent: CLASSES
---

# OrderField

---

This class defines the sort order of results returned by fetch operations ([Query](/operation/query), [QueryAll](/operation/queryall), [BatchQuery](/operation/batchquery)).

## Creating an Instance

```csharp
var orderBy = new OrderField("Id", Order.Ascending);
```

Or descending.

```csharp
var orderfield = new OrderField("Id", Order.Descending);
```

## Ascending Method

```csharp
var field = OrderField.Ascending<Customer>(c => c.Id);
```

## Descending Method

```csharp
var fields = OrderField.Descending<Customer>(c => c.Id);
```

## Parse Expression

```csharp
var field = OrderField.Parse<Person>(e => e.Id, Order.Ascending);
```

Or descending.

```csharp
var field = OrderField.Parse<Person>(e => e.Id, Order.Descending);
```

## Parse Object

```csharp
var fields = OrderField.Parse(new
{
    Id = Order.Ascending
});
```

Or with multiple columns.

```csharp
var fields = OrderField.Parse(new
{
    LastName = Order.Ascending,
    FirstName = Order.Descending
});
```

## Usability

Pass it to fetch operations.

```csharp
var orderBy = new OrderField("Id", Order.Ascending);
using (var connection = new SqlConnection(connectionString0))
{
    var people = connection.QueryAll<Person>(orderBy: orderBy);
}
```
