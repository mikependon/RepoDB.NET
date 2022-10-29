---
layout: default
sidebar: extensibilities
title: "Database Setting"
permalink: /extensibility/databasesetting
tags: [repodb, databasesetting]
parent: EXTENSIBILITIES
---

# Database Setting

---

A database setting is an object that contains the information that are specific to a single RDBMS data providers. It holds the information of the following.

- A character for closing quotation, opening quotation, parameter prefix and schema separator.
- A type that is used for averaging.
- A database schema.
- A support to hints.
- A usage of upsert over merge.
- A direction property enability of command object.
- A dispose behaviour of the data reader after the execution.
- A support to multiple-statement executions.
- A preparation of the command executions.

## Pre-requisites

- The naming must be `Desired Name` + `DbSetting` (i.e.: `SqlServerDbSetting`).
- The implementation must be `internal sealed`.
- The namespace must be `RepoDb.DbSettings`.
- The class must be residing inside the `DbSettings` folder.

> Please see the [folder structuring](/extensibility/folderstructuring) page.

## Get Started

You have to implement the class that inherits the [BaseDbSetting](/class/basedbsetting) class.

```csharp
internal sealed class MyCustomSqlServerDbSetting : BaseDbSetting
{
    ...
}
```

Then, in the constructor, set the values of every property.

```csharp
internal MyCustomSqlServerDbSetting()
    : base()
{
    AreTableHintsSupported = true;
    AverageableType = typeof(double);
    ClosingQuote = "]";
    DefaultSchema = "dbo";
    IsDirectionSupported = true;
    IsExecuteReaderDisposable = true;
    IsMultiStatementExecutable = true;
    IsPreparable = true;
    IsUseUpsert = false;
    OpeningQuote = "[";
    ParameterPrefix = "@";
    SchemaSeparator = ".";
}
```

## Usability

Simply use the [DbSettingMapper](/mapper/dbsettingmapper) class to map it to the specific RDBMS data provider.

```csharp
DbSettingMapper.Add(typeof(SqlConnection), new MyCustomSqlServerDbSetting(), true);
```

The library will then use your customized database setting for all its database specific activity.

A sample is the SQL statement generation. Behind the scene, the library has auto-formatted the created SQL statement with some information from the settings (i.e.: OpeningQuote, ClosingQuote, SchemaSeparator and ParameterPrefix).

Let say you had called the [Query](/operation/query) operation for SQL Server. 

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = connection.Query<Person>(p => p.Id == 10045);
    // Use the 'people' here
}
```

Then, the following SQL statement will be generated.

```csharp
> SELECT [Id], [Name], ..., [CreatedDateUtc] FROM [dbo].[Person] WHERE ([Id] = @Id);
```

> As you noticed on the generated SQL statement, the following settings (i.e.: OpeningQuote, ClosingQuote, SchemaSeparator and ParameterPrefix) were used.

## How to Retrieve?

You can use the [DbHelperMapper](/mapper/dbhelpermapper) to get the database helper by connection type.

```csharp
var helper = DbHelperMapper.Get<SqlConnection>();
// Use the 'helper' here
```

Or, you can use the `GetDbHelper()` extended method of the connection object.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var helper = connection.GetDbHelper();
    // Use the 'helper' here
}
```