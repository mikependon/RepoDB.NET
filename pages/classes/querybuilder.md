---
layout: navpage
sidebar: classes
title: "QueryBuilder (RepoDb)"
permalink: /class/querybuilder
tags: [repodb, class, querybuilder, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

## QueryBuilder

This class is used to compose a SQL statement in a fluent manner. It is very fluent as it only appending the specific string based on the name of the methods.

> Some methods accepts an argument of classes and objects and that will automatically be converted to a SQL text.

#### Use-Cases

- It is used in [IStatementBuilder](/interface/istatementbuilder) when composing a SQL text.
- It is used to compose a SQL statement in a fluent manner.

#### Creating an Instance

```csharp
var querybuilder = new QueryBuilder();
```

#### Composing a simple Query Statement

Below is the way on how to create a very simple query SQL statement for [ExecuteQuery](/operation/executequery) execution.

Let us say you have a `Person` class that targets the `[dbo].[Person]` table.

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

The result of this would be.

```csharp
> SELECT [Id], [Name], [IsActive], [DateInsertedUtc] FROM [dbo].[Person];
```

And you can then execute it like below.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = connection.ExecuteQuery<Person>(statement);
}
```

#### Passing the QueryGroup

To pass a [QueryGroup](/class/querygroup), use the `WhereFrom()` method.

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

The result of this would be.

```csharp
> SELECT [Id], [Name], [IsActive], [DateInsertedUtc] FROM [dbo].[Person] WHERE ([State] = @State);
```

#### Passing the Row Limit

To pass a row limit, simply call the `TopFrom()` method.

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

The result of this would be.

```csharp
> SELECT TOP (10) [Id], [Name], [IsActive], [DateInsertedUtc] FROM [dbo].[Person];
```

#### Ordering the Results

To order a result, simply call the `OrderByFrom()` method.

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

The result of this would be.

```csharp
> SELECT [Id], [Name], [IsActive], [DateInsertedUtc] FROM [dbo].[Person] ORDER BY [Name] ASC ;
```

#### Passing a Hints

To pass a hints, simply call the `HintsFrom()` method.

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

The result of this would be.

```csharp
> SELECT [Id], [Name], [IsActive], [DateInsertedUtc] FROM [dbo].[Person] WITH (NOLOCK) ;
```

#### The WriteText Method

This method is a generic method to be used in order to support the non-existing data-provider specific methods. This can be used anytime for the sake of SQL statement generation.

Below is a sample codes the generates a CTE based `SELECT` query.

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

The result of this would be.

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

> This class has a rich method-sets that can be used during the composition of the SQL Text. It can also be used when composing an `INSERT`, `DELETE`, `UPDATE` or whatever statement you wished to create.