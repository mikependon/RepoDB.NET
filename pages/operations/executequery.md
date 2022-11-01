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

This method is used to execute a raw-SQL directly towards the database. It returns an [IEnumerable<T>](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.ienumerable-1?view=net-7.0) object. This method supports all types of RDMBS data providers.

## Code Snippets

Below is a code that query all the rows from the `[dbo].[Person]` table from the database.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = connection.ExecuteQuery<Person>("SELECT * FROM [dbo].[Person];");
}
```

Returning the enumerable of dynamic objects. The type of the dynamic object is an [ExpandoObject](https://learn.microsoft.com/en-us/dotnet/api/system.dynamic.expandoobject?view=net-7.0).

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var dynamicPeople = connection.ExecuteQuery("SELECT * FROM [dbo].[Person];");
}
```

Returning the enumerable of [ExpandoObject](https://learn.microsoft.com/en-us/dotnet/api/system.dynamic.expandoobject?view=net-7.0) objects.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = connection.ExecuteQuery<ExpandoObject>("SELECT * FROM [dbo].[Person];");
}
```

Returning the enumerable of [IDictionary<string, object>](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.idictionary-2?view=net-6.0) objects.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = connection.ExecuteQuery<IDictionary<string, object>>("SELECT * FROM [dbo].[Person];");
}
```

{: .note }
You can as well pass an anonymous type in the generic type `TResult` of the [ExecuteQuery](/operation/executequery) operation. This scenario is very important to the F# programming language.

## Typed Result

You can infer the result back to any .NET CLR Type.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var ids = connection.ExecuteQuery<long>("SELECT Id FROM [dbo].[Person];");
}
```

Or for the other types like [string](https://learn.microsoft.com/en-us/dotnet/api/system.string?view=net-7.0) and [System.DateTime](https://learn.microsoft.com/en-us/dotnet/api/system.datetime?view=net-7.0).

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var names = connection.ExecuteQuery<string>("SELECT Name FROM [dbo].[Person];");
    var dateOfBirth = connection.ExecuteQuery<DateTime>("SELECT DateOfBirth FROM [dbo].[Person] WHERE Name = 'John Doe';").FirstOrDefault();
}
```

And also for the `Enum` types.

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
The inferrence of the enumeration will work for string types (i.e: `NVARCHAR`, `TEXT`, etc) and non-string types (i.e: `SMALLINT`, `INT`, `BIGINT`, etc) column types.

## Table-Valued Parameters

To execute a Table-Valued Parameter (TVP), create a `DataTable` and set its name equals to the name of the User-Defined Type (UDT).

Please click here to follow the Microsoft [guidelines](https://docs.microsoft.com/en-us/dotnet/framework/data/adonet/sql/table-valued-parameters) on how to create a TVP and UDT and call it from C#/ADO.NET.

```csharp
var table = new DataTable();
table.TableName = "Name of the UDT";
// Create the 'table' columns/rows
```

Then, pass it as a value to your argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var tables = connection.ExecuteQuery<IdentityTable>("EXEC [sp_StoredProcedureName] @Table = @Table;",
        new { Table = table })?.AsList();
}
```

## Passing of Parameters

You can pass a parameter via the following objects.

- IDbDataParameter
- Anonymous Types
- ExpandoObject
- IDictionary&lt;string, object&gt;
- QueryField/QueryGroup

Please see the sample code snippets below.

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
The name of the parameter is not required. The library is replacing it with the actual name of the property passed from the object.

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

You can pass an array of values if you are using the `IN` keyword.

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
> You can also use the types defined at the [Passing of Parameters](#passing-of-parameters) section when passing a parameter.

## Executing a Stored Procedure

There are 2 ways of executing a stored procedure. First, simply pass the name of the stored procedure and set the command type to `CommandType.StoredProcedure`.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var person = connection.ExecuteQuery<Person>("[dbo].[sp_GetPerson]",
        new { Id = 10045 }, commandType: CommandType.StoredProcedure).FirstOrDefault();
}
```

Or, simply use the native SQL calls like below.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var person = connection.ExecuteQuery<Person>("EXEC [dbo].[sp_GetPerson](@Id);",
        new { Id = 10045 }).FirstOrDefault();
}
```

{: .note }
> Notice in the second call, there is semi-colon at the end of the command text and the command type was not set.
