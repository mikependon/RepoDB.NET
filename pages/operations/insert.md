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

This method inserts a data entity object as a new row in the table.

## Code Snippets

The following example inserts a row into the `[dbo].[Person]` table.

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

{: .important }
> The result varies based on the `GlobalConfiguration.Options` property [KeyColumnReturnBehavior](/enumeration/keycolumnreturnbehavior).

## Targeting a Table

To target a specific table, pass the literal table name.

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

Or via Anonymous Type:

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

Or via `Dictionary<string, object>` or [ExpandoObject](https://learn.microsoft.com/en-us/dotnet/api/system.dynamic.expandoobject?view=net-7.0):

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

{: .note }
> The library adds the newly generated identity value into the `Dictionary<string, object>` or [ExpandoObject](https://learn.microsoft.com/en-us/dotnet/api/system.dynamic.expandoobject?view=net-7.0) object if not already present.

## Specific Columns

To insert only specific columns, pass the target fields in the `fields` argument.

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

Or via dynamics:

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

With target fields:

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

## Table Hints

Pass a table hint via the `hints` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var id = connection.Insert<Person>(person,
        hints: "WITH (TABLOCK)");
}
```

Or use the [SqlServerTableHints](/class/sqlservertablehints) class.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var id = connection.Insert<Person>(person,
        hints: SqlServerTableHints.TabLock);
}
```
