---
layout: default
sidebar: operations
title: "ExecuteScalar"
permalink: /operation/executescalar
tags: [repodb, tutorial, executescalar]
parent: OPERATIONS
---

# ExecuteScalar

---

This method executes a raw SQL statement directly against the database and returns the value of the first column of the first row from the result set. It supports all RDBMS data providers.

## Code Snippets

The following example retrieves the server UTC date and time.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var serverDateTime = connection.ExecuteScalar<DateTime>("SELECT GETUTCDATE();");
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
    var count = connection.ExecuteScalar<int>("SELECT COUNT(*) FROM [dbo].[Person] WHERE DateInsertedUtc <= @DateInsertedUtc;", new { DateInsertedUtc = new SqlParameter("_", DateTime.UtcNow) });
}
```

{: .important }
The parameter name is not required. The library replaces it with the actual property name from the object.

## Anonymous Types

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var count = connection.ExecuteScalar<int>("SELECT COUNT(*) FROM [dbo].[Person] WHERE DateInsertedUtc <= @DateInsertedUtc;", new { DateInsertedUtc = DateTime.UtcNow });
}
```

## ExpandoObject

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var param = new ExpandoObject() as IDictionary<string, object>;
    param.Add("DateInsertedUtc", DateTime.UtcNow);
    var count = connection.ExecuteScalar<int>("SELECT COUNT(*) FROM [dbo].[Person] WHERE DateInsertedUtc <= @DateInsertedUtc;", param);
}
```

## Dictionary<string, object>

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var param = new Dictionary<string, object>
    {
        { "DateInsertedUtc", DateTime.UtcNow }
    };
    var count = connection.ExecuteScalar<int>("SELECT COUNT(*) FROM [dbo].[Person] WHERE DateInsertedUtc <= @DateInsertedUtc;", param);
}
```

## QueryField/QueryGroup

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var param = new []
    {
        new QueryField("DateInsertedUtc", DateTime.UtcNow)
    };
    var count = connection.ExecuteScalar<int>("SELECT COUNT(*) FROM [dbo].[Person] WHERE DateInsertedUtc <= @DateInsertedUtc;", param);
}
```

Or via [QueryGroup](/class/querygroup).

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var param = new QueryGroup(new []
    {
        new QueryField("DateInsertedUtc", DateTime.UtcNow)
    });
    var count = connection.ExecuteScalar<int>("SELECT COUNT(*) FROM [dbo].[Person] WHERE DateInsertedUtc <= @DateInsertedUtc;", param);
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
    var count = connection.ExecuteScalar<double>("SELECT SUM(NetWorth) FROM [dbo].[Person] WHERE Id IN (@Keys);", param);
}
```

{: .note }
> Any of the parameter types listed in [Passing of Parameters](#passing-of-parameters) can also be used here.

## Executing a Stored Procedure

There are two ways to execute a stored procedure. Pass the stored procedure name and set the command type to `CommandType.StoredProcedure`:

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var count = connection.ExecuteScalar<DateTime>("[dbo].[sp_GetServerDateTime]", commandType: CommandType.StoredProcedure);
}
```

Or use a native SQL `EXEC` call:

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var count = connection.ExecuteScalar<DateTime>("EXEC [dbo].[sp_GetServerDateTime];");
}
```

{: .note }
> In the second call, the command text ends with a semicolon and no command type is set.
