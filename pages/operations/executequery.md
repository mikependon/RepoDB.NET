---
layout: default
sidebar: operations
title: "ExecuteQuery"
permalink: /operation/executequery
tags: [repodb, tutorial, executequery]
parent: OPERATIONS
---

# ExecuteQuery

---

This method executes a raw SQL statement directly against the database and returns an [IEnumerable](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.ienumerable-1?view=net-7.0) object. It supports all RDBMS data providers.

## Code Snippets

The following example queries all rows from the `[dbo].[Person]` table.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = connection.ExecuteQuery<Person>("SELECT * FROM [dbo].[Person];");
}
```

Returning an enumerable of dynamic objects (typed as [ExpandoObject](https://learn.microsoft.com/en-us/dotnet/api/system.dynamic.expandoobject?view=net-7.0)):

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var dynamicPeople = connection.ExecuteQuery("SELECT * FROM [dbo].[Person];");
}
```

Returning an enumerable of [ExpandoObject](https://learn.microsoft.com/en-us/dotnet/api/system.dynamic.expandoobject?view=net-7.0):

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = connection.ExecuteQuery<ExpandoObject>("SELECT * FROM [dbo].[Person];");
}
```

Returning an enumerable of [IDictionary<string, object>](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.idictionary-2?view=net-6.0):

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = connection.ExecuteQuery<IDictionary<string, object>>("SELECT * FROM [dbo].[Person];");
}
```

{: .note }
An anonymous type can also be passed as the generic type `TResult`. This is particularly useful in F#.

## Typed Result

The result can be inferred as any .NET CLR type.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var ids = connection.ExecuteQuery<long>("SELECT Id FROM [dbo].[Person];");
}
```

Or for other types such as [string](https://learn.microsoft.com/en-us/dotnet/api/system.string?view=net-7.0) and [System.DateTime](https://learn.microsoft.com/en-us/dotnet/api/system.datetime?view=net-7.0):

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var names = connection.ExecuteQuery<string>("SELECT Name FROM [dbo].[Person];");
    var dateOfBirth = connection.ExecuteQuery<DateTime>("SELECT DateOfBirth FROM [dbo].[Person] WHERE Name = 'John Doe';").FirstOrDefault();
}
```

`Enum` types are also supported:

```csharp
// Enumeration
public enum Gender
{
    Male = 1,
    Female = 2
}

// Inferring
using (var connection = new SqlConnection(connectionString))
{
    var gender = connection.ExecuteQuery<Gender>("SELECT Gender FROM [dbo].[Person] WHERE Name = 'John Doe';").FirstOrDefault();
}
```

{: .important }
Enumeration inference works for both string column types (e.g., `NVARCHAR`, `TEXT`) and non-string column types (e.g., `SMALLINT`, `INT`, `BIGINT`).

## Table-Valued Parameters

To execute a Table-Valued Parameter (TVP), create a `DataTable` and set its name to the name of the User-Defined Type (UDT).

Refer to the Microsoft [guidelines](https://docs.microsoft.com/en-us/dotnet/framework/data/adonet/sql/table-valued-parameters) for creating a TVP/UDT and calling it from C#/ADO.NET.

```csharp
var table = new DataTable();
table.TableName = "Name of the UDT";
// Create the 'table' columns/rows
```

Pass it as an argument value:

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var tables = connection.ExecuteQuery<IdentityTable>("EXEC [sp_StoredProcedureName] @Table = @Table;",
        new { Table = table })?.AsList();
}
```

## Passing of Parameters

Parameters can be passed via any of the following types:

- IDbDataParameter
- Anonymous Types
- ExpandoObject
- IDictionary&lt;string, object&gt;
- QueryField/QueryGroup

## IDbDataParameter

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var param = new
    {
        Id = new SqlParameter("_", 10045)
    };
    var person = connection.ExecuteQuery<Person>("SELECT * FROM [dbo].[Person] WHERE Id = @Id;", param).FirstOrDefault();
}
```

{: .important }
The parameter name is not required. The library replaces it with the actual property name from the object.

## Anonymous Types

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var param = new
    {
        Id = 10045
    };
    var person = connection.ExecuteQuery<Person>("SELECT * FROM [dbo].[Person] WHERE Id = @Id;", param).FirstOrDefault();
}
```

## ExpandoObject

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var param = new ExpandoObject() as IDictionary<string, object>;
    param.Add("Id", 10045);
    var person = connection.ExecuteQuery<Person>("SELECT * FROM [dbo].[Person] WHERE Id = @Id;", param).FirstOrDefault();
}
```

## IDictionary<string, object>

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var param = new Dictionary<string, object>
    {
        { "Id", 10045 }
    };
    var person = connection.ExecuteQuery<Person>("SELECT * FROM [dbo].[Person] WHERE Id = @Id;", param).FirstOrDefault();
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
    var person = connection.ExecuteQuery<Person>("SELECT * FROM [dbo].[Person] WHERE Id = @Id;", param).FirstOrDefault();
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
    var person = connection.ExecuteQuery<Person>("SELECT * FROM [dbo].[Person] WHERE Id = @Id;", param).FirstOrDefault();
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
    var people = connection.ExecuteQuery<Person>("SELECT * FROM [dbo].[Person] WHERE Id IN (@Keys);", param);
}
```

{: .note }
> Any of the parameter types listed in [Passing of Parameters](#passing-of-parameters) can also be used here.

## Executing a Stored Procedure

There are two ways to execute a stored procedure. Pass the stored procedure name and set the command type to `CommandType.StoredProcedure`:

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var person = connection.ExecuteQuery<Person>("[dbo].[sp_GetPerson]",
        new { Id = 10045 }, commandType: CommandType.StoredProcedure).FirstOrDefault();
}
```

Or use a native SQL `EXEC` call:

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var person = connection.ExecuteQuery<Person>("EXEC [dbo].[sp_GetPerson](@Id);",
        new { Id = 10045 }).FirstOrDefault();
}
```

{: .note }
> In the second call, the command text ends with a semicolon and no command type is set.
