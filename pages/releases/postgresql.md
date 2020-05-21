---
layout: navpage
sidebar: releases
title: Releases for PostgreSQL
description: "This page contains the latest information of the releases of RepoDb.PostgreSql library."
permalink: /release/postgresql
---

# Releases for RepoDb.PostgreSql

Please click [here](https://www.nuget.org/packages/RepoDb.PostgreSql) to see the actual Nuget package. Otherwise, please click [here](https://www.nuget.org/api/v2/package/RepoDb.PostgreSql) to download it.


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


##### RepoDb (v1.10.3-beta1)

- Referenced the updated version of RepoDb core library (v1.10.4-beta1).


#### RepoDb.PostgreSql (v1.0.2)

- Referenced the version RepoDb v1.10.6.


#### RepoDb.PostgreSql(v1.0.1)

- Referenced the version RepoDb v1.10.5.


#### RepoDb.PostgreSql (v1.0.0)

- Initial release of RepoDb.PostgreSql.


#### RepoDb.PostgreSql (v1.0.0-beta1)

- Initial beta release of RepoDb.PostgreSql.


#### RepoDb.PostgreSql (v1.0.0-alpha1)

- Initial alpha release of RepoDb.PostgreSql.
