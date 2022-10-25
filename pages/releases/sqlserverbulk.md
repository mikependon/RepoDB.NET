---
layout: default
sidebar: releases
title: SQL Server Bulk
description: "This page contains the latest information of the releases of RepoDb.SqlServer.BulkOperations library."
nav_order: 3
permalink: /release/sqlserverbulk
parent: RELEASES
---

# Releases for RepoDb.SqlServer.BulkOperations

---

Please click [here](https://www.nuget.org/packages/RepoDb.SqlServer.BulkOperations) to see the actual Nuget package. Otherwise, please click [here](https://www.nuget.org/api/v2/package/RepoDb.SqlServer.BulkOperations) to download it.

### RepoDb.SqlServer.BulkOperations (v1.13.0-beta1)

- Referenced the `RepoDb` package `v1.13.0-beta1`.
- Referenced the `RepoDb.SqlServer` package `v1.13.0-beta1`.


### RepoDb.SqlServer.BulkOperations (v1.13.0-alpha3)

- Referenced the `RepoDb` package `v1.13.0-alpha3`.
- Referenced the `RepoDb.SqlServer` package `v1.13.0-alpha3`.


### RepoDb.SqlServer.BulkOperations (v1.13.0-alpha2)

- Referenced the `RepoDb` package `v1.13.0-alpha2`.
- Referenced the `RepoDb.SqlServer` package `v1.13.0-alpha2`.


### RepoDb.SqlServer.BulkOperations (v1.13.0-alpha1)

- Referenced the `RepoDb` package `v1.13.0-alpha1`.
- Referenced the `RepoDb.SqlServer` package `v1.13.0-alpha1`.


### RepoDb.SqlServer.BulkOperations (v1.1.6)

- Referenced the `RepoDb` package `v1.12.10`.
- Referenced the `RepoDb.SqlServer` package `v1.1.5`.
- Upgraded the `System.Linq.Async` package to `v6.1.0`.


### RepoDb.SqlServer.BulkOperations (v1.1.6-beta1/beta2)

- Upgraded the `RepoDb` package to `v1.12.10-beta4`.
- Upgraded the `RepoDb.SqlServer` package to `v1.1.5-beta4`.
- Added the `System.Linq.Async` (version `5.1.0`).
- Added the `Microsoft.Bcl.AsyncInterfaces` (version `6.0.0`).
- Initial support to the `IAsyncEnumerable` interface.
- Added the `Title` and `PackageTags` tags in the Nuget Package (.nuspec references).


### RepoDb.SqlServer.BulkOperations (v1.1.5)

- Bug: [BulkMergeAsync](/operation/bulkmerge) with multiple qualifiers [#864](https://github.com/mikependon/RepoDB/issues/864)
- Added the `net5.0` as part of the target frameworks.
- Upgraded the `RepoDb` package to `v1.12.8-beta5`.
- Upgraded the `RepoDb.SqlServer` package to `v1.1.4-beta4`.
- Supported the `.NET v6.0`.


### RepoDb.SqlServer.BulkOperations (v1.1.5-beta2)

- Added the `net5.0` as part of the target frameworks.
- Upgraded the `RepoDb` package to `v1.12.8-beta5`.
- Upgraded the `RepoDb.SqlServer` package to `v1.1.4-beta4`.


### RepoDb.SqlServer.BulkOperations (v1.1.5-beta1)

- Bug: [BulkMergeAsync](/operation/bulkmerge) with multiple qualifiers [#864](https://github.com/mikependon/RepoDB/issues/864)


### RepoDb.SqlServer.BulkOperations (v1.1.4)

- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.12.7](/release/core#repodb-v1127)).
- Referenced the version [RepoDb.SqlServer](https://www.nuget.org/packages/RepoDb.SqlServer) ([v1.1.3](/release/core#repodb-sqlver-v113)).


### RepoDb.SqlServer.BulkOperations (v1.1.3)

- Bug: Identity Values not set correctly with Bulk Insert from Sql Server due to Incorrect Sorting of data. [#691](https://github.com/mikependon/RepoDB/issues/691)
- The `hints` argument is removed from the [BulkInsert](/operation/bulkinsert) via `DbDataReader`.
- Added the `WriteToServer` method to cater the ordered resultset of the `__RepoDb_OrderColumn` custom column.

### RepoDb.SqlServer.BulkOperations (v1.1.2)

- Bug: Incorrect syntax near the keyword `WITH` [BulkMergeAsync](/operation/bulkinsert) [#640](https://github.com/mikependon/RepoDB/issues/640)
- Request: Do not crash if on empty enumerable on bulk operation [#635]((https://github.com/mikependon/RepoDB/issues/635)
- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.12.5-beta2](/release/core#repodb-v1125-beta2)).
- Reverted the changes to the [#635](https://github.com/mikependon/RepoDB/issues/635).
- Bug: System.Data.SqlClient.SqlException: 'Column name 'ID' does not exist in the target table or view.' [#651](https://github.com/mikependon/RepoDB/issues/651)
- Refactor the internal method implementations to only utilize the base methods for both the MDS and SDS method calls.
- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.12.5](/release/core#repodb-v1125)).


### RepoDb.SqlServer.BulkOperations (v1.1.2-beta4)

- Bug: [BulkInsert](/operation/bulkinsert) with mappings [#668](https://github.com/mikependon/RepoDB/issues/668)


### RepoDb.SqlServer.BulkOperations (v1.1.2-beta3)

- Bug: System.Data.SqlClient.SqlException: 'Column name 'ID' does not exist in the target table or view.' [#651](https://github.com/mikependon/RepoDB/issues/651)
- Refactor the internal method implementations to only utilize the base methods for both the MDS and SDS method calls.


### RepoDb.SqlServer.BulkOperations (v1.1.2-beta2)

- Reverted the changes to the [#635](https://github.com/mikependon/RepoDB/issues/635).


### RepoDb.SqlServer.BulkOperations (v1.1.2-beta1)

- Bug: Incorrect syntax near the keyword `WITH` [BulkMergeAsync](/operation/bulkinsert) [#640](https://github.com/mikependon/RepoDB/issues/640)
- Request: Do not crash if on empty enumerable on bulk operation [#635]((https://github.com/mikependon/RepoDB/issues/635)
- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.12.5-beta2](/release/core#repodb-v1125-beta2)).


### RepoDb.SqlServer.BulkOperations (v1.1.1)

- CancellationToken support? [#343](https://github.com/mikependon/RepoDB/issues/343)
- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.12.4](/release/core#repodb-v1124)).
- Referenced the version [RepoDb.SqlServer](https://www.nuget.org/packages/RepoDb.SqlServer) ([v1.1.1](/release/core#repodb-sqlver-v111)).


### RepoDb.SqlServer.BulkOperations (v1.1.0)

- Complete support to F# programming language as requested by the F# Community. Thank you to [Isaac Abraham](https://twitter.com/isaac_abraham) and [Angel Munoz](https://twitter.com/Daniel_Tuna) for being so collaborative.
- Breaking: Introduced the `fields` argument to the fetch (i.e.: [Query](/operation/query), [ExecuteQuery](/operation/executequery) and [BatchQuery](/operation/batchquery)) and push (i.e.: [Insert](/operation/insert), [Merge](/operation/merge) and [Update](/operation/update)) operations.
- Breaking: Introduced the `tableName` argument to all entity based operations. This completely resolves the `F#` anonymous invocations.
- Breaking: Renamed of argument `whereOrPrimaryKey` to `what`.
- Enhacement: Complete support to the immutable classes and/or types.
- Refactoring: Refactored the compiler implementations by simplifying the code from Monolithic to Smaller/Shorter/Cleaner code.
- Feature: Introduced the support to the [Class Handler](/feature/classhandler).
- Test: More coverage of Integration Tests and Unit Tests.
- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.12.0](/release/core#repodb-v1120)).
- Referenced the version [RepoDb.SqlServer](https://www.nuget.org/packages/RepoDb.SqlServer) ([v1.1.0](/release/core#repodb-sqlver-v110)).


### RepoDb.SqlServer.BulkOperations (v1.1.0-beta2)

- Referenced the version [RepoDb.SqlServer](https://www.nuget.org/packages/RepoDb.SqlServer) ([v1.1.0-beta2](/release/core#repodb-sqlver-v110-beta2)).
- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.12.0-beta4](/release/core#repodb-v1120-beta4)).


### RepoDb.SqlServer.BulkOperations (v1.1.0-beta1)

- Complete support to F# programming language as requested by the F# Community. Thank you to [Isaac Abraham](https://twitter.com/isaac_abraham) and [Angel Munoz](https://twitter.com/Daniel_Tuna) for being so collaborative.
- Breaking: Introduced the `fields` argument to the fetch (i.e.: [Query](/operation/query), [ExecuteQuery](/operation/executequery) and [BatchQuery](/operation/batchquery)) and push (i.e.: [Insert](/operation/insert), [Merge](/operation/merge) and [Update](/operation/update)) operations.
- Breaking: Introduced the `tableName` argument to all entity based operations. This completely resolves the `F#` anonymous invocations.
- Breaking: Renamed of argument `whereOrPrimaryKey` to `what`.
- Enhacement: Complete support to the immutable classes and/or types.
- Refactoring: Refactored the compiler implementations by simplifying the code from Monolithic to Smaller/Shorter/Cleaner code.
- Feature: Introduced the support to the [Class Handler](/feature/classhandler).
- Test: More coverage of Integration Tests and Unit Tests.
- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.12.0-beta1](/release/core#repodb-v1120-beta1)).
- Referenced the version [RepoDb.SqlServer](https://www.nuget.org/packages/RepoDb.SqlServer) ([v1.1.0-beta1](/release/core#repodb-sqlver-v110-beta1)).


### RepoDb.SqlServer.BulkOperations (v1.0.14)

- Enhancement: Async all the way [#477](https://github.com/mikependon/RepoDb/issues/477)
- Enhancement: Refactor the usage of `Count()` and `ElementAt()`.


### RepoDb.SqlServer.BulkOperations (v1.0.13)

- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.11.5](/release/core#repodb-v1115)).
- Referenced the version [RepoDb.SqlServer](https://www.nuget.org/packages/RepoDb.SqlServer) ([v1.0.12](/release/sqlserver#repodbsqlserver-v1012)).


### RepoDb.SqlServer.BulkOperations (v1.0.12)

- Enhancement: Support commandTimeout parameter in Bulk Operations [#466](https://github.com/mikependon/RepoDb/issues/466).
- Referenced the version [RepoDb.SqlServer](https://www.nuget.org/packages/RepoDb.SqlServer) ([v1.0.11](/release/sqlserver#repodbsqlserver-v1011))


### RepoDb.SqlServer.BulkOperations (v1.0.12-beta1)

- Enhancement: Support commandTimeout parameter in Bulk Operations [#466](https://github.com/mikependon/RepoDb/issues/466).
- Referenced the version [RepoDb.SqlServer](https://www.nuget.org/packages/RepoDb.SqlServer) ([v1.0.11](/release/sqlserver#repodbsqlserver-v1011))


### RepoDb.SqlServer.BulkOperations (v1.0.11)

- Enhancement: Enable Field class to Parse the Expression for Multiple Properties at BulkOperations ([BulkDelete](/operation/bulkdelete), [BulkInsert](/operation/bulkinsert), [BulkMerge](/operation/bulkmerge) and [BulkUpdate](/operation/bulkupdate)). [#453](https://github.com/mikependon/RepoDb/issues/453)


### RepoDb.SqlServer.BulkOperations (v1.0.10)

- Enhancement: Create a compiler for the SQL Server Bulk Operations Set Identities #450
- Added the XML comments file (a missing from previous release).
- Removed the `BulkOperationIdentitiesResult` class for performance purposes.
- Referenced the version [RepoDb.SqlServer](https://www.nuget.org/packages/RepoDb.SqlServer) ([v1.0.10](/release/sqlserver#repodb-v1010))


### RepoDb.SqlServer.BulkOperations (v1.0.9)

- Enhacement: Return the identity values during the BulkInsert and BulkMerge. [#396](https://github.com/mikependon/RepoDb/issues/396)
- Added the `BulkOperationIdentitiesResult` class to cater the bulk operation results for [BulkInsert](/operation/bulkinsert) and [BulkMerge](/operation/bulkmerge).
- Added the `isReturnIdentity` and `usePhysicalPseudoTempTable` arguments in the [BulkInsert](/operation/bulkinsert) and [BulkMerge](/operation/bulkmerge) operations.
- Added the `hints` argument in the [BulkInsert](/operation/bulkinsert) operations.
- Added more Integration Tests to cater the changes made for `isReturnIdentity` and `hints` arguments.


### RepoDb.SqlServer.BulkOperations (v1.0.8)

- Referenced the version [RepoDb.SqlServer](https://www.nuget.org/packages/RepoDb.SqlServer) ([v1.0.9](/release/sqlserver#repodb-v109))


### RepoDb.SqlServer.BulkOperations (v1.0.7)

- Referenced the version [RepoDb.SqlServer](https://www.nuget.org/packages/RepoDb.SqlServer) ([v1.0.8](/release/sqlserver#repodb-v108))


### RepoDb.SqlServer.BulkOperations (v1.0.6)

- Referenced the version [RepoDb.SqlServer](https://www.nuget.org/packages/RepoDb.SqlServer) ([v1.0.6](/release/sqlserver#repodb-v106))


### RepoDb.SqlServer.BulkOperations (v1.0.5)

- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.10.11](/release/core#repodb-v11011)).


### RepoDb.SqlServer.BulkOperations (v1.0.4)

- Referenced the version RepoDb.Core (v1.10.10).
- Fully supported the Bulk Operations ([BulkDelete](/operation/bulkdelete), [BulkInsert](/operation/bulkinsert), [BulkMerge](/operation/bulkmerge) and [BulkUpdate](/operation/bulkupdate)) for both System.Data.SqlClient and Microsoft.Data.SqlClient namespace.
- Auto set the options to `SqlBulkCopyOptions.KeepIdentity` for [BulkDelete](/operation/bulkdelete), [BulkMerge](/operation/bulkmerge) and [BulkUpdate](/operation/bulkupdate) (if there an identity primary/column from the DB).
- Used an underscore over dashes when composing the temporary/pseudo tables.
- Added an extensive Integration Tests for all Bulk methods (both SDS and MDS namespaces).


### RepoDb.SqlServer.BulkOperations (v1.0.4-beta3)

- Minor updates for [BulkUpdate](/operation/bulkupdate). Used an underscore over dashes when composing the temporary/pseudo tables.


### RepoDb.SqlServer.BulkOperations (v1.0.4-beta2)

- Auto set the options to `SqlBulkCopyOptions.KeepIdentity` for [BulkDelete](/operation/bulkdelete), [BulkMerge](/operation/bulkmerge) and [BulkUpdate](/operation/bulkupdate) (if there an identity primary/column from the DB).
- Used an underscore over dashes when composing the temporary/pseudo tables.


### RepoDb.SqlServer.BulkOperations (v1.0.4-beta1)

- Initial full support to the Bulk Operations ([BulkDelete](/operation/bulkdelete), [BulkInsert](/operation/bulkinsert), [BulkMerge](/operation/bulkmerge) and [BulkUpdate](/operation/bulkupdate)).


### RepoDb.SqlServer.BulkOperations (v1.0.3)

- Upgrade the references to Microsoft.Data.SqlClient (v1.1.1) and RepoDb.SqlServer (v1.0.3).


### RepoDb.SqlServer.BulkOperations (v1.0.2)

- Resumed the support to System.Data.SqlClient namespace.


### RepoDb.SqlServer.BulkOperations(v1.0.1)

- Initial release for RepoDb.SqlServer.BulkOperations.


### RepoDb.SqlServer.BulkOperations (v1.0.0)

- Initial release for RepoDb.SqlServer.BulkOperations.
