---
layout: default
sidebar: operations
title: "MergeAll"
permalink: /operation/mergeall
tags: [repodb, tutorial, mergeall]
parent: OPERATIONS
---

# MergeAll

---

This method inserts multiple data entity objects as new rows or updates existing rows in the table. It operates as an UPSERT and does not perform any deletion.

{: .important }
> Non-existing rows are inserted; existing rows are updated based on the given qualifiers.

## Use Case

When merging multiple rows, use this method instead of iterating individual merges. It generates multi-statement SQL that executes in a single round-trip, which is more performant and efficient.

The batch size can be adjusted to optimize for your specific scenario (e.g., number of columns, network latency, data type).

The execution is ACID — a transaction is created automatically if one is not provided.

{: .warning }
> When managing batch sizes manually, ensure the total parameter count does not exceed ADO.NET's limit of 2,100 parameters.

## Code Snippets

The following example defines a method that returns a list of `Person` models, then merges them into the `[dbo].[Person]` table.

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
    var mergedRows = connection.MergeAll(people);
}
```

By default, the primary column is used as the qualifier. To override, pass the qualifier fields in the `qualifiers` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = GetPeople();
    var mergedRows = connection.MergeAll(people,
        qualifiers: (p => new { p.Name, p.DateOfBirth }));
}
```

## Targeting a Table

To target a specific table, pass the literal table name.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = GetPeople();
    var mergedRows = connection.MergeAll<Person>("[dbo].[Person]",
        entities: people);
}
```

Or via dynamics (Anonymous Type, `Dictionary<string, object>`, [ExpandoObject](https://learn.microsoft.com/en-us/dotnet/api/system.dynamic.expandoobject?view=net-7.0)):

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = GetPeople();
    var mergedRows = connection.MergeAll("[dbo].[Person]",
        entities: people);
}
```

## Specific Columns

To merge only specific columns, pass the target fields in the `fields` argument.

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
    var mergedRows = connection.MergeAll<Person>(entities: people,
        fields: fields);
}
```

Or via dynamics:

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = GetPeople();
    var mergedRows = connection.MergeAll("[dbo].[Person]",
        entities: people,
        fields: Field.From("Id", "Name", "DateInsertedUtc"));
}
```

## Batch Size

Adjust the batch size via the `batchSize` argument. The default is `10` (`Constant.DefaultBatchOperationSize`).

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = GetPeople();
    var mergedRows = connection.MergeAll(people,
        batchSize: 30);
}
```

## Table Hints

Pass a table hint via the `hints` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var mergedRows = connection.MergeAll<Person>(person,
        hints: "WITH (TABLOCK)");
}
```

Or use the [SqlServerTableHints](/class/sqlservertablehints) class.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var mergedRows = connection.MergeAll<Person>(person,
        hints: SqlServerTableHints.TabLock);
}
```
