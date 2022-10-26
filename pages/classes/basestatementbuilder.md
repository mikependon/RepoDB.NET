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

This class stands as the base class of all [IStatementBuilder](/interface/istatementbuilder)-based classes.

#### Use-Cases

To simplify your implementation, always use this class over the [IStatementBuilder](/interface/istatementbuilder) interface when working with customized SQL generator.

#### Virtual Methods

This class has virtual methods pre-implemented with the defined logic. It is the author's choices to add this virtual methods on this base class.

The reason to have these virtual methods are the commonality of the SQL Statements used on most RDBMS data providers (i.e.: the SQL Server and the other RDBMS almost have the same way of SQL generation).

#### Below are the list of virtual methods.

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

### Abstract Methods

This class has some abstract methods that is required to be implemented in the derived classes. It is the author's choices to add this abstract methods on this base class.

The reason to have these abstract methods are the implementation-difference of the SQL Statements between the different RDBMS data providers (i.e.: the SQL Server is using `MERGE` keyword for merging rows whereas the other RDBMS are not).

#### Below are the list of abstract methods.

- CreateBatchQuery
- CreateMerge
- CreateMergeAll

### How to Implement?

Simply create a class that inherits this class.

```csharp
public sealed class OptimizedSqlServerStatementBuilder : BaseStatementBuilder
{
    ...
}
```

Then, override the abstract methods.

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

> You can also override the other non-abstract methods as they were all implemented as `virtual`. It is very important to take note that you should only override the base functionalities if you have a specialized implementation for the specific RDBMS data provider.

### Usability

Simply use the [StatementBuilderMapper](/mapper/statementbuildermapper) class to map it to the specific RDBMS data provider.

```csharp
StatementBuilderMapper.Add(typeof(SqlConnection), new OptimizedSqlServerStatementBuilder(), true);
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

> Please visit the [Statement Builder](/extensibility/statementbuilder) page for more details.