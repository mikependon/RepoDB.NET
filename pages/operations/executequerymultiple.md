---
layout: navpage
sidebar: operations
title: "ExecuteQueryMultiple"
permalink: /operation/executequerymultiple
tags: [repodb, tutorial, executequerymultiple, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# ExecuteQueryMultiple

This method is used to execute multiple raw-SQL Statements directly towards the database (in one-go). It returns an object of [QueryMultipleExtractor](/class/querymultipleextractor). This method supports all types of RDMBS data providers.

#### Code Snippets

Below is a code that queries a parent person row from the `[dbo].[Person]` table and all its related historical addresses from the `[dbo].[Address]` table.

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

You can also get a single value by calling the `Scalar` method.

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

> The calls to the `Extract()` and `Scalar()` methods varry on the order of the calls you have made at the [QueryMultipleExtractor](/class/querymultipleextractor) object. Underneath, it is uses the `DbDataReader.NextResult()` method to extract the rows in order.

#### Passing of Parameters

You can pass a parameter via the following objects.

- Dynamic
- ExpandoObject
- Dictionary&lt;string, object&gt;
- QueryField/QueryGroup

###### Dynamic

```csharp
using (var connection = new SqlConnection(connectionString))
{
    using (var result = connection.ExecuteQueryMultiple("SELECT * FROM [dbo].[Person] WHERE [Id] = @PersonId; SELECT * FROM [dbo].[Address] WHERE PersonId = @PersonId;", new { PersonId = 10045 }))
    {
        // Do more stuffs here
    }
}
```

###### ExpandoObject

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var param = new ExpandoObject() as IDictionary<string, object>;
    param.Add("PersonId", 10045);
    using (var result = connection.ExecuteQueryMultiple("SELECT * FROM [dbo].[Person] WHERE [Id] = @PersonId; SELECT * FROM [dbo].[Address] WHERE PersonId = @PersonId;", param))
    {
        // Do more stuffs here
    }
}
```

###### Dictionary<string, object>

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var param = new Dictionary<string, object>
    {
        { "PersonId", 10045 }
    };
    using (var result = connection.ExecuteQueryMultiple("SELECT * FROM [dbo].[Person] WHERE [Id] = @PersonId; SELECT * FROM [dbo].[Address] WHERE PersonId = @PersonId;", param))
    {
        // Do more stuffs here
    }
}
```

###### QueryField/QueryGroup

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var param = new []
    {
        new QueryField("PersonId", 10045)
    };
    using (var result = connection.ExecuteQueryMultiple("SELECT * FROM [dbo].[Person] WHERE [Id] = @PersonId; SELECT * FROM [dbo].[Address] WHERE PersonId = @PersonId;", param))
    {
        // Do more stuffs here
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
        // Do more stuffs here
    }
}
```

#### Array Parameters (for the IN keyword)

You can pass an array of values if you are using the `IN` keyword.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var param = new
    {
        Keys = new [] { 10045, 10102, 11004 }
    };
    using (var result = connection.ExecuteQueryMultiple("SELECT * FROM [dbo].[Person] WHERE [Id] IN (@Keys); SELECT * FROM [dbo].[Address] WHERE PersonId IN (@Keys);", param))
    {
        // Do more stuffs here
    }
}
```

> You can also use the types defined at the [Passing of Parameters](#passing-of-parameters) section when passing a parameter.

#### Executing a StoredProcedure

The calls to execute a stored procedure is by simply calling the `EXEC` command of the SQL Server. It can be combined together with other raw-SQL statements.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    using (var result = connection.ExecuteQueryMultiple("EXEC [dbo].[sp_GetPerson](@PersonId); SELECT * FROM [dbo].[Address] WHERE PersonId = @PersonId;", new { Id = 10045 }))
    {
        // Do more stuffs here
    }
}
```
