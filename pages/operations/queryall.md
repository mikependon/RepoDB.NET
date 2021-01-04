---
layout: default
sidebar: operations
title: "QueryAll"
permalink: /operation/queryall
tags: [repodb, tutorial, queryall, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
parent: OPERATIONS
---

# QueryAll

---

This method is used to query all the rows from the table.

### Code Snippets

Below is the sample code to fetch a row from the `[dbo].[Person]` table.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = connection.QueryAll<Person>();
}
```

### Targeting a Table

You can also target a specific table by passing the literal table like below.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = connection.QueryAll<Person>("[dbo].[Person]");
}
```

Or via dynamics.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = connection.QueryAll("[dbo].[Person]");
}
```

> The result is a list of dynamic objects of type `ExpandoObject`.

### Specific Columns

You can also target a specific columns to be queried by passing the list of fields to be included in the `fields` argument.

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

Or via dynamics.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = connection.Query("[dbo].[Person]",
        fields: Field.From("Id", "Name", "DateOfBirth", "DateInsertedUtc"));
}
```

### Type Result

You can also directly target a `string` column as a result set.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var names = connection.QueryAll<string>(ClassMappedNameCache.Get<Person>(),
        fields: Field.From(nameof(Person.Name))).FirstOrDefault();
}
```

**Note:** Inferrence works in all types but not from this operation. The other non-class type (i.e.: `long`, `int`, `System.DateTime`, etc) cannot be inferred as the `TEntity` generic type is filtered as `class`. Please see the [ExecuteQuery](/operation/executequery) operation for the support to the other types.

### Table Hints

To pass a hint, simply write the table-hints and pass it in the `hints` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = connection.QueryAll<Person>(hints: "WITH (NOLOCK)");
}
```

Or, you can use the [SqlServerTableHints](/class/sqlservertablehints) class.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = connection.QueryAll<Person>(hints: SqlServerTableHints.TabLock);
}
```

### Ordering the Results

To order the results, you have to pass an array of `OrderField` objects in the `orderBy` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var orderBy = OrderField.Parse(new
    {
        LastName = Order.Descending,
        FirstName = Order.Ascending
    });
    var people = connection.QueryAll<Person>(orderBy: orderBy);
    // Do the stuffs for 'people' here
}
```

### Caching the Results

To cache the results, simply pass a literal string key into the `cacheKey` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = connection.QueryAll<Person>(cacheKey: "CackeKey:AllPeople");
    // Do the stuffs for 'people' here
}
```

> The cache expiration is defaulted to 180 minutes. You can override it by passing an integer value at the `cacheExpirationInMinutes` argument.
