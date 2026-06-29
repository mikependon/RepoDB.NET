---
layout: default
sidebar: classes
title: "BaseStatementBuilder"
description: "A base class for all the statement builder classes."
permalink: /class/basestatementbuilder
tags: [repodb, basestatementbuilder]
parent: CLASSES
---

# BaseStatementBuilder

---

This class is the base class of all [IStatementBuilder](/interface/istatementbuilder)-based classes.

## Use-Cases

Use this class instead of the [IStatementBuilder](/interface/istatementbuilder) interface when implementing a custom SQL generator.

## Virtual Methods

The following virtual methods have pre-implemented logic covering SQL generation patterns common across most RDBMS providers.

- CreateAverage
- CreateAverageAll
- CreateCount
- CreateCountAll
- CreateExists
- CreateInsert
- CreateInsertAll
- CreateMax
- CreateMaxAll
- CreateMin
- CreateMinAll
- CreateQuery
- CreateQueryAll
- CreateSum
- CreateSumAll
- CreateTruncate
- CreateUpdate
- CreateUpdateAll

## Abstract Methods

The following abstract methods must be implemented in derived classes, as their SQL generation differs between RDBMS providers (e.g., SQL Server uses the `MERGE` keyword, while others do not).

- CreateBatchQuery
- CreateMerge
- CreateMergeAll

## How to Implement?

Create a class that inherits this class.

```csharp
public sealed class OptimizedSqlServerStatementBuilder : BaseStatementBuilder
{
    ...
}
```

Then override the abstract methods.

```csharp
public sealed class OptimizedSqlServerStatementBuilder : BaseStatementBuilder
{
    public string CreateBatchQuery(QueryBuilder queryBuilder,
        string tableName,
        IEnumerable<Field> fields,
        int? page,
        int? rowsPerBatch,
        IEnumerable<OrderField> orderBy = null,
        QueryGroup where = null,
        string hints = null)
    {
        // Implement the 'CreateBatchQuery' here
    }

    public string CreateMerge(QueryBuilder queryBuilder,
        string tableName,
        IEnumerable<Field> fields,
        IEnumerable<Field> qualifiers = null,
        DbField primaryField = null,
        DbField identityField = null,
        string hints = null)
    {
        // Implement the 'CreateMerge' here
    }

    public abstract string CreateMergeAll(QueryBuilder queryBuilder,
        string tableName,
        IEnumerable<Field> fields,
        IEnumerable<Field> qualifiers = null,
        int batchSize = Constant.DefaultBatchOperationSize,
        DbField primaryField = null,
        DbField identityField = null,
        string hints = null)
    {
        // Implement the 'CreateMergeAll' here
    }
}
```

{: .note }
> All non-abstract methods are `virtual` and can be overridden. Only override them when you have a specialized implementation for a specific RDBMS provider.

## Usability

Use the [StatementBuilderMapper](/mapper/statementbuildermapper) class to map it to a specific RDBMS provider.

```csharp
StatementBuilderMapper.Add(typeof(SqlConnection), new OptimizedSqlServerStatementBuilder(), true);
```

Or pass it in the constructor of a [BaseRepository](/class/baserepository) object.

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

Or in the constructor of a [DbRepository](/class/dbrepository) object.

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

{: .note }
> Please visit the [Statement Builder](/extensibility/statementbuilder) page for more details.
