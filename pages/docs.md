---
layout: page
title: Documentation (RepoDb)
permalink: /docs
---

## Documentation

RepoDb has a bunch of things available for you. You can maximize your development by leveraging most of it.

### Features

Below are the list of features that you may be interested.

- [Batch Operations]()
- [Bulk Operations]()
- [Caching]()
- [Connection Persistency]()
- [Enumeration]()
- [Expression Trees]()
- [Field/Class Mapping]()
- [Hints]()
- [Multi-Resultset Query]()
- [Property Handlers]()
- [Repositories]()
- [Tracing]()
- [Transaction]()
- [Type Mapping]()

### Methods

Below are the list of *Execute* operations that you can use.

- [ExecuteNonQuery](/operations/executenonquery)
- [ExecuteQuery](/operations/executequery)
- [ExecuteQueryMultiple](/operations/executequerymultiple)
- [ExecuteReader](/operations/executereader)
- [ExecuteScalar]()

Below are the list of *Fluent* operations that you can use.

- [Average]()
- [AverageAll]()
- [BatchQuery]()
- [BulkDelete]()
- [BulkInsert]()
- [BulkMerge]()
- [BulkUpdate]()
- [Count]()
- [CountAll]()
- [Delete]()
- [DeleteAll]()
- [Exists]()
- [Insert]()
- [InsertAll]()
- [Max]()
- [MaxAll]()
- [Merge]()
- [MergeAll]()
- [Min]()
- [MinAll]()
- [Query]()
- [QueryAll]()
- [QueryMultiple]()
- [Sum]()
- [SumAll]()
- [Truncate]()
- [Update]()
- [UpdateAll]()

### Classes

Below are the list of classes that you can use.

- [BulkInsertMapItem]()
- [CacheItem]()
- [CancellableTraceLog]()
- [ClassProperty]()
- [DataEntityDataReader]()
- [DbField]()
- [Field]()
- [OrderField]()
- [PropertyValue]()
- [Parameter]()
- [QueryField]()
- [QueryGroup]()
- [TraceLog]()

### Attributes

Below are the list of attributes available per package.

##### Core

- [Identity]()
- [Map]()
- [Primary]()
- [PropertyHandler]()
- [Text]()
- [TypeMap]()

##### SqlServer

- [MicrosoftSqlServerTypeMapAttribute]()
- [SystemSqlServerTypeMapAttribute]()

##### MySql

- [MySqlTypeMapAttribute]()

##### PostgreSql

- [NpgsqlTypeMapAttribute]()

### Enumerations

- [Conjunction]()
- [ConnectionPersistency]()
- [ConversionType]()
- [Operation]()
- [Order]()

### Interfaces

Below are the list of interface that you can use for implementation.

- [ICache]()
- [IDbHelper]()
- [IDbSetting]()
- [IPropertyHandler]()
- [IResolver]()
- [IStatementBuilder]()
- [ITrace]()

### Helpers

Below are the list of helper classes that you can use.

- [ClassExpression]()
- [DataReader (Reflection)]()
- [FunctionCache (Reflection)]()
- [FunctionFactory (Reflection)]()
- [QueryBuilder]()
- [QueryMultipleExtractor]()

### Cachers

Below are the list of cache objects that you can use.

- [CommandTextCache]()
- [DbFieldCache]()
- [FieldCache]()
- [IdentityCache]()
- [PrimaryCache]()
- [PropertyCache]()
- [PropertyMappedNameCache]()

### Mappers

Below are the list of mappers that you can leverage.

- [DbHelperMapper]()
- [DbSettingMapper]()
- [PropertyTypeHandlerMapper]()
- [StatementBuilderMapper]()
- [TypeMapper]()

### Resolvers

Below are the list of resolvers available per package.

##### SqlServer

- [DbTypeToSqlServerStringNameResolver]()
- [SqlServerConvertFieldResolver]()
- [SqlServerDbTypeNameToClientTypeResolver]()

##### Core

- [ClientTypeToAverageableClientTypeResolver]()
- [ClientTypeToDbTypeResolver]()
- [DbConvertFieldResolver]()
- [DbTypeToClientTypeResolver]()

##### SqLite

- [DbTypeToSqLiteStringNameResolver]()
- [SqLiteConvertFieldResolver]()
- [SqLiteDbTypeNameToClientTypeResolver]()

##### MySql

- [MySqlDbTypeNameToClientTypeResolver]()
- [MySqlDbTypeToMySqlStringNameResolver]()

##### PostgreSql

- [DbTypeToPostgreSqlStringNameResolver]()
- [PostgreSqlConvertFieldResolver]()
- [PostgreSqlDbTypeNameToClientTypeResolver]()

### Extensibility

Below are the list of objects that are useful to support other data provider.

- [Database Helper]()
- [Database Setting]()
- [Statement Builder]()


