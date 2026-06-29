---
layout: default
sidebar: features
title: "Multiple Query"
description: "This is a feature that would allow you to fetch multiple resultsets in a single call."
permalink: /feature/multiplequery
tags: [repodb, multiplequery]
parent: FEATURES
---

# Multiple Query

---

This feature allows you to fetch multiple result sets in a single database call. Both [ExecuteQueryMultiple](/operation/executequerymultiple) and [QueryMultiple](/operation/querymultiple) operations are provided for this purpose.

[ExecuteQueryMultiple](/operation/executequerymultiple) accepts a raw SQL statement, while [QueryMultiple](/operation/querymultiple) accepts a Linq-based query expression and generates the SQL statement automatically.

Both operations abstract the [Read()](https://learn.microsoft.com/en-us/dotnet/api/system.data.common.dbdatareader.read?view=net-6.0) and [NextResult()](https://learn.microsoft.com/en-us/dotnet/api/system.data.common.dbdatareader.nextresult?view=net-7.0) methods of the [DbDataReader](https://learn.microsoft.com/en-us/dotnet/api/system.data.common.dbdatareader?view=net-6.0) object.

## Type of Return Types

[ExecuteQueryMultiple](/operation/executequerymultiple) returns an instance of [QueryMultipleExtractor](/class/querymultipleextractor), which provides control over how result sets are extracted. Execution is deferred until explicit calls to the `Extract()` or `Scalar()` methods are made.

[QueryMultiple](/operation/querymultiple) returns a [Tuple](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/value-tuples) instance with a maximum of 7 generic types, matching the maximum defined for [Tuple](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/value-tuples). The item properties of the tuple correspond to the order of the generic types passed during the call.

## Hints

For [ExecuteQueryMultiple](/operation/executequerymultiple), hints are written directly in the SQL statement. For [QueryMultiple](/class/sqlservertablehints), each query in the execution has a corresponding `hints` argument.

```csharp
var (customers, orders) = connection.QueryMultiple<Customer, Order>(c => c.Id == customerId, // Customer
    o => o.CustomerId == customerId, // Order
    hints1: "WITH (NOLOCK)", // Hints for Customer
    hints2: "WITH (NOLOCK)"); // Hints for Order
```

Use the [SqlServerTableHints](/class/sqlservertablehints) class to simplify hint values:

```csharp
var (customers, orders) = connection.QueryMultiple<Customer, Order>(c => c.Id == customerId, // Customer
    o => o.CustomerId == customerId, // Order
    hints1: SqlServerTableHints.NoLock, // Hints for Customer
    hints2: SqlServerTableHints.NoLock); // Hints for Order
```

## Ordering

For [ExecuteQueryMultiple](/operation/executequerymultiple), ordering is written directly into the SQL statement. For [QueryMultiple](/class/sqlservertablehints), each query has a corresponding `orderBy` argument via the [OrderField](/class/orderfield) class.

```csharp
var (customers, orders) = connection.QueryMultiple<Customer, Order>(c => c.Id == customerId, // Customer
    o => o.CustomerId == customerId, // Order
    orderBy1: OrderField.Ascending<Customer>(c => c.DateInsertedUtc), // Ordering for Customer
    orderBy2: OrderField.Ascending<Order>(o => o.OrderDateUtc)); // Ordering for Order
```

## Filtering

For [ExecuteQueryMultiple](/operation/executequerymultiple), filtering is written using `TOP` or `LIMIT` in the SQL statement. For [QueryMultiple](/class/sqlservertablehints), each query has a corresponding `top` argument.

```csharp
var (customers, orders) = connection.QueryMultiple<Customer, Order>(c => c.Id == customerId, // Customer
    o => o.CustomerId == customerId, // Order
    top2: 10); // Filtering for Order
```
---

## Single Parent with Multiple Children

For raw SQL, use the [ExecuteQueryMultiple](/operation/executequerymultiple) method:

```csharp
using (var connection = new SqlConnection(connectionString))
{
    using (var extractor = connection.ExecuteQueryMultiple(@"SELECT * FROM [dbo].[Customer] WITH (NOLOCK) WHERE [Id] = @CustomerId;
        SELECT * FROM [dbo].[Order] WITH (NOLOCK) WHERE [CustomerId] = @CustomerId;",
        new { CustomerId = 10045 }))
    {
        // Extract the first result
        var customer = extractor.Extract<Customer>().FirstOrDefault();

        // Extract the second result
        var orders = extractor.Extract<Order>().AsList();

        // Set the child orders
        customer.Orders = orders;

        // Process the 'customer' here
    }
}
```

For the fluent method, use [QueryMultiple](/operation/querymultiple):

```csharp
using (var connection = new SqlConnection(connectionString))
{
    // The target key
    var customerId = 10045;

    // Execution by passing the order of entity
    var (customers, orders) = connection
        .QueryMultiple<Customer, Order>(c => c.Id == customerId, // Customer
            o => o.CustomerId == customerId, // Order
            hints1: SqlServerTableHints.NoLock, // Hints for Customer
            hints2: SqlServerTableHints.NoLock); // Hints for Order

    // Extract the customer
    var customer = customers.FirstOrDefault();

    // Extract the orders
    customer.Orders = orders.AsList();

    // Process the 'customer' here
}
```

## Querying Multiple Parent and Multiple Children

For raw SQL, use the [ExecuteQueryMultiple](/operation/executequerymultiple) method:

```csharp
using (var connection = new SqlConnection(connectionString))
{
    using (var extractor = connection.ExecuteQueryMultiple(@"SELECT [Id], [Name] FROM [dbo].[Customer] WITH (NOLOCK) WHERE [Id] IN (@Keys);
        SELECT [Id], [CustomerId], [ProductId], [Price], [Quantity], [OrderDateUtc] FROM [dbo].[Order] WITH (NOLOCK) WHERE [CustomerId] IN (@Keys);",
        new { Keys = new [] { 10045, ..., 11211 }))
    {
        // Extract the first result
        var customers = extractor.Extract<Customer>().AsList();

        // Extract the second result
        var orders = extractor.Extract<Order>();

        // Iterate the customers and map all the orders
        customers.ForEach(
            c => c.Orders = orders.Where(o => o.CustomerId == c.Id).AsList());

        // Process the 'customers' here
    }
}
```

For the fluent method, use [QueryMultiple](/operation/querymultiple):

```csharp
using (var connection = new SqlConnection(connectionString))
{
    // List of keys to query
    var keys = new [] { 10045, ..., 11211 };

    // Execution by passing the order of types
    var (customers, orders) = connection
        .QueryMultiple<Customer, Order>(c => keys.Contains(c.Id), o => keys.Contains(o.CustomerId),
            hints1: SqlServerTableHints.NoLock, hints2: SqlServerTableHints.NoLock);

    // Iterate the customers and map all the orders
    customers.AsList().ForEach(
        c => c.Orders = orders.Where(o => o.CustomerId == c.Id).AsList());

    // Process the 'customers' here
}
```

{: .note }
> Please visit our [Multiple Resultsets](/reference/multipleresultsets) reference implementation page for more details.
