---
layout: default
sidebar: operations
title: "Update"
permalink: /operation/update
tags: [repodb, tutorial, update]
parent: OPERATIONS
---

# Update

---

Updates an existing row in the table.

## Code Snippets

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
	var updatedRows = connection.Update<Person>(person);
}
```

{: .important }
> By default, the primary column is used as a qualifier. Override it by passing the primary key value in the `what` argument.

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
	/* var updatedRows = connection.Update<Person>(person, what: 10045); // Same as below */
	var updatedRows = connection.Update<Person>(person, 10045);
}
```

## Targeting a Table

Target a specific table by passing the table name directly.

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
	var updatedRows = connection.Update<Person>("[dbo].[Person]",
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
	var updatedRows = connection.Update("[dbo].[Person]",
		entity: person);
}
```

Or via `Dictionary<string, object>` or [ExpandoObject](https://learn.microsoft.com/en-us/dotnet/api/system.dynamic.expandoobject?view=net-7.0).

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
    var id = connection.Update("[dbo].[Customer]",
        entity: person);
}
```

## Specific Columns

Restrict the update to specific columns by passing the target fields in the `fields` argument.

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
	var id = connection.Update<Person>(entity: person,
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
	var id = connection.Update("[dbo].[Person]",
		entity: person,
        fields: Field.From("Name", "DateOfBirth", "DateInsertedUtc"));
}
```

## Table Hints

Pass table hints via the `hints` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var updatedRows = connection.Update<Person>(person,
		hints: "WITH (TABLOCK)");
}
```

Or use the [SqlServerTableHints](/class/sqlservertablehints) class.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var updatedRows = connection.Update<Person>(person,
		hints: SqlServerTableHints.TabLock);
}
```
