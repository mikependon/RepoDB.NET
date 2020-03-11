---
layout: page
title: "ExecuteNonQuery (RepoDb)"
permalink: /operations/executenonquery
tags: [repodb, tutorial, executenonquery, orm, hybrid-orm, sqlserver]
---

## ExecuteNonQuery

This method is used to execute a raw-SQL directly towards the database. It returns the number of rows affected during the execution.

This method supports all types of database data providers. The only requirement is that, user needs to provide the raw-SQL itself.

> In this tutorial, we will use the `SQL Server` as the database and `C#` as the programming language.

Below is a very simple codes that deletes all the records from the `[dbo].[Person]` table at the database.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var rowsDeleted = connection.ExecuteNonQuery("DELETE FROM [dbo].[Person];");
}
```

You can pass a parameter via *dynamic* object, like below.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var rowsDeleted = connection.ExecuteNonQuery("UPDATE IsEnabled = @IsEnabled FROM [dbo].[Person] WHERE ([LastAccessDateUtc] = @LastAccessDateUtc);",
		new { IsEnabled = true, LastAccessDateUtc = DateTime.UtcNow.AddMonths(-6).Date });
}
```

The *properties* of the generated *dynamic* object will automatically be mapped on the given *parameters* at the raw-SQL.

### Executing a StoredProcedure

There is 2 ways of executing a stored procedure. First, simply pass the name of the stored procedure and set the command type to `CommandType.StoredProcedure`.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var rowsDeleted = connection.ExecuteNonQuery("[dbo].[sp_DisablePeopleState](@LastAccessDateUtc);",
		new { LastAccessDateUtc = DateTime.UtcNow.AddMonths(-6).Date }, commandType: CommandType.StoredProcedure);
}
```

Or, simply using the native SQL calls like below.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var rowsDeleted = connection.ExecuteNonQuery("EXEC [dbo].[sp_DisablePeopleState](@LastAccessDateUtc);",
		new { LastAccessDateUtc = DateTime.UtcNow.AddMonths(-6).Date });
}
```

> Notice in the second calls, there is semi-colon at the end of the command text and the command type was not set.

### Passing a Transaction

To pass a transaction on this method, simply create an instance of `IDbConnection` and pass it at the `transaction` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	using (var transaction = connection.BeginTransaction())
	{
		try
		{
			var rowsDeleted = connection.ExecuteNonQuery("EXEC [dbo].[sp_DisablePeopleState](@LastAccessDateUtc);",
				new { LastAccessDateUtc = DateTime.UtcNow.AddMonths(-6).Date }, transaction: transaction);
			
			// Do more codes here

			transaction.Commit();
		}
		catch (Exception ex)
		{
			// Handles the 'ex' here
		}
	}
}
```

> Without calling the `Commit()` method will automatically rollback the transaction. On this case, at the *catch* section the transaction will be rolled-back immediately.


