---
layout: navpage
sidebar: releases
title: Releases for PostgreSQL
description: "This page contains the latest information of the releases of RepoDb.PostgreSql library."
permalink: /release/postgresql
---

# Releases for RepoDb.PostgreSql

Please click [here](https://www.nuget.org/packages/RepoDb.PostgreSql) to see the actual Nuget package. Otherwise, please click [here](https://www.nuget.org/api/v2/package/RepoDb.PostgreSql) to download it.

#### RepoDb.PostgreSql (v1.1.0)

- Complete support to F# programming language as requested by the F# Community. Thank you to [Isaac Abraham](https://twitter.com/isaac_abraham) and [Angel Munoz](https://twitter.com/Daniel_Tuna) for being so collaborative.
- Breaking: Introduced the `fields` argument to the fetch (i.e.: [Query](/operation/query), [ExecuteQuery](/operation/executequery) and [BatchQuery](/operation/batchquery)) and push (i.e.: [Insert](/operation/insert), [Merge](/operation/merge) and [Update](/operation/update)) operations.
- Breaking: Introduced the `tableName` argument to all entity based operations. This completely resolves the `F#` anonymous invocations.
- Breaking: Renamed of argument `whereOrPrimaryKey` to `what`.
- Enhacement: Complete support to the immutable classes and/or types.
- Enhancement: PostgreSQL - Add the `RESTART IDENTITY` in the [Truncate](/operation/truncate) operation. [#563](https://github.com/mikependon/RepoDb/issues/563)
- Refactoring: Refactored the compiler implementations by simplifying the code from Monolithic to Smaller/Shorter/Cleaner code.
- Feature: Introduced the support to the [Class Handler](/feature/classhandler).
- Test: More coverage of Integration Tests and Unit Tests.
- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.12.0](/release/core#repodb-v1120)).


#### RepoDb.PostgreSql (v1.1.0-beta2)

- Enhancement: PostgreSQL - Add the `RESTART IDENTITY` in the [Truncate](/operation/truncate) operation. [#563](https://github.com/mikependon/RepoDb/issues/563)
- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.12.0-beta4](/release/core#repodb-v1120-beta4)).


#### RepoDb.PostgreSql (v1.1.0-beta1)

- Complete support to F# programming language as requested by the F# Community. Thank you to [Isaac Abraham](https://twitter.com/isaac_abraham) and [Angel Munoz](https://twitter.com/Daniel_Tuna) for being so collaborative.
- Breaking: Introduced the `fields` argument to the fetch (i.e.: [Query](/operation/query), [ExecuteQuery](/operation/executequery) and [BatchQuery](/operation/batchquery)) and push (i.e.: [Insert](/operation/insert), [Merge](/operation/merge) and [Update](/operation/update)) operations.
- Breaking: Introduced the `tableName` argument to all entity based operations. This completely resolves the `F#` anonymous invocations.
- Breaking: Renamed of argument `whereOrPrimaryKey` to `what`.
- Enhacement: Complete support to the immutable classes and/or types.
- Refactoring: Refactored the compiler implementations by simplifying the code from Monolithic to Smaller/Shorter/Cleaner code.
- Feature: Introduced the support to the [Class Handler](/feature/classhandler).
- Test: More coverage of Integration Tests and Unit Tests.
- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.12.0-beta1](/release/core#repodb-v1120-beta1)).


#### RepoDb.PostgreSql (v1.0.12)

- Bug: `NullReferenceException` occurs when `InsertAllAsync` is executed on Connection that belongs to `TransactionScope`. [#490](https://github.com/mikependon/RepoDb/issues/490)
- Enhancement: Remove the `SchemaSeparator` property from the `IDbSetting` interface [#475](https://github.com/mikependon/RepoDb/issues/475)
- Enhancement: Refactor the usage of `Count()` and `ElementAt()`.


#### RepoDb.PostgreSql (v1.0.11)

- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.11.5](/release/core#repodb-v1115)).


#### RepoDb.PostgreSql (v1.0.10)

- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.11.4](/release/core#repodb-v1114)).
- Upgraded the reference of `Npgsql` version `4.1.3.1`.


#### RepoDb.PostgreSql (v1.0.9)

- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.11.3](/release/core#repodb-v1113)).


#### RepoDb.PostgreSql (v1.0.8)

- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.11.2](/release/core#repodb-v1112)).


#### RepoDb.PostgreSql (v1.0.7)

- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.11.1](/release/core#repodb-v1111)).
- Bug: (Direct Reports) Fixed the issue at DeleteAll() via primary keys if the items is beyond 2100.
- Performance: Reverted the value of Constant.DefaultBatchOperationSize into 10.


#### RepoDb.PostgreSql (v1.0.7-beta1)

- Bug: (Direct Reports) Fixed the issue at DeleteAll() via primary keys if the items is beyond 2100.
- Performance: Reverted the value of Constant.DefaultBatchOperationSize into 10.


#### RepoDb.PostgreSql (v1.0.6)

- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.10.11](/release/core#repodb-v11011)).


#### RepoDb.PostgreSql (v1.0.5)

- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.10.11](/release/core#repodb-v11011)).


#### RepoDb.PostgreSql (v1.0.4)

- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.10.10](/release/core#repodb-v11010)).


#### RepoDb.PostgreSql (v1.0.3)

- Feature: Introduce PostgreSql support to `NpgsqlDbType` via [PostgreSqlTypeMapAttribute](/attribute/postgresqltypemapattribute) attribute. [#390](https://github.com/mikependon/RepoDb/issues/390)


##### RepoDB (v1.10.3-beta1)

- Referenced the updated version of RepoDB core library (v1.10.4-beta1).


#### RepoDb.PostgreSql (v1.0.2)

- Referenced the version RepoDB v1.10.6.


#### RepoDb.PostgreSql(v1.0.1)

- Referenced the version RepoDB v1.10.5.


#### RepoDb.PostgreSql (v1.0.0)

- Initial release of RepoDb.PostgreSql.


#### RepoDb.PostgreSql (v1.0.0-beta1)

- Initial beta release of RepoDb.PostgreSql.


#### RepoDb.PostgreSql (v1.0.0-alpha1)

- Initial alpha release of RepoDb.PostgreSql.
