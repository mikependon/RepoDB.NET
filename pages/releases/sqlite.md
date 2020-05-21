---
layout: navpage
sidebar: releases
title: Releases for SQLite
description: "This page contains the latest information of the releases of RepoDb.SqLite library."
permalink: /release/sqlite
---

# Releases for RepoDb.SqLite

Please click [here](https://www.nuget.org/packages/RepoDb.SqLite) to see the actual Nuget package. Otherwise, please click [here](https://www.nuget.org/api/v2/package/RepoDb.SqLite) to download it.


#### RepoDb.SqlServer (v1.0.10)

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
