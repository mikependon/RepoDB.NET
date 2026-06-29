---
layout: default
sidebar: operations
title: "ExecuteQueryMultiple"
permalink: /operation/executequerymultiple
tags: [repodb, tutorial, executequerymultiple]
parent: OPERATIONS
---

# ExecuteQueryMultiple

---

This method executes multiple raw SQL statements against the database in a single round-trip and returns a [QueryMultipleExtractor](/class/querymultipleextractor) object. It supports all RDBMS data providers.

## Code Snippets

The following example queries a parent `Person` row and all related `Address` rows in one call.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    using (var result = connection.ExecuteQueryMultiple("SELECT * FROM [dbo].[Person] WHERE [Id] = 10045; SELECT * FROM [dbo].[Address] WHERE PersonId = 10045;"))
    {
        var person = result.Extract<Person>().FirstOrDefault();
        var addresses = result.Extract<Address>().AsList();
    }
}
```

To retrieve a scalar value, call the `Scalar()` method:

```csharp
using (var connection = new SqlConnection(connectionString))
{
    using (var result = connection.ExecuteQueryMultiple("SELECT * FROM [dbo].[Person] WHERE [Id] = 10045; SELECT COUNT(*) AS AddressCount FROM [dbo].[Address] WHERE PersonId = 10045;"))
    {
        var person = result.Extract<Person>().FirstOrDefault();
        var addressCount = result.Scalar<int>();
    }
}
```

{: .note }
> Calls to `Extract()` and `Scalar()` are order-dependent and correspond to the order of statements. Internally, `DbDataReader.NextResult()` advances the reader to each result set in sequence.

## Passing of Parameters

Parameters can be passed via any of the following types:

- IDbDataParameter
- Anonymous Types
- ExpandoObject
- Dictionary&lt;string, object&gt;
- QueryField/QueryGroup

## IDbDataParameter

```csharp
using (var connection = new SqlConnection(connectionString))
{
    using (var result = connection.ExecuteQueryMultiple("SELECT * FROM [dbo].[Person] WHERE [Id] = @PersonId; SELECT * FROM [dbo].[Address] WHERE PersonId = @PersonId;", new { PersonId = new SqlParameter("_", 10045) }))
    {
        // Process results here
    }
}
```

{: .important }
The parameter name is not required. The library replaces it with the actual property name from the object.

## Anonymous Types

```csharp
using (var connection = new SqlConnection(connectionString))
{
    using (var result = connection.ExecuteQueryMultiple("SELECT * FROM [dbo].[Person] WHERE [Id] = @PersonId; SELECT * FROM [dbo].[Address] WHERE PersonId = @PersonId;", new { PersonId = 10045 }))
    {
        // Process results here
    }
}
```

## ExpandoObject

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var param = new ExpandoObject() as IDictionary<string, object>;
    param.Add("PersonId", 10045);
    using (var result = connection.ExecuteQueryMultiple("SELECT * FROM [dbo].[Person] WHERE [Id] = @PersonId; SELECT * FROM [dbo].[Address] WHERE PersonId = @PersonId;", param))
    {
        // Process results here
    }
}
```

## Dictionary<string, object>

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var param = new Dictionary<string, object>
    {
        { "PersonId", 10045 }
    };
    using (var result = connection.ExecuteQueryMultiple("SELECT * FROM [dbo].[Person] WHERE [Id] = @PersonId; SELECT * FROM [dbo].[Address] WHERE PersonId = @PersonId;", param))
    {
        // Process results here
    }
}
```

## QueryField/QueryGroup

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var param = new []
    {
        new QueryField("PersonId", 10045)
    };
    using (var result = connection.ExecuteQueryMultiple("SELECT * FROM [dbo].[Person] WHERE [Id] = @PersonId; SELECT * FROM [dbo].[Address] WHERE PersonId = @PersonId;", param))
    {
        // Process results here
    }
}
```

Or via [QueryGroup](/class/querygroup).

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var param = new QueryGroup(new []
    {
        new QueryField("PersonId", 10045)
    });
    using (var result = connection.ExecuteQueryMultiple("SELECT * FROM [dbo].[Person] WHERE [Id] = @PersonId; SELECT * FROM [dbo].[Address] WHERE PersonId = @PersonId;", param))
    {
        // Process results here
    }
}
```

## Array Parameters (for the IN keyword)

Pass an array of values when using the `IN` keyword.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var param = new
    {
        Keys = new [] { 10045, 10102, 11004 }
    };
    using (var result = connection.ExecuteQueryMultiple("SELECT * FROM [dbo].[Person] WHERE [Id] IN (@Keys); SELECT * FROM [dbo].[Address] WHERE PersonId IN (@Keys);", param))
    {
        // Process results here
    }
}
```

{: .note }
> Any of the parameter types listed in [Passing of Parameters](#passing-of-parameters) can also be used here.

## Executing a Stored Procedure

Execute a stored procedure using the `EXEC` SQL command. It can be combined with other raw SQL statements.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    using (var result = connection.ExecuteQueryMultiple("EXEC [dbo].[sp_GetPerson](@PersonId); SELECT * FROM [dbo].[Address] WHERE PersonId = @PersonId;", new { Id = 10045 }))
    {
        // Process results here
    }
}
```
