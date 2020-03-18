---
layout: page
title: Documentation (RepoDb)
permalink: /docs
---

## Documentation

RepoDb has a bunch of things available for you. You can maximize your development by leveraging most of it.

### Features

Below are the list of features that you may be interested.

- [Batch Operations](/feature/batch-operations)
- [Bulk Operations](/feature/bulk-operations)
- [Caching](/feature/caching)
- [Connection Persistency](/feature/connection-persistency)
- [Enumeration](/feature/enumeration)
- [Expression Trees](/feature/expression-trees)
- [Field/Class Mapping](/feature/field-class-mapping)
- [Hints](/feature/hints)
- [Multi-Resultset Query](/feature/multi-resultset-query)
- [Property Handlers](/feature/property-handlers)
- [Repositories](/feature/repositories)
- [Tracing](/feature/tracing)
- [Transaction](/feature/transaction)
- [Type Mapping](/feature/type-mapping)

### Methods

Below are the list of *Execute* operations that you can use.

- [ExecuteNonQuery](/operation/executenonquery)
- [ExecuteQuery](/operation/executequery)
- [ExecuteQueryMultiple](/operation/executequerymultiple)
- [ExecuteReader](/operation/executereader)
- [ExecuteScalar](/operation/executescalar)

Below are the list of *Fluent* operations that you can use.

- [Average](/operation/average)
- [AverageAll](/operation/averageall)
- [BatchQuery](/operation/batchquery)
- [BulkDelete](/operation/bulkdelete)
- [BulkInsert](/operation/bulkinsert)
- [BulkMerge](/operation/bulkmerge)
- [BulkUpdate](/operation/bulkupdate)
- [Count](/operation/count)
- [CountAll](/operation/countall)
- [Delete](/operation/delete)
- [DeleteAll](/operation/deleteall)
- [Exists](/operation/exists)
- [Insert](/operation/insert)
- [InsertAll](/operation/insertall)
- [Max](/operation/max)
- [MaxAll](/operation/maxall)
- [Merge](/operation/merge)
- [MergeAll](/operation/mergeall)
- [Min](/operation/min)
- [MinAll](/operation/minall)
- [Query](/operation/query)
- [QueryAll](/operation/queryall)
- [QueryMultiple](/operation/querymultiple)
- [Sum](/operation/sum)
- [SumAll](/operation/sumall)
- [Truncate](/operation/truncate)
- [Update](/operation/update)
- [UpdateAll](/operation/updateall)

### Classes

Below are the list of classes that you can use.

- [BulkInsertMapItem](/class/bulkinsertmapitem)
- [CacheItem](/class/cacheitem)
- [CancellableTraceLog](/class/cancellabletracelog)
- [ClassExpression](/class/classexpression)
- [ClassProperty](/class/classproperty)
- [DataEntityDataReader](/class/dataentitydatareader)
- [DataReader (Reflection)](/class/datareader)
- [DbField](/class/dbfield)
- [Field](/class/field)
- [OrderField](/class/orderfield)
- [Parameter](/class/parameter)
- [PropertyValue](/class/propertyvalue)
- [QueryBuilder](/class/querybuilder)
- [QueryField](/class/queryfield)
- [QueryGroup](/class/querygroup)
- [QueryMultipleExtractor](/class/querymultipleextractor)
- [TraceLog](/class/tracelog)

##### SqlServer

- [SqlServerTableHints](/class/sqlservertablehints)

### Attributes

Below are the list of attributes available per package.

##### Core

- [Identity](/attribute/identity)
- [Map](/attribute/map)
- [Primary](/attribute/primary)
- [PropertyHandler](/attribute/propertyhandler)
- [Text](/attribute/text)
- [TypeMap](/attribute/typemap)

##### SqlServer

- [MicrosoftSqlServerTypeMapAttribute](/attribute/microsoftsqlservertypemap)
- [SystemSqlServerTypeMapAttribute](/attribute/systemsqlservertypemap)

##### MySql

- [MySqlTypeMapAttribute](/attribute/mysqltypemapattribute)

##### PostgreSql

- [NpgsqlTypeMapAttribute](/attribute/npgsqltypemapattribute)

### Enumerations

- [Conjunction](/enumeration/conjunction)
- [ConnectionPersistency](/enumeration/connectionpersistency)
- [ConversionType](/enumeration/conversiontype)
- [Operation](/enumeration/operation)
- [Order](/enumeration/order)

### Interfaces

Below are the list of interface that you can use for implementation.

- [ICache](/interface/icache)
- [IDbHelper](/interface/idbhelper)
- [IDbSetting](/interface/idbsetting)
- [IPropertyHandler](/interface/ipropertyhandler)
- [IResolver](/interface/iresolver)
- [IStatementBuilder](/interface/istatementbuilder)
- [ITrace](/interface/itrace)

### Cachers

Below are the list of cache objects that you can use.

- [CommandTextCache](/cacher/commandtextcache)
- [DbFieldCache](/cacher/dbfieldcache)
- [FieldCache](/cacher/fieldcache)
- [IdentityCache](/cacher/identitycache)
- [PrimaryCache](/cacher/primarycache)
- [PropertyCache](/cacher/propertycache)
- [PropertyMappedNameCache](/cacher/propertymappednamecache)

### Mappers

Below are the list of mappers that you can leverage.

- [DbHelperMapper](/mapper/dbhelpermapper)
- [DbSettingMapper](/mapper/dbsettingmapper)
- [PropertyTypeHandlerMapper](/mapper/propertytypehandlermapper)
- [StatementBuilderMapper](/mapper/statementbuildermapper)
- [TypeMapper](/mapper/typemapper)

### Extensibility

Below are the list of objects that are useful to support other data provider.

- [Database Helper](/extensibility/databasehelper)
- [Database Setting](/extensibility/databasesetting)
- [Statement Builder](/extensibility/statementbuilder)


