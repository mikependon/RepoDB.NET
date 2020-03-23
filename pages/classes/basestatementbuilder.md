---
layout: page
title: "BaseStatementBuilder (RepoDb)"
permalink: /class/basestatementbuilder
tags: [repodb, class, basestatementbuilder, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

## BaseStatementBuilder

This class stands as the base class of all [IStatementBuilder](/interface/istatementbuilder)-based classes.

#### Use-Cases

To simplify your implementation, always use this class over [IStatementBuilder](/interface/istatementbuilder) when working with customized SQL generator.

#### Virtual Methods

This class has virtual methods pre-implemented with the defined logics. It is the author's choices to add this virtual methods on this base class.

The reason to have these virtual methods are the commonality of most RDBMS data providers (ie: the SQL Server and [SQLite](https://www.nuget.org/packages/RepoDb.SqLite) almost have the same way of SQL generation).

Below are the list of virtual methods.

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

#### Abstract Methods

This class has some abstract methods that is required to be implemented in the derived classes. It is the author's choices to add this abstract methods on this base class.

The reason to have these abstract methods are the implementation-difference between different RDBMS data providers (ie: the SQL Server is using `MERGE` keyword for merging whereas the [SQLite](https://www.nuget.org/packages/RepoDb.SqLite) is using `INSERT OR REPLACE`).

Below are the list of abstract methods.

- CreateBatchQuery
- CreateMerge
- CreateMergeAll

#### How to Implement?

Simply create a class that inherits this class.

```csharp
public sealed class MyCustomSqlServerStatementBuilder : BaseStatementBuilder
{
    ...
}
```

Then override the abstract methods.

```csharp
public sealed class MyCustomSqlServerStatementBuilder : BaseStatementBuilder
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

#### How to Use?

Simply use the [StatementBuilderMapper](/mapper/statementbuildermapper) class to map it to the specific RDBMS data provider.

```csharp
StatementBuilderMapper.Add(typeof(SqlConnection), new MyCustomSqlServerStatementBuilder(), true);
```