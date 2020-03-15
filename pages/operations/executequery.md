---
layout: page
title: "ExecuteQuery (RepoDb)"
permalink: /operation/executequery
tags: [repodb, tutorial, executequery, orm, hybrid-orm, sqlserver]
---

## ExecuteQuery

This method is used to execute a raw-SQL directly towards the database. It returns an `IEnumerable<T>` of objects.

This method supports all types of database data providers. The only requirement is that, user needs to provide the raw-SQL itself.

> In this tutorial, we will use the `SQL Server` as the database and `C#` as the programming language.

#### Installation

To install, simply type the codes below in your Package Manager Console.

```csharp
> Install-Package RepoDb
```

#### Learnings

Below is a very simple codes that query all the rows from the `[dbo].[Person]` table from the database.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var people = connection.ExecuteQuery<Person>("DELETE FROM [dbo].[Person];");
}
```

#### Passing of Parameters

You can pass a parameter via the following objects.
- Dynamic
- ExpandoObject
- Dictionary<string, object>
- Query Objects

##### Dynamic

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var person = connection.ExecuteQuery<Person>("SELECT * FROM [dbo].[Person] WHERE Id = @Id;", new { Id = 10045 }).FirstOrDefault();
}
```

##### ExpandoObject

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var param = new ExpandoObject() as IDictionary<string, object>;
	param.Add("Id", 10045);
	var person = connection.ExecuteQuery<Person>("SELECT * FROM [dbo].[Person] WHERE Id = @Id;", param).FirstOrDefault();
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
	var person = connection.ExecuteQuery<Person>("SELECT * FROM [dbo].[Person] WHERE Id = @Id;", param).FirstOrDefault();
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
	var person = connection.ExecuteQuery<Person>("SELECT * FROM [dbo].[Person] WHERE Id = @Id;", param).FirstOrDefault();
}
```

#### Array Parameters (for the IN keyword)

You can pass an array of values if you are using an `IN` keyword.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var param = new
	{
		Keys = new [] { 10045, 10102, 11004 }
	};
	var people = connection.ExecuteQuery<Person>("SELECT * FROM dbo].[Person] WHERE Id IN (@Keys);", param);
}
```

> You can also use the types defined at the *Passing of Parameters* section when passing a parameter.

#### Executing a StoredProcedure

There are 2 ways of executing a stored procedure. First, simply pass the name of the stored procedure and set the command type to `CommandType.StoredProcedure`.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var person = connection.ExecuteQuery<Person>("[dbo].[sp_GetPerson]",
		new { Id = 10045 }, commandType: CommandType.StoredProcedure).FirstOrDefault();
}
```

Or, simply using the native SQL calls like below.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var person = connection.ExecuteQuery<Person>("EXEC [dbo].[sp_GetPerson](@Id);", new { Id = 10045 }).FirstOrDefault();
}
```

> Notice in the second calls, there is semi-colon at the end of the command text and the command type was not set.

#### Passing a Transaction

To pass a transaction on this method, simply create an instance of `IDbConnection` and pass it at the `transaction` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	using (var transaction = connection.BeginTransaction())
	{
		try
		{
			var people = connection.ExecuteQuery<Person>("SELECT * FROM [dbo].[Person];", transaction: transaction);
			
			people
				.AsList()
				.ForEach(p => ProcessPerson(p));

			transaction.Commit();
		}
		catch (Exception ex)
		{
			// Handles the 'ex' here
		}
	}
}
```

> To commit the transaction, you have to explicitly call the `Commit()` method.


