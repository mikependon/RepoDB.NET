---
layout: default
sidebar: operations
title: "ExecuteScalar"
permalink: /operation/executescalar
tags: [repodb, tutorial, executescalar, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
parent: Operations
---

# ExecuteScalar

---

This method is used to execute a raw-SQL directly towards the database. It returns the value of the first-row's first-column from any kind of resultsets. This method supports all types of RDMBS data providers.

### Code Snippets

Below is a code that gets the server date time from the database.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var serverDateTime = connection.ExecuteScalar<DateTime>("SELECT GETUTCDATE();");
}
```

### Passing of Parameters

You can pass a parameter via the following objects.

- Dynamic
- ExpandoObject
- Dictionary&lt;string, object&gt;
- QueryField/QueryGroup

#### Dynamic

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var count = connection.ExecuteScalar<int>("SELECT COUNT(*) FROM [dbo].[Person] WHERE DateInsertedUtc <= @DateInsertedUtc;", new { DateInsertedUtc = DateTime.UtcNow });
}
```

#### ExpandoObject

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var param = new ExpandoObject() as IDictionary<string, object>;
    param.Add("DateInsertedUtc", DateTime.UtcNow);
    var count = connection.ExecuteScalar<int>("SELECT COUNT(*) FROM [dbo].[Person] WHERE DateInsertedUtc <= @DateInsertedUtc;", param);
}
```

#### Dictionary<string, object>

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

#### QueryField/QueryGroup

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
    )};
    var count = connection.ExecuteScalar<int>("SELECT COUNT(*) FROM [dbo].[Person] WHERE DateInsertedUtc <= @DateInsertedUtc;", param);
}
```

### Array Parameters (for the IN keyword)

You can pass an array of values if you are using the `IN` keyword.

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

> You can also use the types defined at the [Passing of Parameters](#passing-of-parameters) section when passing a parameter.

### Executing a StoredProcedure

There are 2 ways of executing a stored procedure. First, simply pass the name of the stored procedure and set the command type to `CommandType.StoredProcedure`.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var count = connection.ExecuteScalar<DateTime>("[dbo].[sp_GetServerDateTime]", commandType: CommandType.StoredProcedure);
}
```

Or, simply use the native SQL calls like below.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var count = connection.ExecuteScalar<DateTime>("EXEC [dbo].[sp_GetServerDateTime];");
}
```

> Notice in the second call, there is semi-colon at the end of the command text and the command type was not set.
