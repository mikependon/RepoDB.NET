---
layout: default
sidebar: operations
title: "InsertAll"
permalink: /operation/insertall
tags: [repodb, tutorial, insertall]
parent: OPERATIONS
---

# InsertAll

---

This method inserts multiple data entity objects as new rows in the table.

## Use Case

When inserting multiple rows, use this method instead of iterating individual inserts. It generates multi-statement SQL that executes in a single round-trip, which is more performant and efficient.

The batch size can be adjusted to optimize for your specific scenario (e.g., number of columns, network latency).

The execution is ACID — a transaction is created automatically if one is not provided.

{: .warning }
> When managing batch sizes manually, ensure the total parameter count does not exceed ADO.NET's limit of 2,100 parameters.

## Code Snippets

The following example defines a method that returns a list of `Person` models, then inserts them into the `[dbo].[Person]` table.

```csharp
private IEnumerable<Person> GetPeople()
{
    var people = new List<Person>();
    people.Add(new Person
    {
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
    var insertedRows = connection.InsertAll(people);
}
```

## Targeting a Table

To target a specific table, pass the literal table name.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = GetPeople();
    var insertedRows = connection.InsertAll<Person>("[dbo].[Person]",
        entities: people);
}
```

Or via dynamics (Anonymous Type, `Dictionary<string, object>`, [ExpandoObject](https://learn.microsoft.com/en-us/dotnet/api/system.dynamic.expandoobject?view=net-7.0)):

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = GetPeople();
    var insertedRows = connection.InsertAll("[dbo].[Person]",
        entities: people);
}
```

## Specific Columns

To insert only specific columns, pass the target fields in the `fields` argument.

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
    var insertedRows = connection.InsertAll(entities: people,
        fields: fields);
}
```

Or via dynamics:

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = GetPeople();
    var insertedRows = connection.InsertAll("[dbo].[Person]",
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
    var insertedRows = connection.InsertAll(people,
        batchSize: 30);
}
```

## Table Hints

Pass a table hint via the `hints` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var insertedRows = connection.InsertAll<Person>(person,
        hints: "WITH (TABLOCK)");
}
```

Or use the [SqlServerTableHints](/class/sqlservertablehints) class.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var insertedRows = connection.InsertAll<Person>(person,
        hints: SqlServerTableHints.TabLock);
}
```
