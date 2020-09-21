---
layout: navpage
sidebar: operations
title: "ExecuteReader"
permalink: /operation/executereader
tags: [repodb, tutorial, executereader, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# ExecuteReader

This method is used to execute a raw-SQL directly towards the database. It returns an instance of `DbDataReader` object. This method supports all types of RDMBS data providers.

#### Code Snippets

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

#### Passing of Parameters

You can pass a parameter via the following objects.

- `Dynamic`
- `ExpandoObject`
- `Dictionary<string, object>`
- `Query Objects`

##### Dynamic

```csharp
using (var connection = new SqlConnection(connectionString))
{
	using (var reader = connection.ExecuteReader("SELECT * FROM [dbo].[Person] WHERE Id = @Id;", new { Id = 10045 }))
	{
		// Do more stuffs for the reader here
	}
}
```

##### ExpandoObject

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

##### Dictionary

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

##### Query Objects

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

#### Array Parameters (for the IN keyword)

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

> You can also use the types defined at the [Passing of Parameters](#passing-of-parameters) section when passing a parameter.

#### Executing a StoredProcedure

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

> Notice in the second calls, there is semi-colon at the end of the command text and the command type was not set.
