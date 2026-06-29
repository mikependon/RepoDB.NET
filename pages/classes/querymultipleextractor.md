---
layout: default
sidebar: classes
title: "QueryMultipleExtractor"
description: "A class that is being used to extract the information of the multiple resultsets returned by the ExecuteQueryMultiple operation."
permalink: /class/querymultipleextractor
tags: [repodb, querymultipleextractor]
parent: CLASSES
---

# QueryMultipleExtractor

---

The result object of the [ExecuteQueryMultiple](/operation/executequerymultiple) operation. It provides fine-grained control over how results are extracted from the underlying [DbDataReader](https://learn.microsoft.com/en-us/dotnet/api/system.data.common.dbdatareader?view=net-6.0).

Internally, it wraps the [DbDataReader](https://learn.microsoft.com/en-us/dotnet/api/system.data.common.dbdatareader?view=net-6.0), [IDbConnection](https://learn.microsoft.com/en-us/dotnet/api/system.data.idbconnection?view=net-7.0), and the `IDbTransaction` (if present), and advances the reader pointer by calling `DbDataReader.NextResult()` on each extraction call.

## Methods

| Name | Description |
|:-----|:------------|
| Extract | Extracts the current resultset into a typed object. |
| Scalar | Returns the first column of the current resultset. |

{: .note }
> `Scalar()` accepts a generic type argument. Each call to either method advances the reader to the next resultset.

## Usability

Assign the result of [ExecuteQueryMultiple](/operation/executequerymultiple) to a variable and extract results using the `Extract()` method.

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

Or using `Scalar()`.

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

Or a combination of both.

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

{: .note }
> Always wrap the result in a `using` statement to ensure proper disposal and avoid memory leaks.
