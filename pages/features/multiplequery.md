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

This is a feature that allows you to fetch multiple resultsets in a single call from the database. Both the [ExecuteQueryMultiple](/operation/executequerymultiple) and [QueryMultiple](/operation/querymultiple) operations were provided to address this need.

The [ExecuteQueryMultiple](/operation/executequerymultiple) is a raw method that would allow you to pass your own SQL statement for execution, whereas the [QueryMultiple](/operation/querymultiple) is a fluent-method that would allow you to pass a Linq-based query expression and have the library automatically compose the SQL statement for you.

The underlying implementation of this feature is abstracting both the `Read()` and `NextResult()` methods of the the `DbDataReader` object.

## Type of Return Types

The the method [ExecuteQueryMultiple](/operation/executequerymultiple) is returning an instance of [QueryMultipleExtractor](/class/querymultipleextractor). It allows you to control and manage the way on how to extract the resultsets. The execution is differed as it is relying on the explicit calls you are making towards the `Extract()` and `Scalar()` methods.

However, method [QueryMultiple](/operation/querymultiple) is returning an instance of `Tuple` object. It has a maximum of 7 generic types, also defined as max types for the `Tuple` object. The pointer to the item properties of the `Tuple` object is dependent to the order of the generic types passed during the call.

## Hints

The hints were provided as part of the execution. For [ExecuteQueryMultiple](/operation/executequerymultiple) method, you can write your own hints as you are the one composing the SQL statement. For the [QueryMultiple](/class/sqlservertablehints) method, each order of execution has an equivalent `hints` argument that you can use.

```csharp
var tuple = connection.QueryMultiple<Customer, Order>(c => c.Id == customerId, // Customer
    o => o.CustomerId == customerId, // Order
    hints1: "WITH (NOLOCK)", // Hints for Customer
    hints2: "WITH (NOLOCK)"); // Hints for Order
```

To simplify the usage, use the [SqlServerTableHints](/class/sqlservertablehints) class.

```csharp
var tuple = connection.QueryMultiple<Customer, Order>(c => c.Id == customerId, // Customer
    o => o.CustomerId == customerId, // Order
    hints1: SqlServerTableHints.NoLock, // Hints for Customer
    hints2: SqlServerTableHints.NoLock); // Hints for Order
```

## Ordering

The result ordering is available via [OrderField](/class/orderfield) class as part of the execution. For [ExecuteQueryMultiple](/operation/executequerymultiple) method, you can write your own ordering during the SQL statement composition. For the [QueryMultiple](/class/sqlservertablehints) method, each order of execution has equivalent `orderBy` argument that you can use.

```csharp
var tuple = connection.QueryMultiple<Customer, Order>(c => c.Id == customerId, // Customer
    o => o.CustomerId == customerId, // Order
    orderBy1: OrderField.Ascending<Customer>(c => c.DateInsertedUtc), // Ordering for Customer
    orderBy2: OrderField.Ascending<Order>(o => o.OrderDateUtc)); // Ordering for Order
```

## Filtering

The result filtering is available by simply passing the number of rows during the execution. For [ExecuteQueryMultiple](/operation/executequerymultiple) method, you can write your `TOP` or `LIMIT` keyword during SQL statement composition. For the [QueryMultiple](/class/sqlservertablehints) method, each order of execution has equivalent `top` argument that you can use.

```csharp
var tuple = connection.QueryMultiple<Customer, Order>(c => c.Id == customerId, // Customer
    o => o.CustomerId == customerId, // Order
    top2: 10); // Filtering for Order
```
---

## Single Parent with Multiple Children

For raw-SQL, call the [ExecuteQueryMultiple](/operation/executequerymultiple) method.

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

For fluent-method, you can call the [QueryMultiple](/operation/querymultiple) method as below.

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

For raw-SQL, call the [ExecuteQueryMultiple](/operation/executequerymultiple) method.

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

For fluent-method, you can call the [QueryMultiple](/operation/querymultiple) method as below.

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

> You can as well visit our [Multiple Resultsets](/reference/multipleresultsets) reference implementation page for more details.

