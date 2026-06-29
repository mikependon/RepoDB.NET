---
layout: default
sidebar: features
title: "Dynamics"
description: "This feature allows you to work with RepoDB by maximizing the usage of dynamics."
permalink: /feature/dynamics
tags: [repodb, dynamics]
parent: FEATURES
---

# Dynamics / Anonymous Types

---

This feature enables simplified, targeted operations by leveraging anonymous types. It is useful for performing CRUD operations on specific columns or without a class model.

This feature is referred to as DYNAMICS because it enables dynamic capabilities in the context of an ORM. This terminology is distinct from the dynamics concept within the .NET ecosystem.

## Querying a Data

Use the [Query](/operation/query) operation with the target table as a literal string and a filter expression as an anonymous object.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var customer = connection.Query("[dbo].[Customer]", 10045).FirstOrDefault();
}
```

Or filter by an anonymous type:

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var param = new
    {
        FirstName = "John",
        LastName = "Doe"
    };
    var customer = connection.Query("[dbo].[Customer]", param).FirstOrDefault();
}
```

Or return specific columns:

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var customer = connection.Query("[dbo].[Customer]",
        10045,
        Field.From("Id","FirstName", "LastName")).FirstOrDefault();
}
```

## Iterating the Result

Data fetched from the database is automatically converted into an enumerable of [ExpandoObject](https://learn.microsoft.com/en-us/dotnet/api/system.dynamic.expandoobject?view=net-7.0).

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var customers = connection.Query("[dbo].[Customer]", new { Country = "Denmark" },
        Field.From("Id","FirstName", "LastName"));

    foreach (var customer in customers)
    {
        ...
    }
}
```

## Inserting a Data

Use the [Insert](/operation/insert) operation with the target table as a literal string and an anonymous entity object.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var entity = new
    {
        FirstName = "John",
        LastName = "Doe",
        CreatedDateUtc = DateTime.UtcNow
    };
    var id = connection.Insert<int>("[dbo].[Customer]", entity);
}
```

Or via [Dictionary](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.dictionary-2?view=net-7.0) or [ExpandoObject](https://learn.microsoft.com/en-us/dotnet/api/system.dynamic.expandoobject?view=net-7.0):

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var entity = new Dictionary<string, object>
    {
        { "FirstName", "John" },
        { "LastName", "Doe" },
        { "CreatedDateUtc", DateTime.UtcNow }
    };
    var id = connection.Insert<int>("[dbo].[Customer]", entity);
}
```

{: .note }
> The library will add the newly created identity column value into the [Dictionary](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.dictionary-2?view=net-7.0) or [ExpandoObject](https://learn.microsoft.com/en-us/dotnet/api/system.dynamic.expandoobject?view=net-7.0) object if not already present.

## Deleting a Data

Use the [Delete](/operation/delete) operation with the target table as a literal string and a filter expression as an anonymous object.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var deletedRows = connection.Delete("[dbo].[Customer]", new { IsActive = false });
}
```

## Merging a Data

Use the [Merge](/operation/merge) operation with the target table as a literal string and an anonymous entity object.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var entity = new
    {
        Id = 10045,
        FirstName = "John",
        LastName = "Doe",
        Address = "New York",
        LastUpdatedDateUtc = DateTime.UtcNow
    };
    var updatedRows = connection.Merge("[dbo].[Customer]", entity);
}
```

## Updating a Data

Use the [Update](/operation/update) operation with the target table as a literal string and an anonymous entity object.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var entity = new
    {
        Id = 10045,
        FirstName = "James",
        LastUpdatedDateUtc = DateTime.UtcNow
    };
    var updatedRows = connection.Update("[dbo].[Customer]", entity);
}
```

{: .note }
> In general, specific columns can always be targeted during [Query](/operation/query), [Insert](/operation/insert), [Merge](/operation/merge), or [Update](/operation/update) operations.
