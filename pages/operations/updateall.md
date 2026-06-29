---
layout: default
sidebar: operations
title: "UpdateAll"
permalink: /operation/updateall
tags: [repodb, tutorial, updateall]
parent: OPERATIONS
---

# UpdateAll

---

Updates multiple existing rows in the table in a single call.

## Use Case

When updating multiple rows, avoid row-by-row iteration. This method generates a batched SQL statement that executes in a single round-trip, making it significantly more performant than atomic updates.

Adjust the batch size to optimize for your scenario (number of columns, network latency, data types, etc.).

The operation is ACID-compliant — a transaction is created automatically if one is not provided.

{: .warning }
> Be aware that large batch sizes may exceed ADO.NET's maximum parameter limit of 2100.

## Code Snippets

```csharp
private IEnumerable<Person> GetPeople()
{
    var people = new List<Person>();
    people.Add(new Person
    {
        Id = 10045,
        Name = "John Doe",
        Address = "New York",
        DateOfBirth = DateTime.Parse("2020-01-01"),
        IsActive = true,
        DateInsertedUtc = DateTime.UtcNow
    });
    people.Add(new Person
    {
        ...
    });
    people.Add(new Person
    {
        ...
    });
    return people;
}
```

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = GetPeople();
    var updatedRows = connection.UpdateAll(people);
}
```

{: .important }
> By default, the primary column is used as a qualifier. Override it by passing the qualifier fields in the `qualifiers` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = GetPeople();
    var updatedRows = connection.UpdateAll<Person>(people,
        qualifiers: (p => new { p.Name, p.DateOfBirth }));
}
```

## Targeting a Table

Target a specific table by passing the table name directly.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = GetPeople();
    var updatedRows = connection.UpdateAll<Person>("[dbo].[Person]",
        entities: people);
}
```

Or via dynamics (Anonymous Type, `Dictionary<string, object>`, [ExpandoObject](https://learn.microsoft.com/en-us/dotnet/api/system.dynamic.expandoobject?view=net-7.0)).

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = GetPeople();
    var updatedRows = connection.UpdateAll("[dbo].[Person]",
        entities: people);
}
```

## Specific Columns

Restrict the update to specific columns by passing the target fields in the `fields` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = GetPeople();
    var fields = Field.Parse<Person>(e => new
    {
        e.Id,
        e.Name,
        e.DateInsertedUtc
    });
    var updatedRows = connection.UpdateAll<Person>(entities: people,
        fields: fields);
}
```

Or via dynamics.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = GetPeople();
    var updatedRows = connection.UpdateAll("[dbo].[Person]",
        entities: people,
        fields: Field.From("Id", "Name", "DateInsertedUtc"));
}
```

## Batch Size

Control the batch size via the `batchSize` argument. The default is `10` (defined by `Constant.DefaultBatchOperationSize`).

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = GetPeople();
    var updatedRows = connection.UpdateAll(people,
        batchSize: 30);
}
```

## Table Hints

Pass table hints via the `hints` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var updatedRows = connection.UpdateAll<Person>(person,
        hints: "WITH (TABLOCK)");
}
```

Or use the [SqlServerTableHints](/class/sqlservertablehints) class.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var updatedRows = connection.UpdateAll<Person>(person,
        hints: SqlServerTableHints.TabLock);
}
```
