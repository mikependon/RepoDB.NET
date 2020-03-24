---
layout: page
title: Releases for Core Package (RepoDb)
permalink: /releases/core
---

## Releases for RepoDb (Core)

Please click [here](https://www.nuget.org/packages/RepoDb) to see the actual Nuget package. Otherwise, please click [here](https://www.nuget.org/api/v2/package/RepoDb) to download it.


#### RepoDb (v1.10.11-beta1):

- At the [CancellableTraceLog.Cancel()](/class/cancellabletracelog) method, the `throwException` argument has been defaulted the `True`.
- Bug: Calling to [DeleteAll](/operation/deleteall) operation with the list of `PrimaryKeys` and `Transaction` object is failing. Reported by Christian Franck. [#404](https://github.com/mikependon/RepoDb/issues/404).


#### RepoDb (v1.10.10)

- Features: Full support to Bulk Operations ([BulkDelete](/operation/bulkdelete), [BulkInsert](/operation/bulkinsert), [BulkMerge](/operation/bulkmerge) and [BulkUpdate](/operation/bulkupdate)).
- Enhancement: `AsFieldsAsAliasFields` - added left and right aliases.
- Enhancement: XML Documentation updates for the SQL statement Builders.
- Enhancement: Add a Type extended method named IsNullable.
- Bug: Solve the intermittent `NullReferenceException` when calling the Batch Operations (ie: [InsertAll](/operation/insertall), [MergeAll](/operation/mergeall), [UpdateAll](/operation/updateall)). [#392](https://github.com/mikependon/RepoDb/issues/392)
- Bug: The property handlers mapped via `PropertyTypeHandlerMapper` is failing if both `NULLABLE` and `NON-NULLABLE` property types are present in the class. [#394](https://github.com/mikependon/RepoDb/issues/394)


#### RepoDb (v1.10.10-beta1)

- Full support to Bulk Operations ([BulkDelete](/operation/bulkdelete), [BulkInsert](/operation/bulkinsert), [BulkMerge](/operation/bulkmerge) and [BulkUpdate](/operation/bulkupdate)).
- `AsFieldsAsAliasFields` - added left and right aliases.
- XML Documentation updates for the SQL statement Builders.


#### RepoDb (v1.10.9)

- Enhancement: Compiler changes for `ClassProperty.GetDbType()` calls.
- Feature: Support for `SqlServer.SqlDbType` (Microsoft and System), `PostgreSql.NpgsqlDbType` [#390](https://github.com/mikependon/RepoDb/issues/390) and `MySql.MySqlDbType`.


#### RepoDb (v1.10.8)

- Bug: `InvalidCastException` Converting Enums to Strings in Postgres [#388](https://github.com/mikependon/RepoDb/issues/388)


#### RepoDb (v1.10.7)

- Enhancement: Separate the `Bulk` operations into a separate Solution and Nuget Package. [#385](https://github.com/mikependon/RepoDb/issues/385)
- Enhancement: Separete all SqlServer related stuffs to a different library named RepoDb.SqlServer. [#282](https://github.com/mikependon/RepoDb/issues/282)
- Enhancement: Remove the support to System.Data.SqlClient in the RepoDb.Core. [#386](https://github.com/mikependon/RepoDb/issues/386)


#### RepoDb (v1.10.6)

- Added the class property as an argument context in the `Get` and `Set` method.
- Exposed the underlying `DbRepository` property at the ``BaseRepository`` object.


#### RepoDb (v1.10.5)

##### Highlights

- Support: Initiate the support to `Microsoft.Data.SqlClient` namespace. [#374](https://github.com/mikependon/RepoDb/issues/374)
- Feature: Support `PropertyHandlerAttribute` as requested. [#367](https://github.com/mikependon/RepoDb/issues/367)
- Feature: Re-enabled the [BulkInsert](/operation/bulkinsert) for `DataTable`.
- Feature: Support deleting an array of entity objects or primary keys via [DeleteAll](/operation/deleteall). [#353](https://github.com/mikependon/RepoDb/issues/353)

##### Others

- Support: Change the target type to .NetStandard 2.0.
- Support: Initiate the support to `Microsoft.Data.SqlClient` namespace. [#374](https://github.com/mikependon/RepoDb/issues/374)
- Enhancement: Support to Microsoft.Data.SqlClient [#359](https://github.com/mikependon/RepoDb/issues/359)
- NTH: Add a table hints in [Merge](/operation/merge) and [MergeAll](/operation/mergeall). [#370](https://github.com/mikependon/RepoDb/issues/370)
- NTH: Add a table hints in [Update](/operation/update) and [UpdateAll](/operation/updateall). [#371](https://github.com/mikependon/RepoDb/issues/371)
- NTH: Add a table hints in [Delete](/operation/delete) and [DeleteAll](/operation/deleteall). [#376](https://github.com/mikependon/RepoDb/issues/376)
- NTH: Add a table hints in [Insert](/operation/insert) and [InsertAll](/operation/insertall). [#379](https://github.com/mikependon/RepoDb/issues/379)


#### RepoDb (v1.10.4)

- Initial support of RepoDb.PostgreSql.
- Added `IsPreparable` Property for `IDbSetting`.
- Removed the `basedOnFields` logic when extracting the `DbDataReader`.
- [InsertAll](/operation/insertall): Parsed the first entity property fields if the `fields` argument is not defined.
- Enhancement: Introduce the `BaseDbSetting` class to cater a uniform HashCode generator. #369
- Adhoc: Sealed and exposed the `DbSettings`, `DbHelpers` and the `DbStatementBuilders` classes.
- Enhancement: Introduced the `DbConvertFieldResolver` as the base converter field.


#### RepoDb (v1.10.4-beta2)

- Initial support of RepoDb.PostgreSql.


#### RepoDb (v1.10.4-beta1)

- Error: Invalid expression. The property <Id> is not defined on a target type #364 - MySql Support.


#### RepoDb (v1.10.3)

- Revert the recurrent calls to `DbFieldCache`.Get() in the ExecuteQuery method.


#### RepoDb (v1.10.2)

- Bug (Community Request): The primary field is not found exception when primary key column name is different from Primary attribute property name is different while `Connection.Update` is called. [#356](https://github.com/mikependon/RepoDb/issues/356)
- Bug: Set the return value of `Max` and `Min` operations to object. [#357](https://github.com/mikependon/RepoDb/issues/357)
- Performance: Refactor to remove the recurrent calls to `DbFieldCache`.Get() in the ExecuteQuery method.


#### RepoDb (v1.10.2-alpha2)

- Bug: Set the return value of `Max` and `Min` operations to object. [#357](https://github.com/mikependon/RepoDb/issues/357)
- Refactor to remove the recurrent calls to `DbFieldCache`.Get() in the ExecuteQuery method.


#### RepoDb (v1.10.2-alpha1)

- Bug (Community Request): The primary field is not found exception when primary key column name is different from Primary attribute property name is different while `Connection.Update` is called. [#356](https://github.com/mikependon/RepoDb/issues/356)


#### RepoDb (v1.10.1)

- Removed the NetFramework Specific Library at Nuget.
- Supported the RepoDb.MySql extended library.


#### RepoDb (v1.10.0)

- Breaking changes from the previous versions.
- This release contains major code refactoring for performance optimization purposes.
- Feature: Introduced the support `IDbSetting`. Used for other data providers extensibility complete support.
- Feature: Introduced `DbSettingMapper` class.
- Feature: Supported `Upsert` operations (abstracted with [Merge](/operation/merge) operation) for the data providers that does not support multi-statement execution and `MERGE` command.
- Feature: Added support to `Average` and `AverageAll` (with its Async version).
- Feature: Added support to `Exists` (with its Async version).
- Feature: Added support to `Max` and `MaxAll` (with its Async version).
- Feature: Added support to `Min` and `MinAll` (with its Async version).
- Feature: Added support to `Sum` and `SumAll` (with its Async version).
- Feature: Create an extended method `DbConnection.GetLastInsertedIdentity()` for other data providers extensibility complete support.
- Enhancement: Introduced `QueryGroup.IsForUpdate()` method.
- Enhancement: Splitted the `SqlServerStatementBuilder` to a `BaseStatementBuilder`. Further supports for other data providers base statement building.
- Enhancement: Supported `IResolver` for 7 generic types.
- Refactoring: Renamed the `SqlDbHelper` to `SqlServerDbHelper`.
- Refactoring: Renamed the `SqlStatementBuilder` to `SqlServerStatementBuilder`.
- Test: Added an extensive Unit/Integration Tests for `Average`, Exists, `Max`, `Min`, `Sum` (and all its Async operations).
- Test: Added an extensive Unit Tests for `QueryGroup.GetString()` and `QueryField.GetString()`.
- Bug: Where expression using IEnumerable`s contains throws error when using inherited class property [#301](https://github.com/mikependon/RepoDb/issues/301)


#### RepoDb (v1.9.11)

- Stable release.
- Enhancement: Check current transaction (System.Transactions.Transaction.Current) before applying an explicit System.Data.`IDbTransaction` object. [#279](https://github.com/mikependon/RepoDb/issues/279)
- Enhancement: Table Hints is not passing properly in the BatchQuery. [#283](https://github.com/mikependon/RepoDb/issues/283)


#### RepoDb (v1.9.10)

- Stable release.
- v1.9.9 changes + more Integration Tests for DbConnection.


#### RepoDb (v1.9.9)

- Bug: `SqlException`: Login failed for user `xxx`
- Bug: Calling [InsertAll](/operation/insertall) with `MapAttribute` leads to `System.NullReferenceException`: Object reference not set to an instance of an object. [#274](https://github.com/mikependon/RepoDb/issues/274)
- Bug: `SqlDbHelper.GetFields` is failing if the transaction object is present. [#278](https://github.com/mikependon/RepoDb/issues/278)
- Enhancement: Remove database streaming when extracting table schema.
- Enhancement: Add `AsFields()` extended methods for the IEnumerable of DbField objects
#273
- Enhancement: Add `DbFieldCache.GetAsync` method. [#275](https://github.com/mikependon/RepoDb/issues/275)
- Enhancement: Add `DataEntityDataReader` into [BulkInsert](/operation/bulkinsert) that accepts TableName and Entities [#271](https://github.com/mikependon/RepoDb/issues/271)
- Enhancement: Add `AsFields()` extended methods for the IEnumerable of DbField objects.
- To Consider: Enable [BulkInsert](/operation/bulkinsert) with System.`DataTable` [#277](https://github.com/mikependon/RepoDb/issues/277)
- Test: Add IntegrationTests on [BulkInsert](/operation/bulkinsert) that accepts DataEntities and TableName.
- Refactoring: Refactor the codes for `DbFieldCache` to accept `IDbTransaction` objects. These affected too many chain of calls.
- Refactoring: Removed the `ToLower()` and replaced by `StringComparer.OrdinalIgnoreCase`.


#### RepoDb (v1.9.9-beta5)

- Bug: `SqlDbHelper.GetFields` is failing if the transaction object is present. [#278](https://github.com/mikependon/RepoDb/issues/278)
- Refactoring: Refactor the codes for `DbFieldCache` to accept `IDbTransaction` objects. These affected too many chain of calls.


#### RepoDb (v1.9.9-beta4)

- To Consider: Enable [BulkInsert](/operation/bulkinsert) with System.`DataTable` [#277](https://github.com/mikependon/RepoDb/issues/277)
- v1.9.9 release candidate


#### RepoDb (v1.9.9-beta3)

- Enhancement: Add `DbFieldCache.GetAsync` method. [#275](https://github.com/mikependon/RepoDb/issues/275)
- Enhancement: Add `DataEntityDataReader` into [BulkInsert](/operation/bulkinsert) that accepts TableName and Entities [#271](https://github.com/mikependon/RepoDb/issues/271)
- Enhancement: Add `AsFields()` extended methods for the `IEnumerable<DbField>`
- Test: Add IntegrationTests on [BulkInsert](/operation/bulkinsert) that accepts DataEntities and TableName.


#### RepoDb (v1.9.9-beta2)

- Enhancement: Add `AsFields()` extended methods for the `IEnumerable<DbField>` [#273](https://github.com/mikependon/RepoDb/issues/273)
- Bug: Calling [InsertAll](/operation/insertall) with `MapAttribute` leads to `System.NullReferenceException`: Object reference not set to an instance of an object. [#274](https://github.com/mikependon/RepoDb/issues/274)


#### RepoDb (v1.9.9-beta1)

- Bug: SqlException: Login failed for user `xxx`
- Enhancement: Remove database streaming when extracting table schema.


#### RepoDb (v1.9.8)

- Stable release of RepoDb.
- Enum Supports.
- Unorganized Table/Column Supports.
- Bug Fixes.


#### RepoDb (v1.9.8-beta4)

- Title: A hybrid ORM library for .NET.
- Fix: Fixed the issue in the `QueryGroup.Fix()` method if the depth of the Tree is at Level 4.


#### RepoDb (v1.9.8-beta3)

- Fixed: Fixed the issue when using the Mapped/Quoted/Unorganized property in QueryGroup.Parse() method.


#### RepoDb (v1.9.8-beta2)

- Fixed: Failing operations on a purposely quoted class property. Reported by Christian Franck.


#### RepoDb (v1.9.8-beta1)
     
- Requested: Document Enum Support [#261](https://github.com/mikependon/RepoDb/issues/261)
- Bug: Operations are failing on the Unorganized Table - by Christian Franck [#262](https://github.com/mikependon/RepoDb/issues/262)


#### RepoDb (v1.9.7)

- Stable release with complete feature sets.
- Added netstandard2.0 as the default target framework at Nuget Package.


#### RepoDb (v1.9.6)

- [Note] This version is the first ever version release with complete features. The next version will be only be bug-fixes from this version (if we found some).
- [Core] Optimize the Expression field iteration between the number of Class properties and `DbDataReader` columns. [#136](https://github.com/mikependon/RepoDb/issues/136)
- [Core] Add a pluggable interface/abstract for DbProviderOperation [#158](https://github.com/mikependon/RepoDb/issues/158)
- [Core] Add a DbTransaction object on [InsertAll](/operation/insertall), [MergeAll](/operation/mergeall), [UpdateAll](/operation/updateall) operations. [#213](https://github.com/mikependon/RepoDb/issues/213)
- [Test] Added an Integration Tests for IdentityClassWithDifferentIdentity.
- [Test] Add IntegrationTest for [UpdateAll](/operation/updateall) targeting certain columns. [#245](https://github.com/mikependon/RepoDb/issues/245)
- [Test] Add an Async method support to QueryMultipleExtractor.Extract. [#255](https://github.com/mikependon/RepoDb/issues/255)
- [Test] Add an Async method support to QueryMultipleExtractor.Scalar. [#256](https://github.com/mikependon/RepoDb/issues/256)
- [Test] Introduce UnitTest for IDbOperationProvider Interface. [#253](https://github.com/mikependon/RepoDb/issues/253)
- [Test] Create a test for `IStatementBuilder` for all Async operations for DbConnection, DbRepository, `BaseRepository`. [#218](https://github.com/mikependon/RepoDb/issues/218)
- [Test] Add more extensive UnitTest for IN, NOT IN, BETWEEN, NOT BETWEEN, LIKE and NOT LIKE operations. [#186](https://github.com/mikependon/RepoDb/issues/186)
- [Test] Create a much more extensive UnitTests for all Operations (Equal, NotEqual, Between ... ) [#178](https://github.com/mikependon/RepoDb/issues/178)
- [Test] Create an extensive IntegrationTests for IN, NOT IN, BETWEEN, NOT BETWEEN, LIKE and NOT LIKE operations. [#180](https://github.com/mikependon/RepoDb/issues/180)
- [Test] Create IntegrationTest for DbTransaction (Insert, Merge, Delete, Update) [#181](https://github.com/mikependon/RepoDb/issues/181)
- [Added] Introduce SqlDbOperationProvider Class. [#251](https://github.com/mikependon/RepoDb/issues/251)
- [Added] Introduce DbOperationProviderMapper Class. [#252](https://github.com/mikependon/RepoDb/issues/252)
- [Added] Introduce IDbOperationProvider Interface. [#250](https://github.com/mikependon/RepoDb/issues/250)
- [Adhoc] Add `BatchSize` in [BulkInsert](/operation/bulkinsert). [#257](https://github.com/mikependon/RepoDb/issues/257)
- [Adhoc] Remove all `obsolete` methods for v1.9.6. [#246](https://github.com/mikependon/RepoDb/issues/246)
- [Minor] Hide the exposed extended internal methods. [#244](https://github.com/mikependon/RepoDb/issues/244)


#### RepoDb (v1.9.5)

- [Note] This version is the first ever version release with complete features. The next version will be only be bug-fixes from this version (if we found some).
- [Core] Introduce `UpdateMultiple`, different from [UpdateAll](/operation/updateall). [#94](https://github.com/mikependon/RepoDb/issues/94)
- [Core] Introduce the [UpdateAll](/operation/updateall) via ``PrimaryKey``. [#93](https://github.com/mikependon/RepoDb/issues/93)
- [Core] Introduce Query and QueryAsync Table-Based Calls [#176](https://github.com/mikependon/RepoDb/issues/176)
- [Core] Introduce QueryAll and QueryAllAsync Table-Based Calls [#221](https://github.com/mikependon/RepoDb/issues/221)
- [Core] Introduce BatchQuery, BatchQueryAsync Table-Based Calls [#175](https://github.com/mikependon/RepoDb/issues/175)
- [Core] Create an IL and Compiled Expressions for Update, Merge, Insert and [InsertAll](/operation/insertall) Parameter Passing [#197](https://github.com/mikependon/RepoDb/issues/197)
- [Core] Introduce the `InsertMultiple`, different from [InsertAll](/operation/insertall). [#92](https://github.com/mikependon/RepoDb/issues/92)
- [Core] Add ExecutionContext Cache for Insert. [#205](https://github.com/mikependon/RepoDb/issues/205)
- [Core] Add ExecutionContext Cache for Merge. [#206](https://github.com/mikependon/RepoDb/issues/206)
- [Core] Add ExecutionContext Cache for Update. [#208](https://github.com/mikependon/RepoDb/issues/208)
- [Core] Add ExecutionContext Cache for [UpdateAll](/operation/updateall). [#209](https://github.com/mikependon/RepoDb/issues/209)
- [Core] Add ExecutionContext Cache for [MergeAll](/operation/mergeall). [#207](https://github.com/mikependon/RepoDb/issues/207)
- [Core] Re-enable the operation for UPDATE via DataEntity object. [#188](https://github.com/mikependon/RepoDb/issues/188)
- [Performance] Create a cached Expression when setting back the `PrimaryKey` values in Insert and [InsertAll](/operation/insertall). [#211](https://github.com/mikependon/RepoDb/issues/211)
- [Test] Add extensive IntegrationTests for type CRUD via TableName Calls. [#222](https://github.com/mikependon/RepoDb/issues/222)
- [Test] Insert via TableName using Dynamic object with Invalid Column Casing [#223](https://github.com/mikependon/RepoDb/issues/223)
- [Test] Add more IntegrationTest for [UpdateAll](/operation/updateall). [#231](https://github.com/mikependon/RepoDb/issues/231)
- [Test] Add UnitTest for [MergeAll](/operation/mergeall) ITrace object. [#240](https://github.com/mikependon/RepoDb/issues/240)
- [Test] Added more extensive IntegrationTests for [MergeAll](/operation/mergeall) operation. [#242](https://github.com/mikependon/RepoDb/issues/242)
- [Test] Add UnitTest for [UpdateAll](/operation/updateall) (`IStatementBuilder`, ITrace, QueryBuilder) [#230](https://github.com/mikependon/RepoDb/issues/230)
- [Test] Add `UnitTest` for [InsertAll](/operation/insertall). [#214](https://github.com/mikependon/RepoDb/issues/214)
- [Bug] The version 1.9.2 is an (untagged) pre-release version. The Id property is failing on Insert if the Data Type is not long [#215](https://github.com/mikependon/RepoDb/issues/215)
- [Bug] Insert via TableName using Dynamic object with Invalid Column Casing [#223](https://github.com/mikependon/RepoDb/issues/223)
- [Bug] [Core] Insert is failing for non-null (non-table) properties (XJNEN) [#212](https://github.com/mikependon/RepoDb/issues/212)
- [Bug] DbHelper: Script execution security related issue in the company (XJNEN) [#229](https://github.com/mikependon/RepoDb/issues/229)
- [Bug] Expression based using the `ToList().Contains()` is failing. But the `ToArray().Contains()` is working. XJNEN [#233](https://github.com/mikependon/RepoDb/issues/233)
- [Known-Bug] Error is thrown when calling the [UpdateAll](/operation/updateall) with dynamic entities (and the fields are not equals to the DB fields) and the `qualifiers` were not defined. [#232](https://github.com/mikependon/RepoDb/issues/232)
- [Adhoc] Call the `AsList()` method when calling the `QueryGroup.GetString()` method. Support the case of XJNEN [#228](https://github.com/mikependon/RepoDb/issues/228)
- [Adhoc] Reviewed `BaseRepository`, DbRepository, DbConnection Async/Await Keywords
- [Adhoc] Reviewed UnitTests for the ITrace (`BaseRepository`, DbRepository, DbConnection)
- [Adhoc] Convert all Mappers to become a Resolvers by implementing the IResolver interface. [#227](https://github.com/mikependon/RepoDb/issues/227)
- [Adhoc] Remove all MapItem Classes from the Mappers. [#226](https://github.com/mikependon/RepoDb/issues/226)
- [Removed] TypeMapItem Class
- [Removed] DbIdentityCache Class


#### RepoDb (v1.9.4)

- [Note] This version is the first ever version release with complete features. The next version will be only be bug-fixes from this version (if we found some).
- [Core] Introduce `UpdateMultiple`, different from [UpdateAll](/operation/updateall). [#94](https://github.com/mikependon/RepoDb/issues/94)
- [Core] Introduce the [UpdateAll](/operation/updateall) via ``PrimaryKey``. [#93](https://github.com/mikependon/RepoDb/issues/93)
- [Core] Introduce Query and QueryAsync Table-Based Calls [#176](https://github.com/mikependon/RepoDb/issues/176)
- [Core] Introduce QueryAll and QueryAllAsync Table-Based Calls [#221](https://github.com/mikependon/RepoDb/issues/221)
- [Core] Introduce BatchQuery, BatchQueryAsync Table-Based Calls [#175](https://github.com/mikependon/RepoDb/issues/175)
- [Core] Create an IL and Compiled Expressions for Update, Merge, Insert and [InsertAll](/operation/insertall) Parameter Passing [#197](https://github.com/mikependon/RepoDb/issues/197)
- [Core] Introduce the `InsertMultiple`, different from [InsertAll](/operation/insertall). [#92](https://github.com/mikependon/RepoDb/issues/92)
- [Core] Add ExecutionContext Cache for Insert. [#205](https://github.com/mikependon/RepoDb/issues/205)
- [Core] Add ExecutionContext Cache for Merge. [#206](https://github.com/mikependon/RepoDb/issues/206)
- [Core] Add ExecutionContext Cache for Update. [#208](https://github.com/mikependon/RepoDb/issues/208)
- [Core] Add ExecutionContext Cache for [UpdateAll](/operation/updateall). [#209](https://github.com/mikependon/RepoDb/issues/209)
- [Core] Add ExecutionContext Cache for [MergeAll](/operation/mergeall). [#207](https://github.com/mikependon/RepoDb/issues/207)
- [Core] Re-enable the operation for UPDATE via DataEntity object. [#188](https://github.com/mikependon/RepoDb/issues/188)
- [Performance] Create a cached Expression when setting back the `PrimaryKey` values in Insert and [InsertAll](/operation/insertall). [#211](https://github.com/mikependon/RepoDb/issues/211)
- [Test] Add extensive IntegrationTests for type CRUD via TableName Calls. [#222](https://github.com/mikependon/RepoDb/issues/222)
- [Test] Insert via TableName using Dynamic object with Invalid Column Casing [#223](https://github.com/mikependon/RepoDb/issues/223)
- [Test] Add more IntegrationTest for [UpdateAll](/operation/updateall). [#231](https://github.com/mikependon/RepoDb/issues/231)
- [Test] Add UnitTest for [MergeAll](/operation/mergeall) ITrace object. [#240](https://github.com/mikependon/RepoDb/issues/240)
- [Test] Added more extensive IntegrationTests for [MergeAll](/operation/mergeall) operation. [#242](https://github.com/mikependon/RepoDb/issues/242)
- [Test] Add UnitTest for [UpdateAll](/operation/updateall) (`IStatementBuilder`, ITrace, QueryBuilder) [#230](https://github.com/mikependon/RepoDb/issues/230)
- [Test] Add `UnitTest` for [InsertAll](/operation/insertall). [#214](https://github.com/mikependon/RepoDb/issues/214)
- [Bug] The version 1.9.2 is an (untagged) pre-release version. The Id property is failing on Insert if the Data Type is not long [#215](https://github.com/mikependon/RepoDb/issues/215)
- [Bug] Insert via TableName using Dynamic object with Invalid Column Casing [#223](https://github.com/mikependon/RepoDb/issues/223)
- [Bug] [Core] Insert is failing for non-null (non-table) properties (XJNEN) [#212](https://github.com/mikependon/RepoDb/issues/212)
- [Bug] DbHelper: Script execution security related issue in the company (XJNEN) [#229](https://github.com/mikependon/RepoDb/issues/229)
- [Bug] Expression based using the `ToList().Contains()` is failing. But the `ToArray().Contains()` is working. XJNEN [#233](https://github.com/mikependon/RepoDb/issues/233)
- [Adhoc] Call the `AsList()` method when calling the `QueryGroup.GetString()` method. Support the case of XJNEN [#228](https://github.com/mikependon/RepoDb/issues/228)
- [Adhoc] Reviewed `BaseRepository`, DbRepository, DbConnection Async/Await Keywords
- [Adhoc] Reviewed UnitTests for the ITrace (`BaseRepository`, DbRepository, DbConnection)
- [Adhoc] Convert all Mappers to become a Resolvers by implementing the IResolver interface. [#227](https://github.com/mikependon/RepoDb/issues/227)
- [Adhoc] Remove all MapItem Classes from the Mappers. [#226](https://github.com/mikependon/RepoDb/issues/226)
- [Removed] TypeMapItem Class
- [Removed] DbIdentityCache Class


#### RepoDb (v1.9.3)

- [Core] Introduce ExecutionContext to hold the caching of the execution for [InsertAll](/operation/insertall). [#204](https://github.com/mikependon/RepoDb/issues/204)
- [Enhancement, Performance] [InsertAll](/operation/insertall) Compiled Expressions
- [Enhancement] Revisits the DbField for Primary and Identity Columns. [#182](https://github.com/mikependon/RepoDb/issues/182)
- [Enhancement] Add the IdentityDbField in both Merge and Update SqlStatementBuilder. [#198](https://github.com/mikependon/RepoDb/issues/198)
- [Enhancement] Rename the DataReaderConverter to DataReader. [#200](https://github.com/mikependon/RepoDb/issues/200)
- [Enhancement] Add Flush method in the CommandTextCache, and exposed the class. [#220](https://github.com/mikependon/RepoDb/issues/220)
- [Performance] Create a cached Expression when setting back the `PrimaryKey` values in Insert and [InsertAll](/operation/insertall). [#211](https://github.com/mikependon/RepoDb/issues/211)
- [Performance] Use the compiled function for inserting single DataEntity if the BatchSize is 1 for [InsertAll](/operation/insertall). [#217](https://github.com/mikependon/RepoDb/issues/217)
- [Fixed] Remove the Insert and [InsertAll](/operation/insertall) identity conversion from SQL. [#203](https://github.com/mikependon/RepoDb/issues/203) - Fixed by introducing the `ConversionResolver` classes.
- [Test] Create UnitTests for (DbConnection, DbRepository, `BaseRepository`) [InsertAll](/operation/insertall) operation for `IStatementBuilder`. [#216](https://github.com/mikependon/RepoDb/issues/216)
- [Test] Add UnitTests for ClientTypeToSqlDbTypeResolver, SqlDbTypeNameToClientTypeResolver and SqlDbTypeToStringNameResolver [#219](https://github.com/mikependon/RepoDb/issues/219)
- [Added] IConversionResolver
- [Added] ClientTypeToSqlDbTypeResolver
- [Added] SqlDbTypeNameToClientTypeResolver
- [Added] SqlDbTypeToStringNameResolver
- [Added] SqlVariant
- [Added] ParameterField
- [Added] InsertExecutionContext
- [Added] InsertExecutionContextCache
- [Added] InsertAllExecutionContext
- [Added] InsertAllExecutionContextCache


#### RepoDb (v1.9.2)

- [Core] Introduce the [InsertAll](/operation/insertall), different from `InsertMultiple`. [#91](https://github.com/mikependon/RepoDb/issues/91)
- [Core] Return the number of affected rows during [BulkInsert](/operation/bulkinsert) for `DbDataReader`. [#192](https://github.com/mikependon/RepoDb/issues/192)
- [Core] Add a `SetParameters` extended methods for `DbCommand` object. [#196](https://github.com/mikependon/RepoDb/issues/196)
- [Core, Requested] Re-enable the operation for DELETE via DataEntity object. [#185](https://github.com/mikependon/RepoDb/issues/185)
- [Core, Requested] Re-enable the operation for UPDATE via DataEntity object. [#188](https://github.com/mikependon/RepoDb/issues/188)
- [Core, Requested] Add support to exclude non-table Class Properties in all Operations. [#189](https://github.com/mikependon/RepoDb/issues/189)
- [Adhoc, Requested] Expose the `DataEntityDataReader` Class. [#172](https://github.com/mikependon/RepoDb/issues/172)
- [Adhoc, Requested] Expose the QueryGroup.Fix() method. [#179](https://github.com/mikependon/RepoDb/issues/179)
- [Adhoc] Revisit the `async Task` in all `DbRepository` methods. [#190](https://github.com/mikependon/RepoDb/issues/190)
- [Adhoc] Add a new repository method named ‘EnsureOpenAsync’. [#111](https://github.com/mikependon/RepoDb/issues/111)
- [Test] Add an extensive IntegrationTests for the Entity with Extra Fields. [#183](https://github.com/mikependon/RepoDb/issues/183)
- [Test] Add all UnitTests for Trace call for all Async operations. [#193](https://github.com/mikependon/RepoDb/issues/193)
- [Test] Add UnitTest for [InsertAll](/operation/insertall). [#194](https://github.com/mikependon/RepoDb/issues/194)
- [Test] Add IntegrationTests for [InsertAll](/operation/insertall). [#195](https://github.com/mikependon/RepoDb/issues/195)
- [Test] Add more extensive UnitTest for IN, NOT IN, BETWEEN, NOT BETWEEN, LIKE and NOT LIKE operations. [#186](https://github.com/mikependon/RepoDb/issues/186)
- [Test] Add extensive IntegrationTests for ConnectionPersistency. [#191](https://github.com/mikependon/RepoDb/issues/191)
- [Enhancement] Auto-set the `PrimaryField` value of the object after calling the `Insert<T>` operation. [#187](https://github.com/mikependon/RepoDb/issues/187)


#### RepoDb (v1.9.1)

- Stable Release of RepoDb.
- [Update] Exposed BulkCopyOptions and Transaction to [BulkInsert](/operation/bulkinsert) Operation.


#### RepoDb (v1.9.0)

- [Core] Introduced table-based operations for `Count`, `CountAll`, [Delete](/operation/delete), [DeleteAll](/operation/deleteall), [Insert](/operation/insert), [Merge](/operation/merge), `Truncate` and [Update](/operation/update).
- [Core] Introduced `CountAll` and [QueryAll](/operation/queryall) operations.
- [Core] Introduced pluggable interface `DbHelpers`.
- [Core] Updated the `QueryBuilder` to remove the `TEntity` at class level (moved it to method level).
- [Core] Updated the `StatementBuilder` SQL Text generation via TableName.
- [Core] Supported `Dynamic`, `ExpandoObject`, `IDictionary<string, object>`, `QueryField`, `IEnumerable<QueryField>` and `QueryGroup` objects in the `Execute<Method>` calls.
- [Added] ExecuteScalar<T> and ExecuteScalarAsync<T>
- [Added] DbField
- [Added] `DbFieldCache`
- [Removed] AsyncResultExtractor
- [Removed] StatementBuilderMapItem
- [Removed] FieldDefinition
- [Removed] FieldDefinitionCache
- [Renamed] SqlHelper to SqlDbHelper
- [Renamed] PrimaryKeyCache to PrimaryCache`.
- [Renamed] PrimaryKeyIdentityCache to PrimaryIdentityCache
- [Optimization] CountAsync operation is now returning the `Task<int>` type instead of `Task<object>`.
- [Optimization] Allow passing of cache expiration time during [Query](/operation/query) and [QueryAll](/operation/queryall) calls.
- [Optimization, Pull-Request] Enumerations `Order`, `Conjunction` and `Operation` is now a pre-generated `int` values.
- Introduced complete sets of IntegrationTests and UnitTests in all of the changes mentioned above.
- See v1.9.0-beta1 and v1.9.0-beta2 for complete set of changes.


#### RepoDb (v1.9.0-beta2)

- [Core] Make the DbHelper Pluggable by Provider. [#137](https://github.com/mikependon/RepoDb/issues/137)
- [Core] [NTH]: Support InlineDelete(string tableName, object where ...) [#113](https://github.com/mikependon/RepoDb/issues/113) -- Moved to Delete
- [Core] Add table based operations for Count, Delete, [DeleteAll](/operation/deleteall), Insert, Merge, Truncate, Update. [#159](https://github.com/mikependon/RepoDb/issues/159)
- [Core] Organize the ordering of the arguments in all operations. [#160](https://github.com/mikependon/RepoDb/issues/160)
- [Core] Introduce CountAll operation. [#161](https://github.com/mikependon/RepoDb/issues/161)
- [Enhancement] Revisits the NET Framework custom method for GetCustomAttributes<T>. [#133](https://github.com/mikependon/RepoDb/issues/133)
- [Enhancement] Support passing of QueryGroup, QueryField, IEnumerable<QueryField> in the Execution<Method> Operations [#140](https://github.com/mikependon/RepoDb/issues/140)
- [Enhancement] Remove the `StatementBuilderMapItem` class if possible. [#139](https://github.com/mikependon/RepoDb/issues/139)
- [Enhancement] Remove the `Provider` Enumeration. [#138](https://github.com/mikependon/RepoDb/issues/138)
- [Enhancement] Reorder the parameter of for target table to [BulkInsert](/operation/bulkinsert)(TableName, `DbDataReader`). [#145](https://github.com/mikependon/RepoDb/issues/145)
- [Enhancement] Properly parse the type of the passed parameters when parsing the RawSql Array Parameters. [#147](https://github.com/mikependon/RepoDb/issues/147)
- [Enhancement] [NTH]: Support InlineInsert(string tableName, object param ...) [#114](https://github.com/mikependon/RepoDb/issues/114) -- Moved to Insert
- [Enhancement] Implement the `prefix` as a passable argument when during conversion. [#148](https://github.com/mikependon/RepoDb/issues/148)
- [Enhancement] Add additional argument in the DataReaderConverter.ToEnumerable<T> method to support the calls to DbHelper.Get<T> method. [#142](https://github.com/mikependon/RepoDb/issues/142)
- [Enhancement] Add a caching mechanism for type `Field` objects. [#157](https://github.com/mikependon/RepoDb/issues/157)
- [Enhancement] Merge all inline operations into main operations (InlineMerge should be Merge). [#156](https://github.com/mikependon/RepoDb/issues/156)
- [Adhoc] Created a new cache class named `DbIdentityCache`.
- [Adhoc] Renamed the `FieldDefinition` into `DbField`.
- [Adhoc] Renamed the `FieldDefinitionCache` to `DbFieldCache`.
- [Adhoc] Renamed the `SqlHelper` to `SqlDbHelper`.
- [Adhoc] Renamed the `PrimaryKeyCache` to `PrimaryCache`.
- [Adhoc] Renamed the `PrimaryKeyIdentityCache` to `PrimaryIdentityCache`.
- [Adhoc] Moved the `DbHelpers` into `RepoDb.`DbHelpers`.
- [Adhoc, Optimization] Made the `DbFieldCache` object more dynamic to any DB providers.
- [Test] Add additional logic in the Integration Tests of the `IStatementBuilder`, verify that the second call is not hitting the method. [#141](https://github.com/mikependon/RepoDb/issues/141)
- [Test] Add an extensive Unit/Integration Test for InlineInsert that targets a table name. [#146](https://github.com/mikependon/RepoDb/issues/146)
- [Test] Create UnitTests for CountAll. [#163](https://github.com/mikependon/RepoDb/issues/163)
- [Test] Add separate `IntegrationTests` for the RawSql ArrayValues [#168](https://github.com/mikependon/RepoDb/issues/168)
- [Test] Add separate `IntegrationTests` for different Execute<Method> parameters. [#169](https://github.com/mikependon/RepoDb/issues/169)
- [Test] Create IntegrationTests for QueryAll. [#167](https://github.com/mikependon/RepoDb/issues/167)
- [Test] Create IntegrationTests for CountAll. [#164](https://github.com/mikependon/RepoDb/issues/164)
- [Documentation] Document the CountAll operation. [#162](https://github.com/mikependon/RepoDb/issues/162)
- [Documentation] Add a separate documentation entry for Execute<Method> with Array parameters argument. [#143](https://github.com/mikependon/RepoDb/issues/143)


#### RepoDb (v1.9.0-beta1)

- [New] Updated the description to "A dynamic, lightweight, efficient and very fast Hybrid ORM library for .NET.".
- [New] Re-instate the row deletion by entity level (connection.Delete<T>(T entity)) [#112](https://github.com/mikependon/RepoDb/issues/112)
- [Bug] Query using Expression is failing for `Contains()` == `false` [#102](https://github.com/mikependon/RepoDb/issues/102)
- [Test] Create an Integration Tests for a model with much columns that the result of the [ExecuteQuery](/operation/executequery) [#103](https://github.com/mikependon/RepoDb/issues/103)
- [Test] Add Integration Tests for Execute<Methods> with IDictionary and ExpandoObject parameters. [#123](https://github.com/mikependon/RepoDb/issues/123)
- [Test] Add an intensive UnitTests for Cache ExpirationInMinutes in the DbConnection, DbRepository and `BaseRepository`. [#129](https://github.com/mikependon/RepoDb/issues/129)
- [Test] Add major Integration Tests for Caching for SqlConnection, DbRepository, `BaseRepository`. [#130](https://github.com/mikependon/RepoDb/issues/130)
- [Test] Add IntegrationTests for Insert<TEntity, TResult> and InlineInsert<TEntity, TResult> [#132](https://github.com/mikependon/RepoDb/issues/132)
- [Enhancement, Major] Remove the TEntity in QueryBuilder, implement it like this QueryBuilder (instead of QueryBuilder<TEntity>) [#115](https://github.com/mikependon/RepoDb/issues/115)
- [Enhancement, Major] Remove the AsyncResultExtractor class. [#124](https://github.com/mikependon/RepoDb/issues/124)
- [Enhancement] Short datatype used for Enums [#107](https://github.com/mikependon/RepoDb/issues/107)
- [Enhancement] Remove the internal StringConstants class. [#117](https://github.com/mikependon/RepoDb/issues/117)
- [Enhancement] Make the `CountAsync` operation return type `long` instead of `object` without affecting the parallelism. [#108](https://github.com/mikependon/RepoDb/issues/108)
- [Enhancement] Add a new operational method ExecuteScalar<T> (and ExecuteScalarAsync<T>). [#121](https://github.com/mikependon/RepoDb/issues/121)
- [Enhancement] Support passing of IDictionary<string, object> as `param` in the Execute<Methods> operation. [#125](https://github.com/mikependon/RepoDb/issues/125)
- [Enhancement] Allow passing of cache expiration time during creation and in the repository. [#128](https://github.com/mikependon/RepoDb/issues/128)

#### RepoDb (v1.8.4)

- Stable Spring Release of RepoDb. Enjoy!


#### RepoDb (v1.8.3)

- Stable Spring Release of RepoDb. Enjoy!

#### RepoDb (v1.8.2)

##### Updates

- Updated to again support the `BatchQuery` dynamic expression (first-level properties).
- Updated to again support the `Count` dynamic expression (first-level properties).
- Updated to again support the `whereOrPrimaryKey` argument rather than `primaryKey` (first-level properties).

##### Added

- Additional Integration Tests for class/object mappings (CRUD).
- Additional Integration Tests for the `Dynamics` and `Expressions` query expression for `DbConnection`, `DbRepository` and ``BaseRepository``.


#### RepoDb (v1.8.1)

#### New Features

- New: Introduced the [ExecuteQueryMultiple](/operation/executequerymultiple) into the `DbRepository`.
- New: Introduced the `QueryMultiple` into the `DbRepository`.
- New: Introduced the `ConversionType` to let the users decide the type of the conversions. Accessible via `TypeMapper.ConversionType` property.

#### Optimizations

- Optimization: Added a class-level checking when identifying the ``PrimaryKey`` of a class.
- Optimization: Supported the type conversions(if convertible) when querying the data from the database.
- Optimization: Simplified the extraction of the result of the [ExecuteQueryMultiple](/operation/executequerymultiple) via `Extract` method.

##### Added

- Added: A class named `AsyncResultExtractor` to control the extraction of the result in the caller side for all `Async` methods of the ``BaseRepository`/DbRepository`.
- Added: A class named `QueryMultipleRequest` to handle the request when calling the `QueryMultiple` operation.
- Added: A class named `CommandArrayParameter` to hold the array parameters for Raw SQL statements.
- Added: A method named `Reset` for both `QueryField` and `QueryGroup` objects  allow the user to reinstate the instance of that object.
- Added: An operation named [BulkInsert](/operation/bulkinsert) at  `DbRepository` with `DbDataReader` as the parameter.
- Added: An argument named `hints` for both the `BatchQuery` and `Count` operation.
- Added: An intensive `IntegrationTests` for all the different data types of the database (SQL Server) (Numbers, Spatials, Strings, Dates, Bytes, Others).
- Added: An intensive `IntegrationTests` for the data types conversions(i.e: String-to-Numbers (vice versa), String-to-Dates (vice versa), etc).
- Added: An intensive `IntegrationTests` for the invalid casings.
- Added: An intensive `IntegrationTests` for all the operations of the `DbConnection`, `DbRepository` and ``BaseRepository`` objects.

#### Fixes

- Fixed: An issue in the `InlineMerge` if the primary key is not defined in the dynamic object.
- Fixed: An issue in the [Merge](/operation/merge) if the primary key is an identity.
- Fixed: An issue in the `QueryMultiple` if the same expression is are used multiple times.


#### RepoDb (v1.8.1-beta1)

#### New Features

- Introduced the [ExecuteQueryMultiple](/operation/executequerymultiple) into the `DbRepository`.
- Introduced the `QueryMultiple` into the `DbRepository`.
- Introduced the `ConversionType` to let the users decide the strictness of the conversions. Accesible via `TypeMapper.ConversionType` property.

##### Added

- In the `Async` methods, introduced the `AsyncResultExtractor` class to control the extraction of the result in the caller side.
- Supported [BulkInsert](/operation/bulkinsert) for `DbRepository` with `DbDataReader` as the parameter.
- Added an `IntegrationTests` for all the different data types of the database (SQL Server) (Numbers, Spatials, Strings, Dates, Bytes, Others).
- Added an `IntegrationTests` for the data types conversions(i.e: String-to-Numbers (vice versa), String-to-Dates (vice versa), etc).
- Added an `IntegrationTests` for the invalid casings.

#### Optimizations

- Added a class-level checking when identifying the ``PrimaryKey`` of a class.
- Supported the type conversions(if convertible) when querying the data from the database.

#### Todo for 1.8.1

- Complete sets of `IntegrationTests` for DbConnection, DbRepository, `BaseRepository` operations.
- The version 1.8.1 will be the highest production-grade release of the library.


#### RepoDb (v1.8.0)

#### New Features

- Introduced the support for `DbConnection.QueryMultiple`.
- Introduced the support for `DbConnection.ExecuteQueryMultiple`.

##### Added

- Added a class [QueryMultipleExtractor](/class/querymultipleextractor) to manage the extraction of the result of `DbConnection.ExecuteQueryMultiple` operation.
- Added a class named `CommandParameter` to handle the value of the parameter mapping into the target Type.
- Added a class named `QueryGroupTimeMap` to support the mapping of the QueryGroup into the Type.
- Introduced a `hints` argument in the `DbConnection.Query` operation.
- Added class `SqlTableHints`.
- Supported [BulkInsert](/operation/bulkinsert) for `DbDataReader`.

##### Removed

- Removed the `Operation.Any`.
- Removed the `Operation.All`.
- Removed the `orderBy` and `top` argument in the [Query](/operation/query) method with `primaryKey` argument.
- Removed the `DbConnection.BatchQuery` method with `primaryKey` argument.
- Removed the `DbConnection.Count` method with `primaryKey` argument.
- Removed the `DbConnection.Delete` method without the `where` parameter.
- Removed the support on `dynamic` object query tree expressions.

#### Discontinued

- Removed the attribute `Command`.
- Removed the attribute `Ignored`.

#### Optimizations

- Revisits the way on how to map the `QueryGroup` list of `QueryField` into the data entity type.
- Refactor the Linq (IEnumerable<T>.ToList()) into an old-school (foreach / for (var i..)) approach.


#### RepoDb (v1.8.0-beta5)

- (Major Update) Introduced the support for `DbConnection.QueryMultiple`.
- (Major Update) Introduced the support for `DbConnection.ExecuteQueryMultiple`.
- Added class [QueryMultipleExtractor](/class/querymultipleextractor).


#### RepoDb (v1.8.0-beta4)

- Introduced a `hints` argument in the `DbConnection.Query` operation.
- Added class `SqlTableHints`.


#### RepoDb (v1.8.0-beta3)

- Supported [BulkInsert](/operation/bulkinsert) for `DbDataReader`.
- Removed the `Operation.Any`.
- Removed the `Operation.All`.


#### RepoDb (v1.8.0-beta2)

- Removed the `orderBy` and `top` argument in the [Query](/operation/query) method with `primaryKey` argument.
- Removed the `DbConnection.BatchQuery` method with `primaryKey` argument.
- Removed the `DbConnection.Count` method with `primaryKey` argument.
- Removed the `DbConnection.Delete` method without the `where` parameter.


#### RepoDb (v1.8.0-beta1)

- Remove the support on dynamic query tree expresssions.
- Removed the Command enumeration.
- Removed the Ignored attribute.


#### RepoDb (v1.7.0)
   
- Async Method Calls Optimizations
- Expression-Based Queries
- Removed Support for Recursive Query
- Initial Support for Different Database Providers


#### RepoDb (v1.7.0-beta5)
   
- Fixed the invalid binaries deployed at v1.7.0-beta4.
- Modified `async` operations implementation to leverage the awaitable features.
- Exposed the `IsForProvider` and `GetProvider` DbConnection extended methods.
- Introduced a `BulkInsertMapItem` object to provide ability to override the [BulkInsert](/operation/bulkinsert) operation default mappings.
- Removed the recursive query features (DataEntity, DataEntityChildItemData, DataEntityChildListData, RecursionManager, ForeignAttribute).


#### RepoDb (v1.7.0-beta4)

- Modified `async` operations implementation to leverage the awaitable features.
- Exposed the `IsForProvider` and `GetProvider` DbConnection extended methods.
- Introduced a `BulkInsertMapItem` object to provide ability to override the [BulkInsert](/operation/bulkinsert) operation default mappings.
- Removed the recursive query features (DataEntity, DataEntityChildItemData, DataEntityChildListData, RecursionManager, ForeignAttribute).


#### RepoDb (v1.7.0-beta3)

- Fixed Target Framework Referencing Issues of NuGet Package
- Added a new enumeration for data providers.
- Updated the statement builder mapper to use the Provider object instead of DbConnection type.


#### RepoDb (v1.7.0-beta2)

- Introduced Expression-Based Query for Connection and Repository Operations
- Introduced Support to All (as Operation.All), Any (as Operation.Any), Contains (as Operation.Like, Operation.NotLike, `Operation.In`, Operation.NotIn)
- Introduced an Expression-Based `qualifer` field for [Merge](/operation/merge) and `InlineMerge` operation
- Unit Test updates for `All`, `Any`, and `Contains` operation


#### RepoDb (v1.7.0-beta1)

- Introduced Expression-Based Query for Connection and Repository Operations
- Introduced Expression-Based Parsing for QueryGroup, QueryField, Field, OrderField
- Introduced Array-Based Parameters when calling ExecuteScalar
- Removed Support for DataEntityMapper (Multiple Mapping)
- Bug Fixes: https://github.com/RepoDb/RepoDb/issues?q=is:issue+is:closed


#### RepoDb (v1.6.4)

- Performance: Introduced the field definitions for NULL checks at IL. [#48](https://github.com/mikependon/RepoDb/issues/48)
- Performance: Added a compiled-functions (Expression) when converting the DataReader to Entity. FransBouma Recommendation #47


#### RepoDb (v1.6.2)

- Performance: Introduced the field definitions for NULL checks at IL. [#48](https://github.com/mikependon/RepoDb/issues/48)
- Performance: Added a compiled-functions (Expression) when converting the DataReader to Entity. FransBouma Recommendation #47


#### RepoDb (v1.6.0)

- Performance: Introduced the field definitions for NULL checks at IL. [#48](https://github.com/mikependon/RepoDb/issues/48)
- Performance: Added a compiled-functions (Expression) when converting the DataReader to Entity. FransBouma Recommendation #47


#### RepoDb (v1.5.3)

- Optimization: This release has an extreme performance improvement. This is an update at v1.5.2.
- Bug: Fixed the packages.config dependencies issues.


#### RepoDb (v1.5.2)

- Performance Optimization: This release has an extreme performance improvement.


#### RepoDb (v1.5.0)

- Performance Optimization: This release has an extreme performance improvement.


#### RepoDb (v1.3.5)

- Refactor: Removed the DataEntity base class (request from the community)
- Performance: Added a mechanism to cache the command texts
- Bug Fix: Exception is thrown at Repository.Query when cacheKey is present
- Bug Fix: Incorrect milliseconds is being saved for nullable System.DateTime properties


#### RepoDb (v1.3.0)

- Refactor: Removed DataEntity - requested from the community
- Performance: Cached the command texts
- Bug Fix: Exception is thrown at Repository.Query when cacheKey is present
- Bug Fix: Incorrect milliseconds is being saved for nullable System.DateTime properties


#### RepoDb (v1.2.0)

- Stable Release (v1.2.0)


#### RepoDb (v1.2.0-beta5)

- Stabilization: Enabled the recursive query, recursion depth and cyclomatic stack overflow complexity features.


#### RepoDb (v1.2.0-beta4)

- Stabilization: Enabled the Recursive Query feature.


#### RepoDb (v1.2.0-beta3)

- Stabilizing: Added additional validator for query expressions.
- Stabilizing: Enabling the NULL values for Field when composing a query expression.


#### RepoDb (v1.2.0-beta2)

- Stabilizing: Enabled the (Field.Name = (object)null)
- Stabilizing: Enabled the Repository.Query if `PrimaryKey` is not Present


#### RepoDb (v1.2.0-beta1)

- Beta Release 1


#### RepoDb (v1.1.11)

- Supported multi-targetted framework (net40, netstandard1.3, netcore1.1)


#### RepoDb (v1.1.10)

- Candidate for Beta Release


#### RepoDb (v1.1.10-beta1)

- Supported multi-targetted framework (net40, netstandard1.3, netcore1.1)
- Beta release 1 for v1.2.0


#### RepoDb (v1.1.9)

- Introduced the IDbConnection Extended Operational Methods.
- Optimized the DbRepository to use the IDbConnection Extended Operational Methods.
- Removes the DbRepository property at `BaseRepository` class.
- Made the DbRepository and `BaseRepository` Disposable.
- Introduced quoting on Fields and Tables when composing SQL statements (via SqlDbStatementBuilder).
- Documented the ConnectionPersistency, [DeleteAll](/operation/deleteall), InlineInsert, InlineMerge, InlineUpdate and Truncate operations.


#### RepoDb (v1.1.8)

##### Updates for v1.1.7 and v1.1.8

- Supported identity primary key field identification from the database.
- Fixed the parallelism issues by removing the unnecessary cache classes from the library.
- Created a customized DataReader object for the [BulkInsert](/operation/bulkinsert) operation, for future support for the .Net Core.
- Introduced the repository Truncate operation.
- Supported the inline operations for Insert, Merge and Update.
- Added ConnectionPersistency capability for the repositories.
- Built on a lower target framework 4.0 for earlier compatibility.
- Next release is to support the .Net Core.


#### RepoDb (v1.1.7)

- Supported identity primary key field identification from the database.
- Fixed the parallelism issues by removing the unnecessary cache classes from the library.
- Created a customized DataReader object for the [BulkInsert](/operation/bulkinsert) operation, for future support for the .Net Core.
- Introduced the repository Truncate operation.
- Supported the inline operations for Insert, Merge and Update.
- Added ConnectionPersistency capability for the repositories.
- Built on a lower target framework 4.0 for earlier compatibility.
- Next release is to support the .Net Core.


#### RepoDb (v1.1.6)

- Supported identity primary key field identification from the database.
- Fixed the parallelism issues by removing the unnecessary cache objects from the library.
- Created a customized DataReader object for the [BulkInsert](/operation/bulkinsert) operation to support the .Net Core.
- Introduced the Truncate operation.
- Supported the inline operations for Insert, Merge and Update.
- Built on a lower target framework 4.0 for earlier compatibility.
- Next release is to support the .Net Core.


#### RepoDb (v1.1.5)

- Built on a lower target framework 4.0 for earlier compatibility.
- Solution clean-up, removed interfaces that is not unnecessary for extensions.
- Updated the caching by adding the expiration feature in the CacheItem.
- Support identity primary field identification from the database.
- Next release is to support the .Net Core.


#### RepoDb (v1.1.4)

- Built on a lower target framework 4.0 for earlier compatibility.
- Solution clean-up, removed interfaces that is not unnecessary for extensions.
- Updated the caching by adding the expiration feature in the CacheItem.
- Next release is to support the .Net Core.


#### RepoDb (v1.1.3)

- Built on a target framework 4.0 for earlier compatibility
- Renamed CacheItem.Timestamp to CacheItem.CreatedDate
- Added CacheItem.IsExpired method
- Added CacheItem.Expiration property
- Removed ICacheItem
- Removed ITraceLog
- Removed ICancellableTraceLog
- Removed ITypeMap
- Removed IStatementBuilderMap
- Removed IQueryGroup
- Removed IQueryField
- Removed IParameter
- Removed IField
- Removed IOrderField
- Removed IDataEntityMap
- Removed IQueryBuilder
- Removed IDbRepository
- Removed IBaseRepository


#### RepoDb (v1.1.2)

- Built on a target framework 4.0 for earlier compatibility
- Renamed CacheItem.Timestamp to CacheItem.CreatedDate
- Added CacheItem.IsExpired method
- Added CacheItem.Expiration property
- Removed ICacheItem
- Removed ITraceLog
- Removed ICancellableTraceLog
- Removed ITypeMap
- Removed IStatementBuilderMap
- Removed IQueryGroup
- Removed IQueryField
- Removed IParameter
- Removed IField
- Removed IOrderField
- Removed IDataEntityMap


#### RepoDb (v1.1.1)

- Version 1.1.0


#### RepoDb (v1.1.0)

- Version 1.1.0


#### RepoDb (v1.0.20)

- Version 1.0.20


#### RepoDb (v1.0.19)

- Version 1.0.19


#### RepoDb (v1.0.18-beta01)

##### Updates
- Supported the DbConnection.ExecuteQuery<TEntity> method, to return the IEnumerable list of RepoDb.Interfaces.IDataEntity.
- IL Emit the conversion of System.Data.`DbDataReader` to System.Dynamic.DynamicObject when calling the DbConnection.ExecuteQuery method.


#### RepoDb (v1.0.17-alpha3)

##### Updates

- Created a RepoDb.Reflection.ObjectConverter.
- Supported the DbConnection.ExecuteQuery<TEntity> method, to return the IEnumerable list of RepoDb.Interfaces.IDataEntity.

#### Notes

- Next version will be the Beta version of the library.


#### RepoDb (v1.0.17-alpha02)

##### Updates

- Created a RepoDb.Reflection.ObjectConverter.
- Supported the DbConnection.ExecuteQuery<TEntity> method, to return the IEnumerable list of RepoDb.Interfaces.IDataEntity.

#### Notes

- Next version will be the Alpha version of the library.


#### RepoDb (v1.0.16)

##### Updates

- Created a RepoDb.Reflection.ObjectConverter.
- Supported the DbConnection.ExecuteQuery<TEntity> method, to return the IEnumerable list of RepoDb.Interfaces.IDataEntity.

#### Notes

- Next version will be the Alpha version of the library.


#### RepoDb (v1.0.16-alpha-01)

##### Updates

- Created a RepoDb.Reflection.ObjectConverter.
- Supported the DbConnection.ExecuteQuery<TEntity> method, to return the IEnumerable list of RepoDb.Interfaces.IDataEntity.

#### Notes

- Next version will be the Alpha version of the library.


#### RepoDb (v1.0.15)

- Added RepoDb.Reflection.Delegates.DataReaderToEntityMapperDelegate
- Added RepoDb.Reflection.ConstructorInfoCache
- Added RepoDb.Reflection.DataReaderConverter
- Added RepoDb.Reflection.DelegateCache
- Added RepoDb.Reflection.DelegateFactory
- Added RepoDb.Reflection.MethodInfoCache
- Added RepoDb.Reflection.MethodInfoTypes
- Added RepoDb.Reflection.ReflectionFactory
- Added RepoDb.Reflection.TypeArrayCache
- Added RepoDb.Reflection.TypeCache
- Added RepoDb.Reflection.TypeTypes
- Added RepoDb.Attributes.CreateMethodInfoAttribute
- Returned IDataReader object when using the IDbConnection.ExecuteReader
- Fixed the IL Emitter when mapping DataReader to Null properties
- Added ExecuteQuery to IDbConnection extension
- Added BeforeExecuteQuery and AfterExecuteQuery trace methods
- Supported DataEntityMapper, DataEntityMap
- Supported the DbRepository.Count and DbRepository.CountBig operations
- Support Multi-Mapping for Class-Level


#### RepoDb (v1.0.14)

- Reflection (IL Emitting)


#### RepoDb (v1.0.13)

- Reflection (IL Emitting)


#### RepoDb (v1.0.12)

- Same as RepoDb (v1.0.11)


## RepoDb (v1.0.11)

##### Updates

- Same as RepoDb (v1.0.10)

#### Fixes

- Issue 2: `Operation.In` Exception is Thrown for Array Values
- Issue 1: Class Entity `Map` Attribute `PrimaryKey` Identification


#### RepoDb (v1.0.10)

##### Updates

- Support Column-Based Update using Dynamics (new Repository.InlineUpdate operation)
- Supported BatchQuery Operation
- Supported Field-Level Mappings
- Added the currently used IQueryBuilder on the `IStatementBuilder` methods
- Added QueryBuilderCache object
- Added ExecutionTime (Timespan) property in the TraceLog class
- Added CommandTypeCache object
- Cached the CommadType on every calls
- Removed the Create, Drop, Alter, Execute Command Enum Values (until being supported)
- Added ClassMapNameCache object
- Cache `Map`.Name on every calls
- Added PropertyCache object
- Supported Entity Property Caching
- Added PrimaryPropertyCache object
- Supported Entity Primary Property Caching
- Added ClassMapCache object
- Cached the `Map` attribute on every calls

#### Fixes

- Issue 2: `Operation.In` Exception is Thrown for Array Values
- Issue 1: Class Entity `Map` Attribute `PrimaryKey` Identification


#### RepoDb (v1.0.9)

- Renamed `ICache.Has` to `ICache.Contains`
- Removed `ICache.GetAll` and implement the `IEnumerable` interface instead
- Optimized the mapping for SqlBulkCopy for [BulkInsert](/operation/bulkinsert)


#### RepoDb (v1.0.8)

- Fix the bug found when injecting the `IStatementBuilder` at the `BaseRepository`.
- Renamed argument name `orderFields` to `orderBy`.
- Renamed `ExecuteReaderEx` to [ExecuteReader](/operation/executereader) under Connection object.
- Added `Remove` on the ICache
- Renamed ICache `Set` method to `Add`
- Renamed `DbCacheItem` to `CacheItem`
- Remove the `sealed` keyword at `CacheItem` to make it inheritable


#### RepoDb (v1.0.7)

##### Updates

- Support dynamic query objects for QueryGroup
- MemoryCache (ICache) object
- Trace (ITrace) object
- Added Constant class
- Added support for Operation.Like and Operation.NotLike
- Allow Querying, Deleting, Updating by `PrimaryKey` (when the value is passed in the method of `where` argument)
- Support of Operation.Between and Operation.NotBetween
- Support `Operation.In` and Operation.NotIn
- Optimized Statement Builder (SqlDbStatementBuilder)
- Injectable Statement Builder
- Support TOP and ORDER BY
- Order.Ascending and Order.Descending
- Support StatementBuilderMapper, IStatementBuilderMapper, StatementBuilderMap, StatementBuilderMap
- Support ORDER BY parsing of the dynamic objects
- Added support for the new Operation.Any and Operation.All

##### Removed

- EventNotifier class has been obsolete by the Trace class


#### RepoDb (v1.0.6)

##### Updates

- Support dynamic query objects for QueryGroup
- MemoryCache (ICache) object
- Trace (ITrace) object
- Added Constant class
- Added support for Operation.Like and Operation.NotLike
- Allow Querying, Deleting, Updating by `PrimaryKey` (when the value is passed in the method of `where` argument)
- Support of Operation.Between and Operation.NotBetween
- Support `Operation.In` and Operation.NotIn
- Optimized Statement Builder (SqlDbStatementBuilder)
- Injectable Statement Builder
- Support TOP and ORDER BY
- Order.Ascending and Order.Descending
- Support StatementBuilderMapper, IStatementBuilderMapper, StatementBuilderMap, StatementBuilderMap
- Support ORDER BY parsing of the dynamic objects
- Added support for the new Operation.Any and Operation.All

##### Removed

- EventNotifier class has been obsolete by the Trace class


#### RepoDb (v1.0.5)

##### Updates

- EntityNotBulkInsertableException supported when calling [BulkInsert](/operation/bulkinsert) with non SqlConnection connection object
- [BulkInsert](/operation/bulkinsert) optimization - ordering the columns based on the DB Table ordering (not on DataEntity properties ordering)
- Initial implementation for complex query (QueryGroup)
- Added QueryGroup (IQueryGroup)
- Added Conjuctions for QueryGroup(s)
- Added OrQueryGroup
- Added AndQueryGroup
- Supported IFieldValue interface for QueryField - to support the QueryGroup parameterized approach
- Renamed QueryField.Value to QueryField.Parameter (with Name and Value properties) and change its Type from object to IParameter
- In QueryFields, if the Operation is `=` and the parameter Value is equals to NULL then we compose ([Field] IS NULL) string statement
- Solves the problem of: WHERE (SomeDate IS NULL)
- We also did the same thing for != (SomeDate IS NOT NULL)
- Used the `<>` instead of `!=` in InEquality comparer
- Supported EventNotifier (Tracing and Debugging purposes).
- You can listen on the following events to see the Statement and Object passed before DB executions
- BeforeQueryExecution
- AfterQueryExecution
- BeforeUpdateExecution
- AfterUpdateExecution
- BeforeDeleteExecution
- AfterDeleteExecution
- BeforeMergeExecution
- AfterMergeExecution
- BeforeInsertExecution
- AfterInsertExecution
- BeforeBulkInsertExecution
- AfterBulkInsertExecution
- BeforeExecuteNonQueryExecution
- AfterExecuteNonQueryExecution
- BeforeExecuteReaderExecution
- AfterExecuteReaderExecution
- BeforeExecuteScalarExecution
- AfterExecuteScalarExecution
- CancelledExecution


#### RepoDb (v1.0.4)

- Bug Fix: Error when converting the SqlDataReader data back to the class object when the value is DBNull.Value
- Minor code optimizations and refactoring


#### RepoDb (v1.0.3)

- Bug Fix: Error when converting the SqlDataReader data back to the class object when the value is DBNull.Value
- Minor code optimizations and refactoring


#### RepoDb (v1.0.2)

- Converting DataReader to Object
- Async Calls (Task.Factory.StartNew)
- Added support to auto find (Id) property as prospected `PrimaryKey` if the [Primary] attribute is not on place


#### RepoDb (v1.0.1)

- Initial Release for RepoDb (.Net Extension)
- Supported Dynamic DbConnection
- Supported Mapping Command Type (Entity Level)
- Supported ExecuteScalar, ExecuteNonQuery, ExecuteReader (DataEntity and ExpandoObject)
- Supported Primary Attribute (IsIdentity)
- Supported `BaseRepository` that uses the functionality of the DbRepository
- Supported the GetSqlStatement (Entity Level)
- Supported Object Driven approach when doing a query, delete, update and merge
- Supported Merge Method (Entity Level only)
- Supported Bulk Insert (SqlBulkCopy only)
- Supported to get the Connection at all Repositories
- Supported Transaction Handlers
- Supported Async methods
- Supported DbRepository property at the `BaseRepository`
- Supported Dynamic object returns for ExecuteReaderEx
- Supported code-guards at the DbRepository (Queryable, Insertable, Deletable, Mergeable, Updateable)
- Supported Type Mapper


#### RepoDb (v1.0.0)

- Initial Release for RepoDb (.Net Extension)
- Supported Dynamic DbConnection
- Supported Mapping Command Type (Entity Level)
- Supported Execute Scalar
- Supported Primary Attribute (IsIdentity)
- Supported `BaseRepository` that uses the functionality of the DbRepository
- Supported the GetSqlStatement (Entity Level)
- Supported Object Driven approach when doing a query, delete, update and merge
- Supported Merge Method (Entity Level only)
- Supported Bulk Insert (SqlBulkCopy only)
- Supported to get the Connection at all Repositories
- Supported Transaction Handlers
- Supported Async methods
- Supported DbRepository property at the `BaseRepository`
- Supported Dynamic object returns for ExecuteReaderEx
- Supported code-guards at the DbRepository (Queryable, Insertable, Deletable, Mergeable, Updateable)
- Supported Type Mapper
