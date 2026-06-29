---
layout: default
sidebar: operations
title: "Merge"
permalink: /operation/merge
tags: [repodb, tutorial, merge]
parent: OPERATIONS
---

# Merge

---

This method inserts a new row or updates an existing row in the table. It operates as an UPSERT and does not perform any deletion.

## Code Snippets

The following example merges a row into the `[dbo].[Person]` table.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var person = new Person
    {
        Id = 10045,
        Name = "John Doe",
        Address = "New York",
        DateOfBirth = DateTime.Parse("2020-01-01"),
        IsActive = true,
        DateInsertedUtc = DateTime.UtcNow
    };
    var id = connection.Merge<Person>(person);
}
```

By default, the primary column is used as the qualifier. To override, pass the qualifier fields in the `qualifiers` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var person = new Person
    {
        Id = 10045,
        Name = "John Doe",
        Address = "New York",
        DateOfBirth = DateTime.Parse("2020-01-01"),
        IsActive = true,
        DateInsertedUtc = DateTime.UtcNow
    };
    var id = connection.Merge<Person>(person,
    qualifiers: (p => new { p.Name, p.DateOfBirth }));
}
```

{: .important }
> The result varies based on the `GlobalConfiguration.Options` property [KeyColumnReturnBehavior](/enumeration/keycolumnreturnbehavior).

## Targeting a Table

To target a specific table, pass the literal table name.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var person = new Person
    {
        Id = 10045,
        Name = "John Doe",
        Address = "New York",
        DateOfBirth = DateTime.Parse("2020-01-01"),
        IsActive = true,
        DateInsertedUtc = DateTime.UtcNow
    };
    var id = connection.Merge<Person>("[dbo].[Person]",
        entity: person);
}
```

Or via Anonymous Type:

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var person = new
    {
        Id = 10045,
        Name = "John Doe",
        Address = "New York",
        DateOfBirth = DateTime.Parse("2020-01-01"),
        IsActive = true,
        DateInsertedUtc = DateTime.UtcNow
    };
    var id = connection.Merge("[dbo].[Person]",
        entity: person);
}
```

Or via `Dictionary<string, object>` or [ExpandoObject](https://learn.microsoft.com/en-us/dotnet/api/system.dynamic.expandoobject?view=net-7.0):

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var person = new Dictionary<string, object>
    {
        { "Id", 10045 },
        { "Name", "John Doe" },
        { "Address", "New York" },
        { "DateOfBirth", DateTime.Parse("2020-01-01") },
        { "IsActive", true },
        { "CreatedDateUtc", DateTime.UtcNow }
    };
    var id = connection.Merge("[dbo].[Customer]",
        entity: person);
}
```

{: .note }
> The library adds the newly generated identity value into the `Dictionary<string, object>` or [ExpandoObject](https://learn.microsoft.com/en-us/dotnet/api/system.dynamic.expandoobject?view=net-7.0) object if not already present.

## Specific Columns

To merge only specific columns, pass the target fields in the `fields` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var person = new Person
    {
        Id = 10045,
        Name = "John Doe",
        Address = "New York",
        DateOfBirth = DateTime.Parse("2020-01-01"),
        IsActive = true,
        DateInsertedUtc = DateTime.UtcNow
    };
    var fields = Field.Parse<Person>(e => new
    {
        e.Name,
        e.DateOfBirth,
        e.DateInsertedUtc
    })
    var id = connection.Merge<Person>(entity: person,
        fields: fields);
}
```

Or via dynamics:

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var person = new
    {
        Id = 10045,
        Name = "John Doe",
        Address = "New York",
        DateOfBirth = DateTime.Parse("2020-01-01"),
        IsActive = true,
        DateInsertedUtc = DateTime.UtcNow
    };
    var id = connection.Merge("[dbo].[Person]",
        entity: person,
        fields: Field.From("Name", "DateOfBirth", "DateInsertedUtc"));
}
```

## Table Hints

Pass a table hint via the `hints` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var id = connection.Merge<Person>(person,
        hints: "WITH (TABLOCK)");
}
```

Or use the [SqlServerTableHints](/class/sqlservertablehints) class.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var id = connection.Merge<Person>(person,
        hints: SqlServerTableHints.TabLock);
}
```
