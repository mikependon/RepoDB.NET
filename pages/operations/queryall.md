---
layout: default
sidebar: operations
title: "QueryAll"
permalink: /operation/queryall
tags: [repodb, tutorial, queryall]
parent: OPERATIONS
---

# QueryAll

---

This method queries all rows from a table.

## Code Snippets

The following example fetches all rows from the `[dbo].[Person]` table.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = connection.QueryAll<Person>();
}
```

## Targeting a Table

To target a specific table, pass the literal table name.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = connection.QueryAll<Person>("[dbo].[Person]");
}
```

Or via dynamics:

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = connection.QueryAll("[dbo].[Person]");
}
```

{: .note }
> The result is a list of dynamic objects of type [ExpandoObject](https://learn.microsoft.com/en-us/dotnet/api/system.dynamic.expandoobject?view=net-7.0).

## Specific Columns

To query specific columns, pass a list of fields in the `fields` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var fields = Field.Parse<Person>(e => new
    {
        e.Id,
        e.Name,
        e.DateOfBirth,
        e.DateInsertedUtc
    });
    var people = connection.QueryAll<Person>(fields: fields);
}
```

Or via dynamics:

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = connection.Query("[dbo].[Person]",
        fields: Field.From("Id", "Name", "DateOfBirth", "DateInsertedUtc"));
}
```

## Type Result

The result set can be inferred directly as a [string](https://learn.microsoft.com/en-us/dotnet/api/system.string?view=net-7.0) column.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var names = connection.QueryAll<string>(ClassMappedNameCache.Get<Person>(),
        fields: Field.From(nameof(Person.Name))).FirstOrDefault();
}
```

{: .note }
Type inference works for `string` but not for other non-class types (e.g., `long`, [int](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/integral-numeric-types), [System.DateTime](https://learn.microsoft.com/en-us/dotnet/api/system.datetime?view=net-7.0)), since `TEntity` is constrained to `class`. Use [ExecuteQuery](/operation/executequery) for those types.

## Table Hints

Pass a table hint via the `hints` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = connection.QueryAll<Person>(hints: "WITH (NOLOCK)");
}
```

Or use the [SqlServerTableHints](/class/sqlservertablehints) class.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = connection.QueryAll<Person>(hints: SqlServerTableHints.TabLock);
}
```

## Ordering the Results

Pass an array of `OrderField` objects in the `orderBy` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var orderBy = OrderField.Parse(new
    {
        LastName = Order.Descending,
        FirstName = Order.Ascending
    });
    var people = connection.QueryAll<Person>(orderBy: orderBy);
    // Process 'people' here
}
```

## Caching the Results

Pass a literal string key in the `cacheKey` argument to cache the results.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = connection.QueryAll<Person>(cacheKey: "CackeKey:AllPeople");
    // Process 'people' here
}
```

{: .note }
> The default cache expiration is 180 minutes. Override it by passing an integer value in the `cacheExpirationInMinutes` argument.
