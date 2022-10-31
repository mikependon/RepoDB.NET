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

This method is used to execute a raw-SQL directly towards the database. It returns an instance of `DbDataReader` object. This method supports all types of RDMBS data providers.

## Code Snippets

Below is a code that reads all the rows from the `[dbo].[Person]` table from the database.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    using (var reader = connection.ExecuteReader("SELECT * FROM [dbo].[Person];"))
    {
        // Do more stuffs for the reader here
    }
}
```

## Passing of Parameters

You can pass a parameter via the following objects.

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
        // Do more stuffs for the reader here
    }
}
```

{: .important }
The name of the parameter is not required. The library is replacing it with the actual name of the property passed from the object.

## Anonymous Types

```csharp
using (var connection = new SqlConnection(connectionString))
{
    using (var reader = connection.ExecuteReader("SELECT * FROM [dbo].[Person] WHERE Id = @Id;", new { Id = 10045 }))
    {
        // Do more stuffs for the reader here
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
        // Do more stuffs for the reader here
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
        // Do more stuffs for the reader here
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
        // Do more stuffs for the reader here
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
        // Do more stuffs for the reader here
    }
}
```

## Array Parameters (for the IN keyword)

You can pass an array of values if you are using the `IN` keyword.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var param = new
    {
        Keys = new [] { 10045, 10102, 11004 }
    };
    using (var reader = connection.ExecuteReader("SELECT * FROM dbo].[Person] WHERE Id IN (@Keys);", param))
    {
        // Do more stuffs for the reader here
    }
}
```

{: .note }
> You can also use the types defined at the [Passing of Parameters](#passing-of-parameters) section when passing a parameter.

## Executing a Stored Procedure

There are 2 ways of executing a stored procedure. First, simply pass the name of the stored procedure and set the command type to `CommandType.StoredProcedure`.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    using (var reader = connection.ExecuteReader("[dbo].[sp_GetPerson]", new { Id = 10045 }, commandType: CommandType.StoredProcedure))
    {
        // Do more stuffs for the reader here
    }
}
```

Or, simply use the native SQL calls like below.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    using (var reader = connection.ExecuteReader("EXEC [dbo].[sp_GetPerson](@Id);", new { Id = 10045 }))
    {
        // Do more stuffs for the reader here
    }
}
```

{: .note }
> Notice in the second call, there is semi-colon at the end of the command text and the command type was not set.
