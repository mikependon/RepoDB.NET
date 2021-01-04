---
layout: default
sidebar: operations
title: "Merge"
permalink: /operation/merge
tags: [repodb, tutorial, merge, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
parent: OPERATIONS
---

# Merge

---

This method is used to insert a new row or update an existing row from the table. This operation only works like UPSERT, it does not do any deletion.

### Code Snippets

Below is the sample code to merge a row into the `[dbo].[Person]` table.

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

By default, the primary or identity column is used as a qualifier. You can override it by simply passing the list of fields in the `qualifiers` argument.

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

> The result is always the value of primary key. If the primary key is an identity and a new record has been inserted, then the newly generated identity value will be returned.

### Targeting a Table

You can also target a specific table by passing the literal table like below.

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

Or via Anonymous Type.

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

Or via `Dictionary<string, object>` or `ExpandoObject`.

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

> Please note that the library will add the newly created value of the identity column into the `Dictionary<string, object>` or `ExpandoObject` object (if not present).

### Specific Columns

You can also target a specific columns to be merged by passing the list of fields to be included in the `fields` argument.

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

Or via dynamics.

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

### Table Hints

To pass a hint, simply write the table-hints and pass it in the `hints` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var id = connection.Merge<Person>(person,
        hints: "WITH (TABLOCK)");
}
```

Or, you can use the [SqlServerTableHints](/class/sqlservertablehints) class.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var id = connection.Merge<Person>(person,
        hints: SqlServerTableHints.TabLock);
}
```
