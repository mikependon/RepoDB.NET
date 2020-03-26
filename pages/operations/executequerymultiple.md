---
layout: page
title: "ExecuteQueryMultiple (RepoDb)"
permalink: /operation/executequerymultiple
tags: [repodb, tutorial, executequerymultiple, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

## ExecuteQueryMultiple

This method is used to execute multiple raw-SQLs directly towards the database in one-go. It returns an object of [QueryMultipleExtractor](/class/querymultipleextractor). It gives you the controllabilities as a developer to manage the extraction of the resultsets. This method supports all types of RDMBS data providers.

#### Installation

To install, simply type the codes below in your Package Manager Console.

```csharp
> Install-Package RepoDb
```

Or visit our [installation](/tutorials/installation) page for more information.

> In this tutorial, we will use the SQL Server as the database and C# as the programming language.

#### Learnings

Below is a code that queries a parent person (from `[dbo].[Person]` table) and all its related historical addresses (from `[dbo].[Address]` table) from the database.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	using (var result = connection.ExecuteQueryMultiple("SELECT * FROM [dbo].[Person] WHERE [Id] = 10045; SELECT * FROM [dbo].[Address] WHERE PersonId = 10045;"))
	{
		var person = result.Extract<Person>().FirstOrDefault();
		var addresses = result.Extract<Address>().AsList();
	}
}
```

You can also get a single value by calling the `Scalar` method.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	using (var result = connection.ExecuteQueryMultiple("SELECT * FROM [dbo].[Person] WHERE [Id] = 10045; SELECT COUNT(*) AS AddressCount FROM [dbo].[Address] WHERE PersonId = 10045;"))
	{
		var person = result.Extract<Person>().FirstOrDefault();
		var addressCount = result.Scalar<int>();
	}
}
```

> The calls to `Extract()` and `Scalar()` varies on the order of the calls you have made at [QueryMultipleExtractor](/class/querymultipleextractor) object. It is uses the `DbDataReader.NextResult()` method to extract the rows in order.

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
	using (var result = connection.ExecuteQueryMultiple("SELECT * FROM [dbo].[Person] WHERE [Id] = @PersonId; SELECT * FROM [dbo].[Address] WHERE PersonId = @PersonId;", new { PersonId = 10045 }))
	{
		// Do more stuffs here
	}
}
```

##### ExpandoObject

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var param = new ExpandoObject() as IDictionary<string, object>;
	param.Add("PersonId", 10045);
	using (var result = connection.ExecuteQueryMultiple("SELECT * FROM [dbo].[Person] WHERE [Id] = @PersonId; SELECT * FROM [dbo].[Address] WHERE PersonId = @PersonId;", param))
	{
		// Do more stuffs here
	}
}
```

##### Dictionary

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var param = new Dictionary<string, object>
	{
		{ "PersonId", 10045 }
	};
	using (var result = connection.ExecuteQueryMultiple("SELECT * FROM [dbo].[Person] WHERE [Id] = @PersonId; SELECT * FROM [dbo].[Address] WHERE PersonId = @PersonId;", param))
	{
		// Do more stuffs here
	}
}
```

##### Query Objects

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var param = new []
	{
		new QueryField("PersonId", 10045)
	};
	using (var result = connection.ExecuteQueryMultiple("SELECT * FROM [dbo].[Person] WHERE [Id] = @PersonId; SELECT * FROM [dbo].[Address] WHERE PersonId = @PersonId;", param))
	{
		// Do more stuffs here
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
	using (var result = connection.ExecuteQueryMultiple("SELECT * FROM [dbo].[Person] WHERE [Id] IN (@Keys); SELECT * FROM [dbo].[Address] WHERE PersonId IN (@Keys);", param))
	{
		// Do more stuffs here
	}
}
```

> You can also use the types defined at the [Passing of Parameters](#passing-of-parameters) section when passing a parameter.

#### Executing a StoredProcedure

The calls to execute a stored procedure is by simply calling the `EXEC` command of the SQL Server. It can be combined together with other raw-SQL statements.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	using (var result = connection.ExecuteQueryMultiple("EXEC [dbo].[sp_GetPerson](@PersonId); SELECT * FROM [dbo].[Address] WHERE PersonId = @PersonId;", new { Id = 10045 }))
	{
		// Do more stuffs here
	}
}
```

#### Passing a Transaction

To pass a transaction on this method, simply create an instance of `IDbConnection` and pass it at the `transaction` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	using (var transaction = connection.BeginTransaction())
	{
		try
		{
			using (var result = connection.ExecuteQueryMultiple("SELECT * FROM [dbo].[Person] WHERE [Id] = @PersonId; SELECT * FROM [dbo].[Address] WHERE PersonId = @PersonId;", new { PersonId = 10045 }, transaction: transaction))
			{
				// Do more stuffs here
			}

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


