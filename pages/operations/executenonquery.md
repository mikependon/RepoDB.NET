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

This method executes a raw SQL statement directly against the database and returns the number of rows affected. It supports all RDBMS data providers.

## Code Snippets

The following example deletes all rows from the `[dbo].[Person]` table.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var affectedRows = connection.ExecuteNonQuery("DELETE FROM [dbo].[Person];");
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
    var param = new
    {
        IsEnabled = new SqlParameter("_", true),
        LastAccessDateUtc = new SqlParameter("_", DateTime.UtcNow.AddMonths(-6).Date)
    };
    var commandText = "UPDATE IsEnabled = @IsEnabled FROM [dbo].[Person] WHERE ([LastAccessDateUtc] = @LastAccessDateUtc);";
    var affectedRows = connection.ExecuteNonQuery(commandText, param);
}
```

{: .note }
The parameter name is not required. The library replaces it with the actual property name from the object.

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

Pass an array of values when using the `IN` keyword.

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

{: .note }
Any of the parameter types listed in [Passing of Parameters](#passing-of-parameters) can also be used here.

## Executing a Stored Procedure

There are two ways to execute a stored procedure. Pass the stored procedure name and set the command type to `CommandType.StoredProcedure`:

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var param = new { LastAccessDateUtc = DateTime.UtcNow.AddMonths(-6).Date };
    var affectedRows = connection.ExecuteNonQuery("[dbo].[sp_DisablePeopleState](@LastAccessDateUtc);",
        param, commandType: CommandType.StoredProcedure);
}
```

Or use a native SQL `EXEC` call:

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var affectedRows = connection.ExecuteNonQuery("EXEC [dbo].[sp_DisablePeopleState](@LastAccessDateUtc);",
        new { LastAccessDateUtc = DateTime.UtcNow.AddMonths(-6).Date });
}
```

{: .note }
In the second call, the command text ends with a semicolon and no command type is set.
