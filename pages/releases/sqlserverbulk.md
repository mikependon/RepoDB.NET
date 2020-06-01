---
layout: navpage
sidebar: releases
title: Releases for SQL Server Bulk
description: "This page contains the latest information of the releases of RepoDb.SqlServer.BulkOperations library."
permalink: /release/sqlserverbulk
---

# Releases for RepoDb.SqlServer.BulkOperations

Please click [here](https://www.nuget.org/packages/RepoDb.SqlServer.BulkOperations) to see the actual Nuget package. Otherwise, please click [here](https://www.nuget.org/api/v2/package/RepoDb.SqlServer.BulkOperations) to download it.


#### RepoDb.SqlServer.BulkOperations (v1.0.9)

- Enhacement: Return the identity values during the BulkInsert and BulkMerge. [#396](https://github.com/mikependon/RepoDb/issues/396)
- Added the `BulkOperationIdentitiesResult` class to cater the bulk operation results for [BulkInsert](/operation/bulkinsert) and [BulkMerge](/operation/bulkmerge).
- Added the `isReturnIdentity` and `usePhysicalPseudoTempTable` arguments in the [BulkInsert](/operation/bulkinsert) and [BulkMerge](/operation/bulkmerge) operations.
- Added the `hints` argument in the [BulkInsert](/operation/bulkinsert) operations.
- Added more Integration Tests to cater the changes made for `isReturnIdentity` and `hints` arguments.


#### RepoDb.SqlServer.BulkOperations (v1.0.8)

- Referenced the version [RepoDb.SqlServer](https://www.nuget.org/packages/RepoDb.SqlServer) ([v1.0.9](/release/sqlserver#repodb-v109))


#### RepoDb.SqlServer.BulkOperations (v1.0.7)

- Referenced the version [RepoDb.SqlServer](https://www.nuget.org/packages/RepoDb.SqlServer) ([v1.0.8](/release/sqlserver#repodb-v108))


#### RepoDb.SqlServer.BulkOperations (v1.0.6)

- Referenced the version [RepoDb.SqlServer](https://www.nuget.org/packages/RepoDb.SqlServer) ([v1.0.6](/release/sqlserver#repodb-v106))


#### RepoDb.SqlServer.BulkOperations (v1.0.5)

- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.10.11](/release/core#repodb-v11011)).


#### RepoDb.SqlServer.BulkOperations (v1.0.4)

- Referenced the version RepoDb.Core (v1.10.10).
- Fully supported the Bulk Operations ([BulkDelete](/operation/bulkdelete), [BulkInsert](/operation/bulkinsert), [BulkMerge](/operation/bulkmerge) and [BulkUpdate](/operation/bulkupdate)) for both System.Data.SqlClient and Microsoft.Data.SqlClient namespace.
- Auto set the options to `SqlBulkCopyOptions.KeepIdentity` for [BulkDelete](/operation/bulkdelete), [BulkMerge](/operation/bulkmerge) and [BulkUpdate](/operation/bulkupdate) (if there an identity primary/column from the DB).
- Used an underscore over dashes when composing the temporary/pseudo tables.
- Added an extensive Integration Tests for all Bulk methods (both SDS and MDS namespaces).


#### RepoDb.SqlServer.BulkOperations (v1.0.4-beta3)

- Minor updates for [BulkUpdate](/operation/bulkupdate). Used an underscore over dashes when composing the temporary/pseudo tables.


#### RepoDb.SqlServer.BulkOperations (v1.0.4-beta2)

- Auto set the options to `SqlBulkCopyOptions.KeepIdentity` for [BulkDelete](/operation/bulkdelete), [BulkMerge](/operation/bulkmerge) and [BulkUpdate](/operation/bulkupdate) (if there an identity primary/column from the DB).
- Used an underscore over dashes when composing the temporary/pseudo tables.


#### RepoDb.SqlServer.BulkOperations (v1.0.4-beta1)

- Initial full support to the Bulk Operations ([BulkDelete](/operation/bulkdelete), [BulkInsert](/operation/bulkinsert), [BulkMerge](/operation/bulkmerge) and [BulkUpdate](/operation/bulkupdate)).


#### RepoDb.SqlServer.BulkOperations (v1.0.3)

- Upgrade the references to Microsoft.Data.SqlClient (v1.1.1) and RepoDb.SqlServer (v1.0.3).


#### RepoDb.SqlServer.BulkOperations (v1.0.2)

- Resumed the support to System.Data.SqlClient namespace.


#### RepoDb.SqlServer.BulkOperations(v1.0.1)

- Initial release for RepoDb.SqlServer.BulkOperations.


#### RepoDb.SqlServer.BulkOperations (v1.0.0)

- Initial release for RepoDb.SqlServer.BulkOperations.
