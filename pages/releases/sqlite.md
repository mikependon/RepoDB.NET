---
layout: navpage
sidebar: releases
title: Releases for SQLite (RepoDb)
permalink: /releases/sqlite
---

## Releases for RepoDb.SqLite

Please click [here](https://www.nuget.org/packages/RepoDb.SqLite) to see the actual Nuget package. Otherwise, please click [here](https://www.nuget.org/api/v2/package/RepoDb.SqLite) to download it.


#### RepoDb.SqLite (v1.0.7)

- Referenced the latest version of RepoDb.Core (v1.10.10).


#### RepoDb.SqLite (v1.0.6)

- Referenced the latest version of RepoDb v1.10.6.


#### RepoDb.SqLite (v1.0.5)

- Referenced the latest version of RepoDb v1.10.5.


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
