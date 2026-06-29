---
layout: default
sidebar: operations
title: "Query"
permalink: /operation/query
tags: [repodb, tutorial, query]
parent: OPERATIONS
---

# Query

---

This method queries rows from a table.

## Code Snippets

The following example fetches a row from the `[dbo].[Person]` table by primary key.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var person = connection.Query<Person>(10045).FirstOrDefault();
}
```

Query via expression:

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var person = connection.Query<Person>(e => e.Id == 10045).FirstOrDefault();
}
```

Or with compound conditions:

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var person = connection.Query<Person>(
        e => e.FirstName == "John" && e.LastName == "Doe").FirstOrDefault();
}
```

{: .note }
> The method always returns an [IEnumerable](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.ienumerable-1?view=net-7.0), even when the result is a single row.

## Targeting a Table

To target a specific table, pass the literal table name.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var person = connection.Query<Person>("[dbo].[Person]",
        10045).FirstOrDefault();
}
```

Or via dynamics:

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var person = connection.Query("[dbo].[Person]",
        10045).FirstOrDefault();
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
    var person = connection.Query<Person>(e => e.Id == 10045,
        fields: fields).FirstOrDefault();
}
```

Or via dynamics:

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var person = connection.Query("[dbo].[Person]",
        new { Id = 10045 },
        fields: Field.From("Id", "Name", "DateOfBirth", "DateInsertedUtc")).FirstOrDefault();
}
```

## Type Result

The result set can be inferred directly as a [string](https://learn.microsoft.com/en-us/dotnet/api/system.string?view=net-7.0) type.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var names = connection.Query<string>(ClassMappedNameCache.Get<Person>(),
        new QueryField("Name", Operation.Like, "%Anders%"),
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
    var person = connection.Query<Person>(10045,
        hints: "WITH (NOLOCK)").FirstOrDefault();
}
```

Or use the [SqlServerTableHints](/class/sqlservertablehints) class.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var person = connection.Query<Person>(10045,
        hints: SqlServerTableHints.TabLock).FirstOrDefault();
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
    var people = connection.Query<Person>(e => e.IsActive == true,
        orderBy: orderBy);
    // Process 'people' here
}
```

## Filtering the Results

Pass a value in the `top` argument to limit the number of rows returned.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = connection.Query<Person>(e => e.IsActive == true,
        top: 100);
    // Process 'people' here
}
```

## Caching the Results

Pass a literal string key in the `cacheKey` argument to cache the results.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = connection.Query<Person>(e => e.IsActive == true,
        cacheKey: "CackeKey:ActivePeople");
    // Process 'people' here
}
```

{: .note }
> The default cache expiration is 180 minutes. Override it by passing an integer value in the `cacheExpirationInMinutes` argument.
