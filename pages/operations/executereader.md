---
layout: default
sidebar: operations
title: "ExecuteReader"
permalink: /operation/executereader
tags: [repodb, tutorial, executereader]
parent: OPERATIONS
---

# ExecuteReader

---

This method executes a raw SQL statement directly against the database and returns a [DbDataReader](https://learn.microsoft.com/en-us/dotnet/api/system.data.common.dbdatareader?view=net-6.0) object. It supports all RDBMS data providers.

## Code Snippets

The following example reads all rows from the `[dbo].[Person]` table.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    using (var reader = connection.ExecuteReader("SELECT * FROM [dbo].[Person];"))
    {
        // Process the reader here
    }
}
```

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
    using (var reader = connection.ExecuteReader("SELECT * FROM [dbo].[Person] WHERE Id = @Id;", new { Id = new SqlParameter("_", 10045) }))
    {
        // Process the reader here
    }
}
```

{: .important }
The parameter name is not required. The library replaces it with the actual property name from the object.

## Anonymous Types

```csharp
using (var connection = new SqlConnection(connectionString))
{
    using (var reader = connection.ExecuteReader("SELECT * FROM [dbo].[Person] WHERE Id = @Id;", new { Id = 10045 }))
    {
        // Process the reader here
    }
}
```

## ExpandoObject

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var param = new ExpandoObject() as IDictionary<string, object>;
    param.Add("Id", 10045);
    using (var reader = connection.ExecuteReader("SELECT * FROM [dbo].[Person] WHERE Id = @Id;", param))
    {
        // Process the reader here
    }
}
```

## Dictionary<string, object>

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var param = new Dictionary<string, object>
    {
        { "Id", 10045 }
    };
    using (var reader = connection.ExecuteReader("SELECT * FROM [dbo].[Person] WHERE Id = @Id;", param))
    {
        // Process the reader here
    }
}
```

## QueryField/QueryGroup

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var param = new []
    {
        new QueryField("Id", 10045)
    };
    using (var reader = connection.ExecuteReader("SELECT * FROM [dbo].[Person] WHERE Id = @Id;", param))
    {
        // Process the reader here
    }
}
```

Or via [QueryGroup](/class/querygroup).

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var param = new QueryGroup(new []
    {
        new QueryField("Id", 10045)
    });
    using (var reader = connection.ExecuteReader("SELECT * FROM [dbo].[Person] WHERE Id = @Id;", param))
    {
        // Process the reader here
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
    using (var reader = connection.ExecuteReader("SELECT * FROM dbo].[Person] WHERE Id IN (@Keys);", param))
    {
        // Process the reader here
    }
}
```

{: .note }
> Any of the parameter types listed in [Passing of Parameters](#passing-of-parameters) can also be used here.

## Executing a Stored Procedure

There are two ways to execute a stored procedure. Pass the stored procedure name and set the command type to `CommandType.StoredProcedure`:

```csharp
using (var connection = new SqlConnection(connectionString))
{
    using (var reader = connection.ExecuteReader("[dbo].[sp_GetPerson]", new { Id = 10045 }, commandType: CommandType.StoredProcedure))
    {
        // Process the reader here
    }
}
```

Or use a native SQL `EXEC` call:

```csharp
using (var connection = new SqlConnection(connectionString))
{
    using (var reader = connection.ExecuteReader("EXEC [dbo].[sp_GetPerson](@Id);", new { Id = 10045 }))
    {
        // Process the reader here
    }
}
```

{: .note }
> In the second call, the command text ends with a semicolon and no command type is set.
