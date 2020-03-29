---
layout: page
title: "IStatementBuilder (RepoDb)"
permalink: /interface/istatementbuilder
tags: [repodb, class, istatementbuilder, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

## IStatementBuilder

This interface is used to mark a class to be a statement builder object. I provides necessary methods for your to be able to override the way how the SQL statements are being constructed.

#### Methods

Below are the methods available from this interface.

- `CreateAverage` - used to create a SQL statement for the [Average](/operation/average) operation.
- `CreateAverageAll` - used to create a SQL statement for the [AverageAll](/operation/averageall) operation.
- `CreateBatchQuery` - used to create a SQL statement for the [BatchQuery](/operation/batchquery) operation.
- `CreateCount` - used to create a SQL statement for the [Count](/operation/count) operation.
- `CreateCountAll` - used to create a SQL statement for the [Average](/operation/countall) operation.
- `CreateDelete` - used to create a SQL statement for the [Delete](/operation/delete) operation.
- `CreateDeleteAll` - used to create a SQL statement for the [DeleteAll](/operation/deleteall) operation.
- `CreateExists` - used to create a SQL statement for the [Exists](/operation/exists) operation.
- `CreateInsert` - used to create a SQL statement for the [Insert](/operation/insert) operation.
- `CreateInsertAll` - used to create a SQL statement for the [InsertAll](/operation/insertall) operation.
- `CreateMax` - used to create a SQL statement for the [Max](/operation/max) operation.
- `CreateMaxAll` - used to create a SQL statement for the [MaxAll](/operation/maxall) operation.
- `CreateMerge` - used to create a SQL statement for the [Merge](/operation/merge) operation.
- `CreateMergeAll` - used to create a SQL statement for the [MergeAll](/operation/mergeall) operation.
- `CreateMin` - used to create a SQL statement for the [Min](/operation/min) operation.
- `CreateMinAll` - used to create a SQL statement for the [MinAll](/operation/minall) operation.
- `CreateQuery` - used to create a SQL statement for the [Query](/operation/query) operation.
- `CreateQueryAll` - used to create a SQL statement for the [QueryAll](/operation/queryall) operation.
- `CreateSum` - used to create a SQL statement for the [Sum](/operation/sum) operation.
- `CreateSumAll` - used to create a SQL statement for the [SumAll](/operation/sumall) operation.
- `CreateTruncate` - used to create a SQL statement for the [Truncate](/operation/truncate) operation.
- `CreateUpdate` - used to create a SQL statement for the [Update](/operation/update) operation.
- `CreateUpdateAll` - used to create a SQL statement for the [UpdateAll](/operation/updateall) operation.

#### Use-Cases

This is very useful if you wish to override the existing statement builder of the library, or wish to support the other RDBMS database providers.

Please visit the [Statement Builder](/extensibility/statementbuilder) to learn more about the statement builder.

#### How to Implement?

You have to manually create a class that implements this interface.

```csharp
public class OptimizedSqlServerStatementBuilder : IStatementBuilder
{
        private IDbSetting m_dbSetting = new SqlServerDbSetting();

        public string CreateAverage(QueryBuilder queryBuilder,
            string tableName,
            Field field,
            QueryGroup where = null,
            string hints = null)
        {
                // Initialize the builder
                var builder = queryBuilder ?? new QueryBuilder();

                // Build the query
                builder.Clear()
                        .Select()
                        .Average(field, DbSetting, ConvertFieldResolver)
                        .WriteText($"AS {"AverageValue".AsQuoted(DbSetting)}")
                        .From()
                        .TableNameFrom(tableName, DbSetting)
                        .HintsFrom(hints)
                        .WhereFrom(where, DbSetting)
                        .End();

                // Return the query
                return builder.GetString();
        }

        ...

        public string CreateQuery(QueryBuilder queryBuilder,
            string tableName,
            IEnumerable<Field> fields,
            QueryGroup where = null,
            IEnumerable<OrderField> orderBy = null,
            int? top = null,
            string hints = null)
        {
                // Initialize the builder
                var builder = queryBuilder ?? new QueryBuilder();

                // Build the query
                builder.Clear()
                        .Select()
                        .TopFrom(top)
                        .FieldsFrom(fields, DbSetting)
                        .From()
                        .TableNameFrom(tableName, DbSetting)
                        .HintsFrom(hints)
                        .WhereFrom(where, DbSetting)
                        .OrderByFrom(orderBy, DbSetting)
                        .End();

                // Return the query
                return builder.GetString();
        }

        ...
}
```

#### How to Use?

You can instantiate a new instance and pass it when you are calling any [fluent methods](/docs/fluent-methods).

```csharp
var statementBuilder = new OptimizedSqlServerStatementBuilder();
using (var connection = new SqlConnection(connectionString))
{
        var people = connection.QueryAll<Person>(statementBuilder: statementBuilder);
}
```

Or, you can pass it on the constructor of [BaseRepository](/class/baserepository) or [DbRepository](/class/dbrepository).

```csharp
// Repository class implementation
public class PersonRepository : BaseRepository<Person, SqlConnection>
{
        public PersonRepository(string connectionString)
                : base(connectionString, new OptimizedSqlServerStatementBuilder())
        { }
}

// Repository class usability
using (var repository = new PersonRepository(connectionString))
{
        var people = connection.QueryAll();
}
```

Or, you can use the [StatementBuilderMapper](/mapper/statementbuildermapper) class to map it with specific RDBMS data provider.

```csharp
StatementBuilderMapper.Map(typeof(SqlConnection), new OptimizedSqlServerStatementBuilder(), true);
```

> By using the [StatementBuilderMapper](/mapper/statementbuildermapper), the library will automatically use the mapped statement builder when calling the `DbConnection`, [BaseRepository](/class/baserepository) or [DbRepository](/class/dbrepository) methods. It will vary on the type of the `DbConnection` object you used.