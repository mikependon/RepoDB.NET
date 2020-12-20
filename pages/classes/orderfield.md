---
layout: navpage
sidebar: classes
title: "OrderField"
description: "A class that is used to define an ordering of the results when querying a data from the database."
permalink: /class/orderfield
tags: [repodb, class, orderfield, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# OrderField

This is the class that is used to define an ordering of the results from any fetch operations (i.e.: [Query](/operation/query), [QueryAll](/operation/queryall) or [BatchQuery](/operation/batchquery)).

#### Creating an Instance

```csharp
var orderBy = new OrderField("Id", Order.Ascending);
```

Or by descending.

```csharp
var orderfield = new OrderField("Id", Order.Descending);
```

#### Ascending Method

You can also create by using the `Ascending` method.

```csharp
var field = OrderField.Ascending<Customer>(c => c.Id);
```

#### Descending Method

Also by using the `Descending` method.

```csharp
var fields = OrderField.Descending<Customer>(c => c.Id);
```

#### Parse Expression

You can also extract by parsing an expression.

```csharp
var field = OrderField.Parse<Person>(e => e.Id, Order.Ascending);
```

Or by descending.

```csharp
var field = OrderField.Parse<Person>(e => e.Id, Order.Descending);
```

#### Parse Object

You can also extract by parsing an anonymous object.

```csharp
var fields = OrderField.Parse(new
{
    Id = Order.Ascending
});
```

Or by multiple columns.

```csharp
var fields = OrderField.Parse(new
{
    LastName = Order.Ascending,
    FirstName = Order.Descending
});
```

#### How to use?

Simply pass it on the operations like below.

```csharp
var orderBy = new OrderField("Id", Order.Ascending);
using (var connection = new SqlConnection(connectionString0))
{
    var people = connection.QueryAll<Person>(orderBy: orderBy);
}
```


