---
layout: default
sidebar: features
title: "Hints"
description: "This is a feature that would allow you to optimize the command executions towards the database. It is a keyword that is usually being added into the SQL statements (DML) to further optimize the execution of your commands."
permalink: /feature/hints
tags: [repodb, hints]
parent: FEATURES
---

# Hints

---

This feature allows you to optimize command execution against the database by embedding hint keywords into SQL DML statements. See the Microsoft documentation [here](https://docs.microsoft.com/en-us/sql/t-sql/queries/hints-transact-sql-table?view=sql-server-ver15).

## Raw-SQL

For raw SQL, hints are included directly in the SQL text you provide.

The following example queries dirty data from the `[dbo].[Order]` table:

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var orders = connection.ExecuteQuery<Order>("SELECT * FROM [dbo].[Order] WITH (NOLOCK);");
}
```

The following example acquires a table lock on `[dbo].[Person]` during an insert:

```csharp
var person = new
{
    Name = "John Doe",
    BirthDay = DateTime.Parse("1970-01-01"),
    IsActive = true
};
using (var connection = new SqlConnection(connectionString))
{
    var affectedRows = connection.ExecuteNonQuery("INSERT INTO [dbo].[Person] WITH (TABLOCK) ([Name], [DateOfBirth], [IsActive], [CreatedDateUtc]) VALUES (@Name, @BirthDay, @IsActive, GETUTCDATE());");
}
```

## Fluent-Methods

Most [operations](/docs#operations) accept a `hints` argument that accepts a literal string, giving full control over query optimization.

The following are the fluent-method equivalents of the raw SQL examples above:

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var orders = connection.QueryAll<Order>(hints: "WITH (NOLOCK)");
}
```

Acquiring a table lock during an insert:

```csharp
var person = new
{
    Name = "John Doe",
    BirthDay = DateTime.Parse("1970-01-01"),
    IsActive = true
};
using (var connection = new SqlConnection(connectionString))
{
    var id = connection.Insert<Person>(person, hints: "WITH (TABLOCK)");
}
```

The [SqlServerTableHints](/class/sqlservertablehints) class can also be used to pass hints:

```csharp
var person = new
{
    Name = "John Doe",
    BirthDay = DateTime.Parse("1970-01-01"),
    IsActive = true
};
using (var connection = new SqlConnection(connectionString))
{
    var id = connection.Insert<Person>(person, hints: SqlServerTableHints.TabLock);
}
```

{: .warning }
> The `hints` argument is only supported for [SQL Server](https://www.nuget.org/packages/RepoDb.SqlServer). Passing hints for other RDBMS providers will throw an exception.
