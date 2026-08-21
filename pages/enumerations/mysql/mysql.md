---
layout: default
title: "MySql"
has_children: true
permalink: /enumeration/mysql
parent: ENUMERATIONS
---

# Enumerations (MySql)

---

Enumerations used by the [MySql bulk operations](/operation/mysql) (`MySql.Data`-based, via [RepoDb.MySql.BulkOperations](https://www.nuget.org/packages/RepoDb.MySql.BulkOperations)). [MySqlBulkImportIdentityBehavior](/enumeration/mysql/mysqlbulkimportidentitybehavior) controls whether newly generated identity values are returned back to the entities after [BulkInsert](/operation/mysql/bulkinsert) or [BulkMerge](/operation/mysql/bulkmerge), and [MySqlBulkImportPseudoTableType](/enumeration/mysql/mysqlbulkimportpseudotabletype) controls the kind of staging table created to stage the bulk-imported data.
