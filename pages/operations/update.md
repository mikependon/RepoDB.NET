---
layout: navpage
sidebar: operations
title: "Update"
permalink: /operation/update
tags: [repodb, tutorial, update, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# Update

This method is used to updates an existing row in the table.

#### Code Snippets

Below is a sample code to update a row into the `[dbo].[Person]` table.

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

By default, it uses the primary (or identity) field as the qualifier. You can override by simply passing the primary key in the `whereOrPrimaryKey` argument.

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
	/* var updatedRows = connection.Update<Person>(person, whereOrPrimaryKey: 10045); // Same as below */
	var updatedRows = connection.Update<Person>(person, 10045);
}
```

#### Targeting a Table

You can also target a specific table by passing the literal table like below.

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
	var updatedRows = connection.Update("[dbo].[Person]",
		entity: person);
}
```

#### Specific Columns

You can also target a specific columns to be updated by passing the list of fields to be included in the `fields` argument.

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

#### Table Hints

To pass a hint, simply write the table-hints and pass it in the `hints` argument.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var updatedRows = connection.Update<Person>(person,
		hints: "WITH (TABLOCK)");
}
```

Or, you can use the [SqlServerTableHints](/class/sqlservertablehints) class.

```csharp
using (var connection = new SqlConnection(connectionString))
{
	var updatedRows = connection.Update<Person>(person,
		hints: SqlServerTableHints.TabLock);
}
```
