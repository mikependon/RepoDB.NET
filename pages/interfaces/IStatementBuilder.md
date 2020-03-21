---
layout: page
title: "IStatementBuilder (RepoDb)"
permalink: /interface/istatementbuilder
tags: [repodb, class, istatementbuilder, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

## IStatementBuilder

This is an interface that is used to mark a class to be a statement builder object.

#### Methods

Below are the methods available from this interface.

- CreateAverage - used to create a SQL Statement for the [Average](/operation/average) operation.
- CreateAverageAll - used to create a SQL Statement for the [AverageAll](/operation/averageall) operation.
- CreateBatchQuery - used to create a SQL Statement for the [BatchQuery](/operation/batchquery) operation.
- CreateCount - used to create a SQL Statement for the [Count](/operation/count) operation.
- CreateCountAll - used to create a SQL Statement for the [Average](/operation/countall) operation.
- CreateDelete - used to create a SQL Statement for the [Delete](/operation/delete) operation.
- CreateDeleteAll - used to create a SQL Statement for the [DeleteAll](/operation/deleteall) operation.
- CreateExists - used to create a SQL Statement for the [Exists](/operation/exists) operation.
- CreateInsert - used to create a SQL Statement for the [Insert](/operation/insert) operation.
- CreateInsertAll - used to create a SQL Statement for the [InsertAll](/operation/insertall) operation.
- CreateMax - used to create a SQL Statement for the [Max](/operation/max) operation.
- CreateMaxAll - used to create a SQL Statement for the [MaxAll](/operation/maxall) operation.
- CreateMerge - used to create a SQL Statement for the [Merge](/operation/merge) operation.
- CreateMergeAll - used to create a SQL Statement for the [MergeAll](/operation/mergeall) operation.
- CreateMin - used to create a SQL Statement for the [Min](/operation/min) operation.
- CreateMinAll - used to create a SQL Statement for the [MinAll](/operation/minall) operation.
- CreateQuery - used to create a SQL Statement for the [Query](/operation/query) operation.
- CreateQueryAll - used to create a SQL Statement for the [QueryAll](/operation/queryall) operation.
- CreateSum - used to create a SQL Statement for the [Sum](/operation/sum) operation.
- CreateSumAll - used to create a SQL Statement for the [SumAll](/operation/sumall) operation.
- CreateTruncate - used to create a SQL Statement for the [Truncate](/operation/truncate) operation.
- CreateUpdate - used to create a SQL Statement for the [Update](/operation/update) operation.
- CreateUpdateAll - used to create a SQL Statement for the [UpdateAll](/operation/updateall) operation.

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

You have to use the [StatementBuilderMapper](/mapper/statementbuildermapper) class to map it with specific RDBMS data provider.

```csharp
StatementBuilderMapper.Map(typeof(SqlConnection), new OptimizedSqlServerStatementBuilder(), true);
```