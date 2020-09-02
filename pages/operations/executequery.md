---
layout: navpage
sidebar: operations
title: "ExecuteQuery"
permalink: /operation/executequery
tags: [repodb, tutorial, executequery, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# ExecuteQuery

This method is used to execute a raw-SQL directly towards the database. It returns an `IEnumerable<T>` object. This method supports all types of RDMBS data providers.

#### Code Snippets

Below is a code that queries all the rows from the `[dbo].[Person]` table from the database.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var people = connection.ExecuteQuery<Person>("SELECT * FROM [dbo].[Person];");
}
```

You can also execute a SQL statement that returns a dynamic object with properties equals to the selected fields of the SQL statement.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var dynamicPeople = connection.ExecuteQuery("SELECT * FROM [dbo].[Person];");
}
```

#### Passing of Parameters

You can pass a parameter via the following objects.

- `Dynamic`
- `ExpandoObject`
- `IDictionary<string, object>`
- `Query Objects`

##### Anonymous Types

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var person = connection.ExecuteQuery<Person>("SELECT * FROM [dbo].[Person] WHERE Id = @Id;", new { Id = 10045 }).FirstOrDefault();
}
```

##### ExpandoObject

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var param = new ExpandoObject() as IDictionary<string, object>;
	param.Add("Id", 10045);
	var person = connection.ExecuteQuery<Person>("SELECT * FROM [dbo].[Person] WHERE Id = @Id;", param).FirstOrDefault();
}
```

##### Dictionary

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var param = new Dictionary<string, object>
	{
		{ "Id", 10045 }
	};
	var person = connection.ExecuteQuery<Person>("SELECT * FROM [dbo].[Person] WHERE Id = @Id;", param).FirstOrDefault();
}
```

##### Query Objects

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var param = new []
	{
		new QueryField("Id", 10045)
	};
	var person = connection.ExecuteQuery<Person>("SELECT * FROM [dbo].[Person] WHERE Id = @Id;", param).FirstOrDefault();
}
```

#### Array Parameters (for the IN keyword)

You can pass an array of values if you are using the `IN` keyword.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var param = new
	{
		Keys = new [] { 10045, 10102, 11004 }
	};
	var people = connection.ExecuteQuery<Person>("SELECT * FROM dbo].[Person] WHERE Id IN (@Keys);", param);
}
```

> You can also use the types defined at the [Passing of Parameters](#passing-of-parameters) section when passing a parameter.

#### Executing a StoredProcedure

There are 2 ways of executing a stored procedure. First, simply pass the name of the stored procedure and set the command type to `CommandType.StoredProcedure`.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var person = connection.ExecuteQuery<Person>("[dbo].[sp_GetPerson]",
		new { Id = 10045 }, commandType: CommandType.StoredProcedure).FirstOrDefault();
}
```

Or, simply use the native SQL calls like below.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
	var person = connection.ExecuteQuery<Person>("EXEC [dbo].[sp_GetPerson](@Id);", new { Id = 10045 }).FirstOrDefault();
}
```

> Notice in the second calls, there is semi-colon at the end of the command text and the command type was not set.
