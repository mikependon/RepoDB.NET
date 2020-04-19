---
layout: navpage
sidebar: features
title: "Dynamics (RepoDb)"
permalink: /feature/dynamics
tags: [repodb, class, dynamics, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# Dynamics

This feature allows you to create a simplified and targetted operations by simply maximizing the usage of dynamics.

It is very useful if you wish to do CRUD for targetted columns or CRUD operations without a class models.

> In this tutorial, we will use the SQL Server as the database and C# as the programming language.

#### Querying a Data

Use the [Query](/operation/query) operation and pass the target table as a literal string and a filter expression as a dynamic object.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
    var customer = connection.Query("[dbo].[Customer]", 10045).FirstOrDefault();
}
```

Or via dynamics.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
    var customer = connection.Query("[dbo].[Customer]", new { FirstName = "John", "Doe" }).FirstOrDefault();
}
```

Or with specific columns.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
    var customer = connection.Query("[dbo].[Customer]", 10045, Field.From("Id","FirstName", "LastName")).FirstOrDefault();
}
```

#### Inserting a Data

Use the [Insert](/operation/insert) operation and pass the target table as a literal string and a dynamic entity object.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
    var entity = new
    {
        FirstName = "John",
        LastName = "Doe",
        CreatedDateUtc = DateTime.UtcNow
    }
    var id = connection.Insert<int>("[dbo].[Customer]", entity);
}
```

#### Deleting a Data

Use the [Delete](/operation/delete) operation and pass the target table as a literal string and a filter expression as a dynamic object.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
    var deletedRows = connection.Delete("[dbo].[Customer]", new { IsActive = false });
}
```

#### Merging a Data

Use the [Merge](/operation/merge) operation and pass the target table as a literal string and a dynamic entity object.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
    var entity = new
    {
        Id = 10045,
        FirstName = "John",
        LastName = "Doe",
        Address = "New York",
        LastUpdatedDateUtc = DateTime.UtcNow
    }
    var updatedRows = connection.Merge("[dbo].[Customer]", entity);
}
```

#### Updating a Data

Use the [Update](/operation/update) operation and pass the target table as a literal string and a dynamic entity object.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
    var entity = new
    {
        Id = 10045,
        FirstName = "James",
        LastUpdatedDateUtc = DateTime.UtcNow
    }
    var updatedRows = connection.Update("[dbo].[Customer]", entity);
}
```

> You can target specific columns during the [Query](/operation/query), [Insert](/operation/insert), [Merge](/operation/merge) or [Update](/operation/update) operations.
