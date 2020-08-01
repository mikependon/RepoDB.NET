---
layout: navpage
sidebar: releases
title: Releases for SQL Server
description: "This page contains the latest information of the releases of RepoDb.SqlServer library."
permalink: /release/sqlserver
---

# Releases for RepoDb.SqlServer

Please click [here](https://www.nuget.org/packages/RepoDb.SqlServer) to see the actual Nuget package. Otherwise, please click [here](https://www.nuget.org/api/v2/package/RepoDb.SqlServer) to download it.

#### Next Version

- Bug: `NullReferenceException` occurs when `InsertAllAsync` is executed on Connection that belongs to `TransactionScope`. [#490](https://github.com/mikependon/RepoDb/issues/490)
- Enhancement: Remove the `SchemaSeparator` property from the `IDbSetting` interface [#475](https://github.com/mikependon/RepoDb/issues/475)
- Enhancement: Refactor the usage of 'Count()' and 'ElementAt()'
- Bug: RepoDb does not seem to work for SQL Server tables with dot in the name [#492](https://github.com/mikependon/RepoDb/issues/492)


#### RepoDb.SqlServer (v1.0.12)

- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.11.5](/release/core#repodb-v1115)).


#### RepoDb.SqlServer (v1.0.11)

- Updated the version of `Microsoft.Data.SqlClient` into `v2.0.0`.
- Bug: table schema are ignored [#456](https://github.com/mikependon/RepoDb/issues/456)
- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.11.4](/release/core#repodb-v1114)).


#### RepoDb.SqlServer (v1.0.11-beta1)

- Bug: table schema are ignored [#456](https://github.com/mikependon/RepoDb/issues/456)


#### RepoDb.SqlServer (v1.0.10)

- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.11.3](/release/core#repodb-v1113)).


#### RepoDb.SqlServer (v1.0.9)

- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.11.2](/release/core#repodb-v1112)).


#### RepoDb.SqlServer (v1.0.8)

- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.11.1](/release/core#repodb-v1111)).


#### RepoDb.SqlServer (v1.0.7)

- Bug: Truncates string and bytes [#423](https://github.com/mikependon/RepoDb/issues/423)
- Referenced the Microsoft.Data.SqlClient version 1.1.2.
- Referenced the System.Data.SqlClient version 4.8.1. 
- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.11.0](/release/core#repodb-v1110)).


#### RepoDb.SqlServer (v1.0.6)

- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.10.11](/release/core#repodb-v11011)).


#### RepoDb.SqlServer (v1.0.5)

- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.10.11](/release/core#repodb-v11011)).


#### RepoDb.SqlServer (v1.0.4)

- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.10.10](/release/core#repodb-v11010)).


#### RepoDb.SqlServer (v1.0.3)

- Feature: Introduce the SQL Server support to `SqlParameter.SqlDbType` via `MicrosoftSqlServerTypeMapAttribute` class (for Microsoft.Data.SqlClient) and `SystemSqlServerTypeMapAttribute` class (for System.Data.SqlClient).
- Added more Unit Tests.


#### RepoDb.SqlServer (v1.0.2)

- Resume the support to System.Data.SqlClient.


#### RepoDb.SqlServer(v1.0.1)

- Initial release for RepoDb.SqlServer.


#### RepoDb.SqlServer (v1.0.0)

- Initial release for RepoDb.SqlServer.
