---
layout: default
sidebar: operations
title: "QueryMultiple"
permalink: /operation/querymultiple
tags: [repodb, tutorial, querymultiple]
parent: OPERATIONS
---

# QueryMultiple

---

This method queries data as multiple result sets from the table based on the given target types.

## Code Snippets

The following example fetches a single `Customer` row and all related `Order` rows.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var result = connection.QueryMultiple<Customer, Order>(p => p.Id == 10045,
        o => o.PersonId == 10045);
    var customer = result.Item1.FirstOrDefault();
    var orders = result.Item2.AsList();

    // Process 'customer' and 'orders' here
}
```

{: .note }
> Returns a `Tuple<T1, .., T7>` object. Up to 7 types are supported. Each tuple item is an [IEnumerable](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.ienumerable-1?view=net-7.0) of the corresponding generic type.

## Table Hints

Pass table hints via the `hints` arguments.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var result = connection.QueryMultiple<Customer, Order>(p => p.Id == 10045,
        o => o.PersonId == 10045,
        hints1: "WITH (NOLOCK)",
        hints2: "WITH (NOLOCK)");
    var customer = result.Item1.FirstOrDefault();
    var orders = result.Item2.AsList();

    // Process 'customer' and 'orders' here
}
```

Or use the [SqlServerTableHints](/class/sqlservertablehints) class.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var result = connection.QueryMultiple<Customer, Order>(p => p.Id == 10045,
        o => o.PersonId == 10045,
        hints1: SqlServerTableHints.TabLock,
        hints2: SqlServerTableHints.TabLock);
    var customer = result.Item1.FirstOrDefault();
    var orders = result.Item2.AsList();

    // Process 'customer' and 'orders' here
}
```

## Ordering the Results

Pass an array of `OrderField` objects in the `orderBy` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var orderBy = OrderField.Parse(new
    {
        OrderDateUtc = RepoDb.Enumerations.Order.Descending
    });
    var result = connection.QueryMultiple<Customer, Order>(p => p.Id == 10045,
        o => o.PersonId == 10045,
        orderBy2: orderBy);
    var customer = result.Item1.FirstOrDefault();
    var orders = result.Item2.AsList();

    // Process 'customer' and 'orders' here
}
```

## Filtering the Results

Pass a value in the `top` argument to limit the number of rows returned.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var result = connection.QueryMultiple<Customer, Order>(p => p.Id == 10045,
        o => o.PersonId == 10045,
        top2: 100);
    var customer = result.Item1.FirstOrDefault();
    var orders = result.Item2.AsList();

    // Process 'customer' and 'orders' here
}
```
