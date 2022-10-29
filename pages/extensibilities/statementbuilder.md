---
layout: default
sidebar: extensibilities
title: "Statement Builder"
permalink: /extensibility/statementbuilder
tags: [repodb, statementbuilder]
parent: EXTENSIBILITIES
---

# Statement Builder

---

A statement builder is an object that is being used by the library to generate the SQL statements for all operations. To be more practical, when you call the [QueryAll](/operation/queryall) operation, a SQL statement is being constructed via statement builder and is being executed by the engine against the database.

You have to use the [BaseStatementBuilder](/class/basestatementbuilder) class when implementing a customized statement builder. With this, some methods of the [IStatementBuilder](/interface/istatementbuilder) interface were already implemented and you automatically inheritted them. Therefore, you do not need to implement it yourselves.

> We recommend to use the [BaseStatementBuilder](/class/basestatementbuilder) over [IStatementBuilder](/interface/istatementbuilder) when creating a customized statement builder.

## Pre-requisites

- The naming to this object must be `Desired Name` + `StatementBuiler` (i.e.: `SqlServerStatementBuiler`).
- The implementation must be `internal sealed`.
- The namespace must be `RepoDb.StatementBuilders`.
- The class must be residing inside the `StatementBuilders` folder.

> Please see the [folder structuring](/extensibility/folderstructuring) page.

## Get Started

To create a statement builder class, simply inherits the [BaseStatementBuilder](/class/basestatementbuilder) class.

```csharp
internal sealed class OptimizedSqlServerStatementBuilder : BaseStatementBuilder
{
    public OptimizedSqlServerStatementBuilder()
        : base(DbSettingMapper.Get(typeof(SqlConnection)),
            new SqlServerConvertFieldResolver(),
            new ClientTypeToAverageableClientTypeResolver())
    { }
}
```

You have to call the `base()` method on the constructor by passing the following arguments.

- [Database Setting](/extensibility/databasesetting) - the actual database settings to be used.
- [Convert Field Resolver](/extensibility/convertfieldresolver) - a resolver that is being used by the library as an equivalent calls to `CONVERT` keyword.
- [Average Type Resolver](/extensibility/averagetyperesolver) - a resolver that is being used to define which type of .NET CLR type the library is going to be used when calling the [Average](/operation/average) and [AverageAll](/operation/averageall) operations.

## Implementing the Abstract Methods

All methods of the [BaseStatementBuilder](/class/basestatementbuilder) class were implemented as virtuals, except for the following.

- `CreateBatchQuery` - a method that generates a SQL statement for [BatchQuery](/operation/batchquery) operation.
- `CreateMerge` - a method that generates a SQL statement for [Merge](/operation/merge) operation.
- `CreateMergeAll` - a method that generates a SQL statement for [MergeAll](/operation/mergeall) operation.

### Why?

- [BatchQuery](/operation/batchquery) - each RDBMS data provider has a different syntax for paging/batching.
- [Merging](/operation/merge) - not all RDBMS data providers are supporting the default merge statement.

Below is the sample code to implement the [Merge](/operation/merge) operation.

```csharp
public override string CreateMerge(string tableName,
    IEnumerable<Field> fields,
    IEnumerable<Field> qualifiers = null,
    DbField primaryField = null,
    DbField identityField = null,
    string hints = null)
{
    // Initialize the needed variables
    var dbSetting = DbSettingMapper.GetDbSetting<SqlConnection>();
    var builder = new QueryBuilder();

    // Get the insertable and updateable fields
    var insertableFields = fields
        .Where(field => !string.Equals(field.Name, identityField?.Name, StringComparison.OrdinalIgnoreCase));
    var updateableFields = fields
        .Where(field => !string.Equals(field.Name, primaryField?.Name, StringComparison.OrdinalIgnoreCase) &&
            !string.Equals(field.Name, identityField?.Name, StringComparison.OrdinalIgnoreCase));

    // Build the query
    builder.Clear()
        // MERGE T USING S
        .Merge()
        .TableNameFrom(tableName, dbSetting)
        .As("T")
        .HintsFrom(hints)
        .Using()
        .OpenParen()
        .Select()
        .ParametersAsFieldsFrom(fields, 0, dbSetting)
        .CloseParen()
        .As("S")
        // QUALIFIERS
        .On()
        .OpenParen()
        .WriteText(qualifiers?
            .Select(
                field => field.AsJoinQualifier("S", "T", dbSetting))
                    .Join(" AND "))
        .CloseParen()
        // WHEN NOT MATCHED THEN INSERT VALUES
        .When()
        .Not()
        .Matched()
        .Then()
        .Insert()
        .OpenParen()
        .FieldsFrom(insertableFields, dbSetting)
        .CloseParen()
        .Values()
        .OpenParen()
        .AsAliasFieldsFrom(insertableFields, "S", dbSetting)
        .CloseParen()
        // WHEN MATCHED THEN UPDATE SET
        .When()
        .Matched()
        .Then()
        .Update()
        .Set()
        .FieldsAndAliasFieldsFrom(updateableFields, "T", "S", dbSetting);

	// Return the query
    return builder.GetString();
}
```

> The code snippets above is just a sample, it was not tested and is not working. In addition to this note, you have to manually implement all the abstract methods.

## Overriding the Virtual Methods

It is not necessary to override a virtual method of the [BaseStatementBuilder](/class/basestatementbuilder). However, if you do override, then below is a sample referrence-code that overrides the default implementation of [CreateTruncate](/operation/truncate) operation.

```csharp
public override string CreateTruncate(string tableName)
{
    // Initialize the builder
    var builder = new QueryBuilder();

    // Build the query
    builder.Clear()
        .Truncate()
        .From()
        .TableNameFrom(tableName, DbSetting)
        .End();

    // Return the query
    return builder.GetString();
}
```

You can do override the other virtual methods (as many as you like).

> It is very important to only override the virtual methods if a customization or different implementation is needed.

## Usability

Once you have the statement builder implemented in a customized class, you can directly inject it on the library using the [StatementBuilderMapper](/mapper/statementbuildermapper) class.

```csharp
StatementBuilderMapper.Add<SqlConnection>(new OptimizedSqlServerStatementBuilder());
```

Or by passing it to any extended [operations](/docs#operations) of the `DbConnection` object.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var person = connection.Query<Person>(p => p.Id == 10045, statementBuilder: new OptimizedSqlServerStatementBuilder()).FirstOrDefault();
}
```

Or by passing it on the constructor of the [BaseRepository](/class/baserepository) object.

```csharp
public class PersonRepository : BaseRepository<Person, SqlConnection>
{
    public PersonRepository(ISettings settings)
        : base(settings.Value.ConnectionString, new OptimizedSqlServerStatementBuilder())
    { }
}

using (var repository = new PersonRepository(new AppSettings()))
{
    var person = repository.Query(p => p.Id == 10045).FirstOrDefault();
}
```

Or even to the constructor of [DbRepository](/class/dbrepository) object.

```csharp
public class DatabaseRepository : DbRepository<SqlConnection>
{
    public DatabaseRepository(ISettings settings)
        : base(settings.Value.ConnectionString, new OptimizedSqlServerStatementBuilder())
    { }
}

using (var repository = new DatabaseRepository(new AppSettings()))
{
    var person = repository.Query<Person>(p => p.Id == 10045).FirstOrDefault();
}
```

## How to Retrieve?

You can use the [StatementBuilderMapper](/mapper/statementbuildermapper) to get the statement builder by connection type.

```csharp
var builder = StatementBuilderMapper.Get<SqlConnection>();
// Use the 'builder' here
```

Or, you can use the `GetStatementBuilder()` extended method of the connection object.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var builder = connection.GetStatementBuilder();
    // Use the 'builder' here
}
```
