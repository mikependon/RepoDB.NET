---
layout: navpage
sidebar: releases
title: Releases for MySQL (using MySqlConnector)
description: "This page contains the latest information of the releases of RepoDb.MySqlConnector library."
permalink: /release/mysqlconnector
---

# Releases for RepoDb.MySqlConnector

Please click [here](https://www.nuget.org/packages/RepoDb.MySqlConnector) to see the actual Nuget package. Otherwise, please click [here](https://www.nuget.org/api/v2/package/RepoDb.MySqlConnector) to download it.

#### RepoDb.MySqlConnector (v1.1.0-beta1)

- Breaking: Complete support F# programming language as requested by the Community. Thank you to [Isaac Abraham](https://twitter.com/isaac_abraham) and [Angel Munoz](https://twitter.com/Daniel_Tuna) for being so collaborative.
- Breaking: Introduced the `fields` argument to the fetch (i.e.: [Query](/operation/query), [ExecuteQuery](/operation/executequery) and [BatchQuery](/operation/batchquery)) and push (i.e.: [Insert](/operation/insert), [Merge](/operation/merge) and [Update](/operation/update)) operations.
- Breaking: Introduced the `tableName` argument to all entity based operations. This completely resolves the `F#` anonymous invocations.
- Breaking: Renamed of argument `whereOrPrimaryKey` to `what`.
- Enhacement: Complete support to the immutable classes and/or types.
- Refactoring: Refactored the compiler implementations by simplifying the code from Monolithic to Smaller/Shorter/Cleaner code.
- Feature: Introduced the support to the [Class Handler](/feature/classhandler).
- Test: More coverage of Integration Tests and Unit Tests.
- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.12.0-beta1](/release/core#repodb-v1120-beta1)).


#### RepoDb.MySqlConnector (v1.0.4)

- Bug: `NullReferenceException` occurs when `InsertAllAsync` is executed on Connection that belongs to `TransactionScope`. [#490](https://github.com/mikependon/RepoDb/issues/490)
- Enhancement: Remove the `SchemaSeparator` property from the `IDbSetting` interface [#475](https://github.com/mikependon/RepoDb/issues/475)
- Enhancement: Refactor the usage of `Count()` and `ElementAt()`.


#### RepoDb.MySqlConnector (v1.0.3)

- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.11.5](/release/core#repodb-v1115)).


#### RepoDb.MySqlConnector (v1.0.2)

- Update to MySqlConnector 1.0 [#455](https://github.com/mikependon/RepoDb/pull/455)
- Referenced the version [MySqlConnector](https://github.com/mysql-net/MySqlConnector) ([v1.0.0](https://www.nuget.org/packages/MySqlConnector/1.0.0)).
- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.11.4](/release/core#repodb-v1114)).


#### RepoDb.MySqlConnector (v1.0.1)

- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.11.3](/release/core#repodb-v1113)).


#### RepoDb.MySqlConnector (v1.0.0)

- Initial release to MySqlConnector. References ([#446](https://github.com/mikependon/RepoDb/pull/446), [Commits](https://github.com/mikependon/RepoDb/commit/fa13702718ba8ace68a3c99505fdcaa5525bf73a)).
- Added the extensive Unit Tests and Integration Tests for MySqlConnector. 
- Added the AppVeyor for MySqlConnector.

