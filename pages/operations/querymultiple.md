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

This method is used to query the data as multiple resultsets from the table based on the given number of target types.

## Code Snippets

Below is the sample code to fetch a single parent row from the `[dbo].[Customer]` table and all the related orders made from the `[dbo].[Order]` table.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var result = connection.QueryMultiple<Customer, Order>(p => p.Id == 10045,
        o => o.PersonId == 10045);
    var customer = result.Item1.FirstOrDefault();
    var orders = result.Item2.AsList();

    // Do the stuffs for the `customer` and `orders` here
}
```

{: .note }
> It returns a type of `Tuple<T1, .., T7>` object. The maximum type that it can cater is 7. Each item in the tuple object is an [IEnumerable<T>](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.ienumerable-1?view=net-7.0) object of the target generic type.

## Table Hints

To pass a hint, simply write the table-hints and pass it in the `hints` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var result = connection.QueryMultiple<Customer, Order>(p => p.Id == 10045,
        o => o.PersonId == 10045,
        hints1: "WITH (NOLOCK)",
        hints2: "WITH (NOLOCK)");
    var customer = result.Item1.FirstOrDefault();
    var orders = result.Item2.AsList();

    // Do the stuffs for the `customer` and `orders` here
}
```

Or, you can use the [SqlServerTableHints](/class/sqlservertablehints) class.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var result = connection.QueryMultiple<Customer, Order>(p => p.Id == 10045,
        o => o.PersonId == 10045,
        hints1: SqlServerTableHints.TabLock,
        hints2: SqlServerTableHints.TabLock);
    var customer = result.Item1.FirstOrDefault();
    var orders = result.Item2.AsList();

    // Do the stuffs for the `customer` and `orders` here
}
```

## Ordering the Results

To order the results, you have to pass an array of `OrderField` objects in the `orderBy` argument.

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

    // Do the stuffs for the `customer` and `orders` here
}
```

## Filtering the Results

To filter the results, you have to pass a value at the `top` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var result = connection.QueryMultiple<Customer, Order>(p => p.Id == 10045,
        o => o.PersonId == 10045,
        top2: 100);
    var customer = result.Item1.FirstOrDefault();
    var orders = result.Item2.AsList();

    // Do the stuffs for the `customer` and `orders` here
}
```
