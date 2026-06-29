---
layout: default
sidebar: classes
title: "QueryBuilder"
description: "An advance fluent class that is being used to construct a SQL statements all throughout the library."
permalink: /class/querybuilder
tags: [repodb, querybuilder]
parent: CLASSES
---

# QueryBuilder

---

This class composes SQL statements fluently by appending SQL tokens based on method names.

{: .note }
> Some methods accept class or object arguments that are automatically converted to SQL text.

## Use-Cases

- Composing SQL text in [IStatementBuilder](/interface/istatementbuilder) implementations.
- Building SQL statements fluently in application code.

## Creating an Instance

```csharp
var querybuilder = new QueryBuilder();
```

## Composing a simple Query Statement

The following builds a simple `SELECT` statement for use with [ExecuteQuery](/operation/executequery).

```csharp
var dbSetting = DbSettingMapper.Get(typeof(SqlConnection)); // Get the setting for SqlConnection
var fields = FieldCache.Get<Person>(); // Get all the fields
var queryBuilder = new QueryBuilder();
var statement = queryBuilder
    .Clear()
    .Select()
    .FieldsFrom(fields, dbSetting) // With the setting, the field 'Name' will become '[Name]'
    .From()
    .TableNameFrom(tableName, dbSetting)
    .End()
    .GetString(); // This will append the ';' character at the end
```

The result:

```csharp
> SELECT [Id], [Name], [IsActive], [DateInsertedUtc] FROM [dbo].[Person];
```

Execute it as follows:

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = connection.ExecuteQuery<Person>(statement);
}
```

## Passing the QueryGroup

Use the `WhereFrom()` method to append a [QueryGroup](/class/querygroup).

```csharp
var dbSetting = DbSettingMapper.Get(typeof(SqlConnection));
var fields = FieldCache.Get<Person>();
var where = new QueryGroup(new QueryField("State", "Michigan"));
var queryBuilder = new QueryBuilder();
var statement = queryBuilder
    .Clear()
    .Select()
    .FieldsFrom(fields, dbSetting)
    .From()
    .TableNameFrom(tableName, dbSetting)
    .WhereFrom(where, dbSetting) // Call this method
    .End()
    .GetString();
```

The result:

```csharp
> SELECT [Id], [Name], [IsActive], [DateInsertedUtc] FROM [dbo].[Person] WHERE ([State] = @State);
```

## Passing the Row Limit

Use the `TopFrom()` method to limit rows.

```csharp
var dbSetting = DbSettingMapper.Get(typeof(SqlConnection));
var fields = FieldCache.Get<Person>();
var queryBuilder = new QueryBuilder();
var statement = queryBuilder
    .Clear()
    .Select()
    .TopFrom(10) // Call this method
    .FieldsFrom(fields, dbSetting)
    .From()
    .TableNameFrom(tableName, dbSetting)
    .End()
    .GetString();
```

The result:

```csharp
> SELECT TOP (10) [Id], [Name], [IsActive], [DateInsertedUtc] FROM [dbo].[Person];
```

## Ordering the Results

Use the `OrderByFrom()` method to sort results.

```csharp
var dbSetting = DbSettingMapper.Get(typeof(SqlConnection));
var orderBy = OrderField.Ascending<Person>(p => p.Name); // Create an OrderField
var fields = FieldCache.Get<Person>();
var queryBuilder = new QueryBuilder();
var statement = queryBuilder
    .Clear()
    .Select()
    .FieldsFrom(fields, dbSetting)
    .From()
    .TableNameFrom(tableName, dbSetting)
    .OrderByFrom(orderBy, dbSetting)  // Call this method
    .End()
    .GetString();
```

The result:

```csharp
> SELECT [Id], [Name], [IsActive], [DateInsertedUtc] FROM [dbo].[Person] ORDER BY [Name] ASC ;
```

## Passing a Hints

Use the `HintsFrom()` method to append table hints.

```csharp
var dbSetting = DbSettingMapper.Get(typeof(SqlConnection));
var fields = FieldCache.Get<Person>();
var queryBuilder = new QueryBuilder();
var statement = queryBuilder
    .Clear()
    .Select()
    .FieldsFrom(fields, dbSetting)
    .HintsFrom("WITH (NOLOCK)")  // Call this method
    .From()
    .TableNameFrom(tableName, dbSetting)
    .End()
    .GetString();
```

The result:

```csharp
> SELECT [Id], [Name], [IsActive], [DateInsertedUtc] FROM [dbo].[Person] WITH (NOLOCK) ;
```

## The WriteText Method

`WriteText` is a generic method for inserting arbitrary SQL text, useful for provider-specific syntax not covered by built-in methods.

The following example generates a CTE-based `SELECT` query.

```csharp
var dbSetting = DbSettingMapper.Get(typeof(SqlConnection));
var fields = FieldCache.Get<Person>();
var queryBuilder = new QueryBuilder();
var statement = queryBuilder
    .Clear()
    .WriteText("WITH CTE AS (")
    .Select()
    .FieldsFrom(fields, dbSetting)
    .HintsFrom("WITH (NOLOCK)")
    .From()
    .TableNameFrom(tableName, dbSetting)
    .WriteText(")")
    .Select()
    .WriteText("*")
    .From()
    .WriteText("CTE")
    .End()
    .GetString();
```

The result:

```csharp
WITH CTE AS
(
    SELECT [Id]
        , [Name]
        , [IsActive]
        , [DateInsertedUtc]
    FROM [dbo].[Person] WITH (NOLOCK)
)
SELECT * FROM CTE;
```

{: .note }
> This class provides a rich set of methods for composing any SQL statement, including `INSERT`, `DELETE`, `UPDATE`, and more.
