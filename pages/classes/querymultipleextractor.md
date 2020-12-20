---
layout: navpage
sidebar: classes
title: "QueryMultipleExtractor"
description: "A class that is used to extract the information of the multiple resultsets returned by the ExecuteQueryMultiple operation."
permalink: /class/querymultipleextractor
tags: [repodb, class, querymultipleextractor, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# QueryMultipleExtractor

This class is a result object of the [ExecuteQueryMultiple](/operation/executequerymultiple) operation. It offers you a much more controllability on how to extract the results from the `DbDataReader`.

Internally, it is abstracting the instance of `DbDataReader`, `IDbConnection` and `IDbTransaction` (if present).

It is also managing how the pointer of the `DbDataReader` object when you (as the developer) is calling its method. Underneath to this, it calls the `DbDataReader.NextResult()` method.

#### Methods

These are the methods available on this class.

- `Extract` - a generic based method that will extract the contents of the `DbDataReader` into a class object.
- `Scalar` - a method that is used to get the first column of the result.

> When using the `Scalar()` method, you can pass a generic type as a type of the result. Also, when calling any of the mentioned above, the pointer of the `DbDataReader` is advances to the next result.

#### How to use?

You need to handle the result of [ExecuteQueryMultiple](/operation/executequerymultiple) into a variable and manage the extraction via `Extrac()` method.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var commandText = "SELECT Id, Name FROM [dbo].[Customer] WHERE Id = @CustomerId; SELECT * FROM [dbo].[Order] WHERE CustomerId = @CustomerId;";

    // Ensure to wrap with 'using' to auto-dispose
    using (var extractor = connection.ExecuteQueryMultiple(commandText, new { CustomerId = 10045 }))
    {
        // We know that the first query is for 'Customer' and second query is for 'Order'
        var customer = extractor.Extract<Customer>().FirstOrDefault();
        var orders = extractor.Extract<Order>().AsList();

        // Do the stuffs for the 'customer' and 'orders' here
    }
}
```

Or, using the `Scalar()` method.


```csharp
using (var connection = new SqlConnection(connectionString))
{
    var commandText = "SELECT COUNT(*) FROM [dbo].[Customer]; SELECT GETUTCDATE();";

    // Ensure to wrap with 'using' to auto-dispose
    using (var extractor = connection.ExecuteQueryMultiple(commandText, new { CustomerId = 10045 }))
    {
        // We know that the first query is type 'int' and second query is type 'DateTime'
        var customerCount = extractor.Scalar<int>()
        var serverDate = extractor.Scalar<DateTime>();

        // Do the stuffs for the 'customerCount' and 'serverDate' here
    }
}
```

Or by combination.


```csharp
using (var connection = new SqlConnection(connectionString))
{
    var commandText = "SELECT Id, Name FROM [dbo].[Customer] WHERE Id = @CustomerId; SELECT COUNT(*) FROM [dbo].[Order] WHERE CustomerId = @CustomerId;";

    // Ensure to wrap with 'using' to auto-dispose
    using (var extractor = connection.ExecuteQueryMultiple(commandText, new { CustomerId = 10045 }))
    {
        // We know that the first query is for 'Customer' and second query is type 'int'
        var customer = extractor.Extract<Customer>().FirstOrDefault();
        var ordersCount = extractor.Scalar<int>();

        // Do the stuffs for the 'customer' and 'ordersCount' here
    }
}
```

> Please be ensure to always wrap the result with `using` keyword to auto-dispose the object. Otherwise, you may leak some memory pointers that is left unmanaged.