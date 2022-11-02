---
layout: default
sidebar: releases
title: MySQL
description: "This page contains the latest information of the releases of RepoDb.MySql library."
nav_order: 6
permalink: /release/mysql
parent: RELEASES
---

# Releases for RepoDb.MySql

---

Please click [here](https://www.nuget.org/packages/RepoDb.MySql) to see the actual Nuget package. Otherwise, please click [here](https://www.nuget.org/api/v2/package/RepoDb.MySql) to download it.

## RepoDB.MySql (v1.13.0)

- Referenced the `RepoDb` package `v1.13.0`.
- Upgraded the `MySql.Data` package to `8.0.31`.


## RepoDB.MySql (v1.13.0-beta1)

- Referenced the `RepoDb` package `v1.13.0-beta1`.
- Upgraded the `MySql.Data` package to `8.0.31`.


## RepoDB.MySql (v1.13.0-alpha2)

- Referenced the `RepoDb` package `v1.13.0-alpha2`.


## RepoDB.MySql (v1.13.0-alpha1)

- Referenced the `RepoDb` package `v1.13.0-alpha1`.


## RepoDb.MySql (v1.1.6)

- Upgraded the `MySql.Data` package to `8.0.28`.
- Question: why to order by a field, it needs to be in the select? [#963](https://github.com/mikependon/RepoDB/issues/963)
- Supported the `.NET v6.0`.
- Added the `Title` and `PackageTags` tags in the Nuget Package (.nuspec references).
- Bug: Dot in schema name [#1015](https://github.com/mikependon/RepoDB/issues/1015)
- Referenced the `RepoDb` package `v1.12.10`.


## RepoDb.MySql (v1.1.5)

- Added the `net5.0` as part of the target frameworks.
- Upgraded the `RepoDb` package to `v1.12.8-beta5`.
- Bug: Exception on [Merge](/operation/merge) (PostgreSQL) [#776](https://github.com/mikependon/RepoDb/issues/776). RELATED
- Enhancement: Added the [MySqlDbTypeAttribute](/attribute/mysql/mysqldbtype) attribute. [#884](https://github.com/mikependon/RepoDb/issues/884)
- Enhancement: Deprecated the [MySqlTypeMapAttribute](/attribute/mysqltypemapattribute) attribute.
- Enhancement: Moved the `MySqlDbSetting` to `RepoDb.DbSettings` namespace.
- Enhancement: Add the attribute-based parameter setters for the [DbParameter](https://learn.microsoft.com/en-us/dotnet/api/system.data.common.dbparameter?view=net-6.0) object. [#886](https://github.com/mikependon/RepoDB/issues/886)


## RepoDb.MySql (v1.1.5-beta1)

- Added the `net5.0` as part of the target frameworks.
- Upgraded the `RepoDb` package to `v1.12.8-beta5`.
- Bug: Exception on [Merge](/operation/merge) (PostgreSQL) [#776](https://github.com/mikependon/RepoDb/issues/776). RELATED
- Enhancement: Added the [MySqlDbTypeAttribute](/attribute/parameter/mysqldbtype) attribute. [#884](https://github.com/mikependon/RepoDb/issues/884)
- Enhancement: Deprecated the [MySqlTypeMapAttribute](/attribute/mysqltypemapattribute) attribute.
- Enhancement: Moved the `MySqlDbSetting` to `RepoDb.DbSettings` namespace.
- Enhancement: Add the attribute-based parameter setters for the [DbParameter](https://learn.microsoft.com/en-us/dotnet/api/system.data.common.dbparameter?view=net-6.0) object. [#886](https://github.com/mikependon/RepoDB/issues/886)


## RepoDb.MySql (v1.1.4)

- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.12.7](/release/core#repodb-v1127)).
- Referenced the version [MySql.Data](https://www.nuget.org/packages/MySql.Data) ([v5.0.2](https://www.nuget.org/packages/MySql.Data/8.03)).


## RepoDb.MySql (v1.1.3)

- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.12.5](/release/core#repodb-v1125)).


## RepoDb.MySql (v1.1.2)

- Fixed the failing calls to the `ReaderToDbFieldAsync` method of the `MySqlDbHelper` object.
- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.12.5](/release/core#repodb-v1125)).


## RepoDb.MySql (v1.1.1)

- CancellationToken support? [#343](https://github.com/mikependon/RepoDB/issues/343)
- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.12.4](/release/core#repodb-v1124)).


## RepoDb.MySql (v1.1.0)

- Complete support to F# programming language as requested by the F# Community. Thank you to [Isaac Abraham](https://twitter.com/isaac_abraham) and [Angel Munoz](https://twitter.com/Daniel_Tuna) for being so collaborative.
- Breaking: Introduced the `fields` argument to the fetch (i.e.: [Query](/operation/query), [ExecuteQuery](/operation/executequery) and [BatchQuery](/operation/batchquery)) and push (i.e.: [Insert](/operation/insert), [Merge](/operation/merge) and [Update](/operation/update)) operations.
- Breaking: Introduced the `tableName` argument to all entity based operations. This completely resolves the `F#` anonymous invocations.
- Breaking: Renamed of argument `whereOrPrimaryKey` to `what`.
- Enhacement: Complete support to the immutable classes and/or types.
- Refactoring: Refactored the compiler implementations by simplifying the code from Monolithic to Smaller/Shorter/Cleaner code.
- Feature: Introduced the support to the [Class Handler](/feature/classhandler).
- Test: More coverage of Integration Tests and Unit Tests.
- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.12.0](/release/core#repodb-v1120)).


## RepoDb.MySql (v1.1.0-beta2)

- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.12.0-beta4](/release/core#repodb-v1120-beta4)).


## RepoDb.MySql (v1.1.0-beta1)

- Complete support to F# programming language as requested by the F# Community. Thank you to [Isaac Abraham](https://twitter.com/isaac_abraham) and [Angel Munoz](https://twitter.com/Daniel_Tuna) for being so collaborative.
- Breaking: Introduced the `fields` argument to the fetch (i.e.: [Query](/operation/query), [ExecuteQuery](/operation/executequery) and [BatchQuery](/operation/batchquery)) and push (i.e.: [Insert](/operation/insert), [Merge](/operation/merge) and [Update](/operation/update)) operations.
- Breaking: Introduced the `tableName` argument to all entity based operations. This completely resolves the `F#` anonymous invocations.
- Breaking: Renamed of argument `whereOrPrimaryKey` to `what`.
- Enhacement: Complete support to the immutable classes and/or types.
- Refactoring: Refactored the compiler implementations by simplifying the code from Monolithic to Smaller/Shorter/Cleaner code.
- Feature: Introduced the support to the [Class Handler](/feature/classhandler).
- Test: More coverage of Integration Tests and Unit Tests.
- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.12.0-beta1](/release/core#repodb-v1120-beta1)).


## RepoDb.MySql (v1.0.15)

- Bug: `NullReferenceException` occurs when `InsertAllAsync` is executed on Connection that belongs to `TransactionScope`. [#490](https://github.com/mikependon/RepoDb/issues/490)
- Enhancement: Remove the `SchemaSeparator` property from the `IDbSetting` interface [#475](https://github.com/mikependon/RepoDb/issues/475)
- Enhancement: Refactor the usage of `Count()` and `ElementAt()`.
- Upgraded the driver version of `MySql.Data` to `v8.0.21`


## RepoDb.MySql (v1.0.14)

- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.11.5](/release/core#repodb-v1115)).


## RepoDb.MySql (v1.0.13)

- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.11.4](/release/core#repodb-v1114)).


## RepoDb.MySql (v1.0.12)

- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.11.3](/release/core#repodb-v1113)).


## RepoDb.MySql (v1.0.11)

- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.11.2](/release/core#repodb-v1112)).


## RepoDb.MySql (v1.0.10)

- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.11.1](/release/core#repodb-v1111)).
- Bug: insert&lt;T&gt; issue - parameter 'xxx' already added [#432](https://github.com/mikependon/RepoDb/issues/432)
- Referenced the latest beta version of [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.11.0-beta4](/release/core#repodb-v1110-beta4)).


## RepoDb.MySql (v1.0.10-beta1)

- Bug: insert&lt;T&gt; issue - parameter 'xxx' already added [#432](https://github.com/mikependon/RepoDb/issues/432)
- Referenced the latest beta version of [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.11.0-beta4](/release/core#repodb-v1110-beta4)).


## RepoDb.MySql (v1.0.9)

- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.10.11](/release/core#repodb-v11011)).


## RepoDb.MySql (v1.0.8)

- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.10.11](/release/core#repodb-v11011)).


## RepoDb.MySql (v1.0.7)

- Referenced the version RepoDb.Core (v1.10.10).


## RepoDb.MySql (v1.0.6)

- Feature: Introduce MySql support to `MySqlDbType` via [MySqlTypeMapAttribute](/attribute/mysqltypemapattribute) attribute.


## RepoDb.MySql (v1.0.5)

- Referenced the version RepoDB v1.10.6.


## RepoDb.MySql (v1.0.4)

- Referenced the version RepoDB v1.10.5.


## RepoDb.MySql (v1.0.3)

- Referenced the updated version of RepoDB core library (v1.10.4).
- Error: Invalid expression. The property `Id` is not defined on a target type. [#364](https://github.com/mikependon/RepoDb/issues/364)


## RepoDB (v1.10.3-beta1)

- Referenced the updated version of RepoDB core library (v1.10.4-beta1).


## RepoDb.MySql (v1.0.2)

- Referenced the updated version of RepoDB core library (v1.10.3).


## RepoDb.MySql(v1.0.1)

- Referenced the updated version of RepoDB core library (v1.10.2).


## RepoDb.MySql (v1.0.1-beta1)

- Bug (Community Request): The primary field is not found exception when primary key column name is different from `Primary` attribute property name is different while `Connection.Update` is called. [#356](https://github.com/mikependon/RepoDb/issues/356)


## RepoDb.MySql (v1.0.0)

- Initial release for RepoDb.MySql.
