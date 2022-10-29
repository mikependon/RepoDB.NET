---
layout: default
sidebar: operations
title: "ExecuteNonQuery"
permalink: /operation/executenonquery
tags: [repodb, tutorial, executenonquery]
parent: OPERATIONS
---

# ExecuteNonQuery

---

This method is used to execute a raw-SQL directly towards the database. It returns the number of rows affected during the execution. This method supports all types of RDMBS data providers.

## Code Snippets

Below is a code that deletes all the rows from the `[dbo].[Person]` table from the database.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var affectedRows = connection.ExecuteNonQuery("DELETE FROM [dbo].[Person];");
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
    var param = new
    {
        IsEnabled = new SqlParameter("_", true),
        LastAccessDateUtc = new SqlParameter("_", DateTime.UtcNow.AddMonths(-6).Date)
    };
    var commandText = "UPDATE IsEnabled = @IsEnabled FROM [dbo].[Person] WHERE ([LastAccessDateUtc] = @LastAccessDateUtc);";
    var affectedRows = connection.ExecuteNonQuery(commandText, param);
}
```

**Note:** The name of the parameter is not required. The library is replacing it with the actual name of the property passed from the object.

## Anonymous Types

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var param = new
    {
        IsEnabled = true,
        LastAccessDateUtc = DateTime.UtcNow.AddMonths(-6).Date 
    };
    var commandText = "UPDATE IsEnabled = @IsEnabled FROM [dbo].[Person] WHERE ([LastAccessDateUtc] = @LastAccessDateUtc);";
    var affectedRows = connection.ExecuteNonQuery(commandText, param);
}
```

## ExpandoObject

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var param = new ExpandoObject() as IDictionary<string, object>;
    param.Add("IsEnabled", true);
    param.Add("LastAccessDateUtc", DateTime.UtcNow.AddMonths(-6).Date );
    var commandText = "UPDATE IsEnabled = @IsEnabled FROM [dbo].[Person] WHERE ([LastAccessDateUtc] = @LastAccessDateUtc);";
    var affectedRows = connection.ExecuteNonQuery(commandText, param);
}
```

## Dictionary<string, object>

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var param = new Dictionary<string, object>
    {
        { "IsEnabled", true },
        { "LastAccessDateUtc", DateTime.UtcNow.AddMonths(-6).Date }
    };
    var commandText = "UPDATE IsEnabled = @IsEnabled FROM [dbo].[Person] WHERE ([LastAccessDateUtc] = @LastAccessDateUtc);";
    var affectedRows = connection.ExecuteNonQuery(commandText, param);
}
```

## QueryField/QueryGroup

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var param = new []
    {
        new QueryField("IsEnabled", true),
        new QueryField("LastAccessDateUtc", DateTime.UtcNow.AddMonths(-6).Date)
    };
    var commandText = "UPDATE IsEnabled = @IsEnabled FROM [dbo].[Person] WHERE ([LastAccessDateUtc] = @LastAccessDateUtc);";
    var affectedRows = connection.ExecuteNonQuery(commandText, param);
}
```

Or via [QueryGroup](/class/querygroup).

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var param = new QueryGroup(new []
    {
        new QueryField("IsEnabled", true),
        new QueryField("LastAccessDateUtc", DateTime.UtcNow.AddMonths(-6).Date)
    });
    var commandText = "UPDATE IsEnabled = @IsEnabled FROM [dbo].[Person] WHERE ([LastAccessDateUtc] = @LastAccessDateUtc);";
    var affectedRows = connection.ExecuteNonQuery(commandText, param);
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
    var commandText = "DELETE FROM dbo].[Person] WHERE Id IN (@Keys);";
    var affectedRows = connection.ExecuteNonQuery(commandText, param);
}
```

> You can also use the types defined at the [Passing of Parameters](#passing-of-parameters) section when passing a parameter.

## Executing a Stored Procedure

There are 2 ways of executing a stored procedure. First, simply pass the name of the stored procedure and set the command type to `CommandType.StoredProcedure`.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var param = new { LastAccessDateUtc = DateTime.UtcNow.AddMonths(-6).Date };
    var affectedRows = connection.ExecuteNonQuery("[dbo].[sp_DisablePeopleState](@LastAccessDateUtc);",
        param, commandType: CommandType.StoredProcedure);
}
```

Or, simply use the native SQL calls like below.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var affectedRows = connection.ExecuteNonQuery("EXEC [dbo].[sp_DisablePeopleState](@LastAccessDateUtc);",
        new { LastAccessDateUtc = DateTime.UtcNow.AddMonths(-6).Date });
}
```

> Notice in the second call, there is semi-colon at the end of the command text and the command type was not set.
