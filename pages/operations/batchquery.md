---
layout: default
sidebar: operations
title: "BatchQuery"
permalink: /operation/batchquery
tags: [repodb, tutorial, batchquery]
parent: OPERATIONS
---

# BatchQuery

---

This method queries rows from the database in batches.

## Code Snippets

The following example queries the first batch of 20 active rows from the `[dbo].[Person]` table, ordered by creation date.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var orderBy = OrderField.Parse(new { DateInsertedUtc = Order.Descending });
    var page = 0; // Starts at 0 for the first batch
    var rowsPerBatch = 20;
    var people = connection.BatchQuery<Person>(page: page,
        rowsPerBatch: rowsPerBatch,
        orderBy: orderBy,
        where: e => e.IsActive == true);
}
```

{: .warning }
> Paging starts at 0.

The following example queries the third batch.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var orderBy = OrderField.Parse(new { DateInsertedUtc = Order.Descending });
    var page = 2;// This is the 3rd batch
    var rowsPerBatch = 20;
    var people = connection.BatchQuery<Person>(page: page,
        rowsPerBatch: rowsPerBatch,
        orderBy: orderBy,
        where: e => e.IsActive == true);
}
```

## Targeting a Table

To target a specific table, pass the literal table name.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var orderBy = OrderField.Parse(new { DateInsertedUtc = Order.Descending });
    var page = 0;
    var rowsPerBatch = 20;
    var people = connection.BatchQuery<Person>("Person",
        page: page,
        rowsPerBatch: rowsPerBatch,
        orderBy: orderBy,
        where: e => e.IsActive == true);
}
```

Or via dynamics.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var orderBy = OrderField.Parse(new { DateInsertedUtc = Order.Descending });
    var page = 0;
    var rowsPerBatch = 20;
    var people = connection.BatchQuery("Person",
        page: page,
        rowsPerBatch: rowsPerBatch,
        orderBy: orderBy,
        where: new { IsActive = true });
}
```

## Specific Columns

To query specific columns, pass a list of fields.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var orderBy = OrderField.Parse(new { DateInsertedUtc = Order.Descending });
    var page = 0;
    var rowsPerBatch = 20;
    var fields = Field.Parse<Person>(e => new
    {
        e.Id,
        e.Name,
        e.DateInsertedUtc
    });
    var people = connection.BatchQuery<Person>(page: page,
        rowsPerBatch: rowsPerBatch,
        orderBy: orderBy,
        where: e => e.IsActive == true,
        fields: fields);
}
```

Or via dynamics.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var orderBy = OrderField.Parse(new { DateInsertedUtc = Order.Descending });
    var page = 0;
    var rowsPerBatch = 20;
    var people = connection.BatchQuery("Person",
        page: page,
        rowsPerBatch: rowsPerBatch,
        orderBy: orderBy,
        where: new { IsActive = true },
        fields: Field.From("Id", "Name", "DateInsertedUtc"));
}
```

## Filtering the Results

Use [QueryGroup](/class/querygroup) or [QueryField](/class/queryfield) to build more complex WHERE expressions.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var where = new []
    {
        new QueryField("IsActive", true),
        new QueryField("DateInsertedUtc", Operation.GreaterThanOrEqual, DateTime.UtcNow.Date.AddDays(-1))
    };
    var orderBy = OrderField.Parse(new { DateInsertedUtc = Order.Descending })
    var page = 0;
    var rowsPerBatch = 20;
    var people = connection.BatchQuery("Person",
        page: page,
        rowsPerBatch: rowsPerBatch,
        orderBy: orderBy,
        where: where);
}
```

## Table Hints

Pass a table hint via the `hints` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var orderBy = OrderField.Parse(new { DateInsertedUtc = Order.Descending });
    var page = 0;
    var rowsPerBatch = 20;
    var people = connection.BatchQuery<Person>(page: page,
        rowsPerBatch: rowsPerBatch,
        orderBy: orderBy,
        where: e => e.IsActive == true,
        hints: "WITH (NOLOCK)");
}
```

Or use the [SqlServerTableHints](/class/sqlservertablehints) class.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var orderBy = OrderField.Parse(new { DateInsertedUtc = Order.Descending });
    var page = 0;
    var rowsPerBatch = 20;
    var people = connection.BatchQuery<Person>(page: page,
        rowsPerBatch: rowsPerBatch,
        orderBy: orderBy,
        where: e => e.IsActive == true,
        hints: SqlServerTableHints.NoLock);
}
```
