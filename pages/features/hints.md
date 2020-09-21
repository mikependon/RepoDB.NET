---
layout: navpage
sidebar: features
title: "Hints"
description: "This is a feature that would allow you to optimize the command executions towards the database. It is a keyword that is usually being added into the SQL statements (DML) to further optimize the execution of your commands."
permalink: /feature/hints
tags: [repodb, class, hints, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# Hints

This is a feature that would allow you to optimize the command executions towards the database. It is a keyword that is usually being added into the SQL statements (DML) to further optimize the execution of your commands. Please the Microsoft document [here](https://docs.microsoft.com/en-us/sql/t-sql/queries/hints-transact-sql-table?view=sql-server-ver15).

#### Raw-SQL

For raw-SQL, the process of adding hints is controlled by you as you are the one who is providing the SQL text.

Below is the code that queries all the dirty transactions from the `[dbo].[Order]` table.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var orders = connection.ExecuteQuery<Order>("SELECT * FROM [dbo].[Order] WITH (NOLOCK);");
}
```

And below is the code that locks the table `[dbo].[Person]` when you insert a record.

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

#### Fluent-Methods

Most [fluent-methods](/docs#fluent-methods) within the library accepts the `hints` argument. It allows you to pass the hints as a literal string, those giving you the full control of your optimizations.

Below are the equivalent fluent-methods of the raw-SQLs execution defined in the previous section.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var orders = connection.Query<Order>(hints: "WITH (NOLOCK)");
}
```

And below is the code that locks the table when you insert a record.

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

You can also use the [SqlServerTableHints](/class/sqlservertablehints) class to pass the hints.

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

> The hints is only supporting the [SQL Server](https://www.nuget.org/packages/RepoDb.SqlServer).
