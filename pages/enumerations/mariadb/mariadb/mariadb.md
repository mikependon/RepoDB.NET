---
layout: default
title: "MariaDb"
has_children: true
permalink: /enumeration/mariadb/mariadb
parent: ENUMERATIONS
---

# Enumerations (MariaDb)

---

Enumerations used by the [MariaDb bulk operations](/operation/mariadb) (`MySql.Data`-based, via [RepoDb.MariaDb.BulkOperations](https://www.nuget.org/packages/RepoDb.MariaDb.BulkOperations)). [MariaDbBulkImportIdentityBehavior](/enumeration/mariadb/mariadb/mariadbbulkimportidentitybehavior) controls whether newly generated identity values are returned back to the entities after [BulkInsert](/operation/mariadb/bulkinsert) or [BulkMerge](/operation/mariadb/bulkmerge), and [MariaDbBulkImportPseudoTableType](/enumeration/mariadb/mariadb/mariadbbulkimportpseudotabletype) controls the kind of staging table created to stage the bulk-imported data.
