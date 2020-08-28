---
layout: navpage
sidebar: releases
title: Releases for SQLite
description: "This page contains the latest information of the releases of RepoDb.SqLite library."
permalink: /release/sqlite
---

# Releases for RepoDb.SqLite

Please click [here](https://www.nuget.org/packages/RepoDb.SqLite) to see the actual Nuget package. Otherwise, please click [here](https://www.nuget.org/api/v2/package/RepoDb.SqLite) to download it.

#### RepoDb.SqLite (v1.1.0-beta1)

- Breaking: Complete support F# programming language as requested by the Community. Thank you to [Isaac Abraham](https://twitter.com/isaac_abraham) and [Angel Munoz](https://twitter.com/Daniel_Tuna) for being so collaborative.
- Breaking: Introduced the `fields` argument to the fetch (i.e.: [Query](/operation/query), [ExecuteQuery](/operation/executequery) and [BatchQuery](/operation/batchquery)) and push (i.e.: [Insert](/operation/insert), [Merge](/operation/merge) and [Update](/operation/update)) operations.
- Breaking: Introduced the `tableName` argument to all entity based operations. This completely resolves the `F#` anonymous invocations.
- Breaking: Renamed of argument `whereOrPrimaryKey` to `what`.
- Enhacement: Complete support to the immutable classes and/or types.
- Refactoring: Refactored the compiler implementations by simplifying the code from Monolithic to Smaller/Shorter/Cleaner code.
- Feature: Introduced the support to the [Class Handler](/feature/classhandler).
- Test: More coverage of Integration Tests and Unit Tests.
- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.12.0-beta1](/release/core#repodb-v1120-beta1)).


#### RepoDb.SqLite (v1.0.16)

- Supported both the drivers from `System.Data.SQLite.Core` and `Microsoft.Data.Sqlite`.
- The switch from `System.Data.SQLite.Core` to `Microsoft.Data.SQLite.Core` is a pretty big breaking change [#497](https://github.com/mikependon/RepoDb/issues/497)


#### RepoDb.SqLite (v1.0.15)

- Updated the underlying driver from `System.Data.SQLite` to `Microsoft.Data.Sqlite.Core`.
- Request: RepoDb.SqLite remove dependency on `EntityFramework` [#486](https://github.com/mikependon/RepoDb/issues/486)
- Bug: `NullReferenceException` occurs when `InsertAllAsync` is executed on Connection that belongs to `TransactionScope`. [#490](https://github.com/mikependon/RepoDb/issues/490)
- Enhancement: Remove the `SchemaSeparator` property from the `IDbSetting` interface [#475](https://github.com/mikependon/RepoDb/issues/475)
- Enhancement: Refactor the usage of `Count()` and `ElementAt()`.


#### RepoDb.SqLite (v1.0.14)

- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.11.5](/release/core#repodb-v1115)).


#### RepoDb.SqLite (v1.0.13)

- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.11.4](/release/core#repodb-v1114)).
- Upgraded the reference of `System.Data.SQLite` version `1.0.113.1`.


#### RepoDb.SqLite (v1.0.12)

- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.11.3](/release/core#repodb-v1113)).


#### RepoDb.SqLite (v1.0.11)

- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.11.2](/release/core#repodb-v1112)).


#### RepoDb.SqLite (v1.0.10)

- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.11.1](/release/core#repodb-v1111)).
- Upgraded the reference of `System.Data.SQLite` version `1.0.112.2`.


#### RepoDb.SqLite (v1.0.9)

- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.10.11](/release/core#repodb-v11011)).


#### RepoDb.SqLite (v1.0.8)

- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.10.11](/release/core#repodb-v11011)).


#### RepoDb.SqLite (v1.0.7)

- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.10.10](/release/core#repodb-v11010)).


#### RepoDb.SqLite (v1.0.6)

- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.10.6](/release/core#repodb-v1106)).


#### RepoDb.SqLite (v1.0.5)

- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.10.5](/release/core#repodb-v1105)).


#### RepoDb.SqLite (v1.0.4)

- Referenced the updated version of RepoDb core library (v1.10.4).


#### RepoDb.SqLite (v1.0.3)

- Referenced the updated version of RepoDb core library (v1.10.3).


##### RepoDb (v1.10.3)

- Revert the recurrent calls to DbFieldCache.Get() in the ExecuteQuery method.


#### RepoDb.SqLite (v1.0.2)

- Referenced the updated version of RepoDb core library (v1.10.2).

##### RepoDb v1.10.2:

- Bug (Community Request): The primary field is not found exception when primary key column name is different from `Primary` attribute property name is different while `Connection.Update` is called. [#356](https://github.com/mikependon/RepoDb/issues/356)
- Bug: Set the return value of `Max` and `Min` operations to object. [#357](https://github.com/mikependon/RepoDb/issues/357)
- Performance: Refactor to remove the recurrent calls to `DbFieldCache.Get()` in the `ExecuteQuery` method.


#### RepoDb.SqLite(v1.0.1)

- Referenced the updated version of RepoDb core library.


#### RepoDb.SqLite (v1.0.0)

- Initial release for RepoDb.SqLite.
