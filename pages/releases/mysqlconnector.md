---
layout: default
sidebar: releases
title: MySQL (MySqlConnector)
description: "This page contains the latest information of the releases of RepoDb.MySqlConnector library."
nav_order: 7
permalink: /release/mysqlconnector
parent: RELEASES
---

# Releases for RepoDb.MySqlConnector

---

Please click [here](https://www.nuget.org/packages/RepoDb.MySqlConnector) to see the actual Nuget package. Otherwise, please click [here](https://www.nuget.org/api/v2/package/RepoDb.MySqlConnector) to download it.

## RepoDB.MySqlConnector (v1.13.0-beta1)

- Referenced the `RepoDb` package `v1.13.0-beta1`.


## RepoDB.MySqlConnector (v1.13.0-alpha2)

- Referenced the `RepoDb` package `v1.13.0-alpha2`.


## RepoDB.MySqlConnector (v1.13.0-alpha1)

- Referenced the `RepoDb` package `v1.13.0`.
- Upgraded the `MySqlConnector` package to `2.1.13`.


## RepoDb.MySqlConnector (v1.1.5)

- Upgraded the `MySqlConnector` package to `2.1.6`.
- Question: why to order by a field, it needs to be in the select? [#963](https://github.com/mikependon/RepoDB/issues/963)
- Supported the `.NET v6.0`.
- Added the `Title` and `PackageTags` tags in the Nuget Package (.nuspec references).
- Bug: Dot in schema name [#1015](https://github.com/mikependon/RepoDB/issues/1015)
- Referenced the `RepoDb` package `v1.12.10`.


## RepoDb.MySqlConnector (v1.1.4)

- Upgraded the `MySqlConnector` package to `v1.3.12`.
- Upgraded the `RepoDb` package to `v1.12.8-beta5`.
- Added the `net5.0` as part of the target frameworks.
- Enhancement: Added the [MySqlDbTypeAttribute](/attribute/mysqlconnector/mysqldbtype) attribute. [#884](https://github.com/mikependon/RepoDb/issues/884)
- Enhancement: Deprecated the [MySqlConnectorTypeMapAttribute](/attribute/mysqlconnectortypemap) attribute.
- Enhancement: Moved the `MySqlConnectorDbSetting` to `RepoDb.DbSettings` namespace.
- Enhancement: Add the attribute-based parameter setters for the `DbParameter` object. [#886](https://github.com/mikependon/RepoDB/issues/886)
- Bug: Exception on [Merge](/operation/merge) (PostgreSQL) [#776](https://github.com/mikependon/RepoDb/issues/776). RELATED
- Wrong ordinal for Nullable filed information in MySqlConnectorDbHelper.ReaderToDbFieldAsync [#780](https://github.com/mikependon/RepoDb/issues/780)


## RepoDb.MySqlConnector (v1.1.4-beta2)

- Upgraded the `MySqlConnector` package to `v1.3.12`.
- Upgraded the `RepoDb` package to `v1.12.8-beta5`.
- Added the `net5.0` as part of the target frameworks.
- Enhancement: Added the [MySqlConnectorParameterDbTypeAttribute](/attribute/mysqlconnectorparameterdbtype) attribute.
- Enhancement: Added the [MySqlDbTypeAttribute](/attribute/parameter/mysqlconnectordbtype) attribute. [#884](https://github.com/mikependon/RepoDb/issues/884)
- Enhancement: Deprecated the [MySqlConnectorTypeMapAttribute](/attribute/mysqlconnectortypemap) attribute.
- Enhancement: Moved the `MySqlConnectorDbSetting` to `RepoDb.DbSettings` namespace.
- Enhancement: Add the attribute-based parameter setters for the `DbParameter` object. [#886](https://github.com/mikependon/RepoDB/issues/886)


## RepoDb.MySqlConnector (v1.1.4-beta1)

- Bug: Exception on [Merge](/operation/merge) (PostgreSQL) [#776](https://github.com/mikependon/RepoDb/issues/776). RELATED
- Wrong ordinal for Nullable filed information in MySqlConnectorDbHelper.ReaderToDbFieldAsync [#780](https://github.com/mikependon/RepoDb/issues/780)


## RepoDb.MySqlConnector (v1.1.3)

- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.12.7](/release/core#repodb-v1127)).


## RepoDb.MySqlConnector (v1.1.2)

- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.12.5](/release/core#repodb-v1125)).


## RepoDb.MySqlConnector (v1.1.1)

- CancellationToken support? [#343](https://github.com/mikependon/RepoDB/issues/343)
- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.12.4](/release/core#repodb-v1124)).


## RepoDb.MySqlConnector (v1.1.0)

- Complete support to F# programming language as requested by the F# Community. Thank you to [Isaac Abraham](https://twitter.com/isaac_abraham) and [Angel Munoz](https://twitter.com/Daniel_Tuna) for being so collaborative.
- Breaking: Introduced the `fields` argument to the fetch (i.e.: [Query](/operation/query), [ExecuteQuery](/operation/executequery) and [BatchQuery](/operation/batchquery)) and push (i.e.: [Insert](/operation/insert), [Merge](/operation/merge) and [Update](/operation/update)) operations.
- Breaking: Introduced the `tableName` argument to all entity based operations. This completely resolves the `F#` anonymous invocations.
- Breaking: Renamed of argument `whereOrPrimaryKey` to `what`.
- Enhacement: Complete support to the immutable classes and/or types.
- Refactoring: Refactored the compiler implementations by simplifying the code from Monolithic to Smaller/Shorter/Cleaner code.
- Feature: Introduced the support to the [Class Handler](/feature/classhandler).
- Test: More coverage of Integration Tests and Unit Tests.
- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.12.0](/release/core#repodb-v1120)).


## RepoDb.MySqlConnector (v1.1.0-beta2)

- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.12.0-beta4](/release/core#repodb-v1120-beta4)).


## RepoDb.MySqlConnector (v1.1.0-beta1)

- Complete support to F# programming language as requested by the F# Community. Thank you to [Isaac Abraham](https://twitter.com/isaac_abraham) and [Angel Munoz](https://twitter.com/Daniel_Tuna) for being so collaborative.
- Breaking: Introduced the `fields` argument to the fetch (i.e.: [Query](/operation/query), [ExecuteQuery](/operation/executequery) and [BatchQuery](/operation/batchquery)) and push (i.e.: [Insert](/operation/insert), [Merge](/operation/merge) and [Update](/operation/update)) operations.
- Breaking: Introduced the `tableName` argument to all entity based operations. This completely resolves the `F#` anonymous invocations.
- Breaking: Renamed of argument `whereOrPrimaryKey` to `what`.
- Enhacement: Complete support to the immutable classes and/or types.
- Refactoring: Refactored the compiler implementations by simplifying the code from Monolithic to Smaller/Shorter/Cleaner code.
- Feature: Introduced the support to the [Class Handler](/feature/classhandler).
- Test: More coverage of Integration Tests and Unit Tests.
- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.12.0-beta1](/release/core#repodb-v1120-beta1)).


## RepoDb.MySqlConnector (v1.0.4)

- Bug: `NullReferenceException` occurs when `InsertAllAsync` is executed on Connection that belongs to `TransactionScope`. [#490](https://github.com/mikependon/RepoDb/issues/490)
- Enhancement: Remove the `SchemaSeparator` property from the `IDbSetting` interface [#475](https://github.com/mikependon/RepoDb/issues/475)
- Enhancement: Refactor the usage of `Count()` and `ElementAt()`.


## RepoDb.MySqlConnector (v1.0.3)

- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.11.5](/release/core#repodb-v1115)).


## RepoDb.MySqlConnector (v1.0.2)

- Update to MySqlConnector 1.0 [#455](https://github.com/mikependon/RepoDb/pull/455)
- Referenced the version [MySqlConnector](https://github.com/mysql-net/MySqlConnector) ([v1.0.0](https://www.nuget.org/packages/MySqlConnector/1.0.0)).
- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.11.4](/release/core#repodb-v1114)).


## RepoDb.MySqlConnector (v1.0.1)

- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.11.3](/release/core#repodb-v1113)).


## RepoDb.MySqlConnector (v1.0.0)

- Initial release to MySqlConnector. References ([#446](https://github.com/mikependon/RepoDb/pull/446), [Commits](https://github.com/mikependon/RepoDb/commit/fa13702718ba8ace68a3c99505fdcaa5525bf73a)).
- Added the extensive Unit Tests and Integration Tests for MySqlConnector. 
- Added the AppVeyor for MySqlConnector.

