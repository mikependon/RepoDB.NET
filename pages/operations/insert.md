---
layout: default
sidebar: operations
title: "Insert"
permalink: /operation/insert
tags: [repodb, tutorial, insert]
parent: OPERATIONS
---

# Insert

---

This method is used to insert a data entity object as a new row in the table.

### Code Snippets

Below is the sample code to insert a row into the `[dbo].[Person]` table.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var id = connection.Insert<Person, int>(new Person
    {
        Name = "John Doe",
        Address = "New York",
        DateOfBirth = DateTime.Parse("2020-01-01"),
        IsActive = true,
        DateInsertedUtc = DateTime.UtcNow
    });
}
```

> The result is always the value of primary key. If the primary key is an identity, then the newly generated identity value will be returned.

### Targeting a Table

You can also target a specific table by passing the literal table like below.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var person = new Person
    {
        Name = "John Doe",
        Address = "New York",
        DateOfBirth = DateTime.Parse("2020-01-01"),
        IsActive = true,
        DateInsertedUtc = DateTime.UtcNow
    };
    var id = connection.Insert<Person>("[dbo].[Person]",
        entity: person);
}
```

Or via Anonymous Type.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var person = new
    {
        Name = "John Doe",
        Address = "New York",
        DateOfBirth = DateTime.Parse("2020-01-01"),
        IsActive = true,
        DateInsertedUtc = DateTime.UtcNow
    };
    var id = connection.Insert("[dbo].[Person]",
        entity: person);
}
```

Or via `Dictionary<string, object>` or `ExpandoObject`.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var person = new Dictionary<string, object>
    {
        { "Name", "John Doe" },
        { "Address", "New York" },
        { "DateOfBirth", DateTime.Parse("2020-01-01") },
        { "IsActive", true },
        { "CreatedDateUtc", DateTime.UtcNow }
    };
    var id = connection.Insert<int>("[dbo].[Customer]",
        entity: person);
}
```

> Please note that the library will add the newly created value of the identity column into the `Dictionary<string, object>` or `ExpandoObject` object (if not present).

### Specific Columns

You can also target a specific columns to be inserted by passing the list of fields to be included in the `fields` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var person = new Person
    {
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
    var id = connection.Insert<Person>(entity: person,
        fields: fields);
}
```

Or via dynamics.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var person = new
    {
        Name = "John Doe",
        DateOfBirth = DateTime.Parse("2020-01-01"),
        DateInsertedUtc = DateTime.UtcNow
    };
    var id = connection.Insert("[dbo].[Person]",
        entity: person));
}
```

And with the target fields.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var person = new
    {
        Name = "John Doe",
        Address = "New York",
        DateOfBirth = DateTime.Parse("2020-01-01"),
        IsActive = true,
        DateInsertedUtc = DateTime.UtcNow
    };
    var id = connection.Insert("[dbo].[Person]",
        entity: person,
        fields: Field.From("Name", "DateOfBirth", "DateInsertedUtc"));
}
```

### Table Hints

To pass a hint, simply write the table-hints and pass it in the `hints` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var id = connection.Insert<Person>(person,
        hints: "WITH (TABLOCK)");
}
```

Or, you can use the [SqlServerTableHints](/class/sqlservertablehints) class.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var id = connection.Insert<Person>(person,
        hints: SqlServerTableHints.TabLock);
}
```
