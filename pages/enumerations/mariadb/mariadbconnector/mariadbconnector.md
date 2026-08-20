---
layout: default
title: "MariaDbConnector"
has_children: true
permalink: /enumeration/mariadb/mariadbconnector
parent: ENUMERATIONS
---

# Enumerations (MariaDbConnector)

---

Enumerations used by the [MariaDbConnector bulk operations](/operation/mariadbconnector) (`MySqlConnector`-based, via [RepoDb.MariaDbConnector.BulkOperations](https://www.nuget.org/packages/RepoDb.MariaDbConnector.BulkOperations)). [MariaDbBulkImportIdentityBehavior](/enumeration/mariadb/mariadbconnector/mariadbbulkimportidentitybehavior) controls whether newly generated identity values are returned back to the entities after [BulkInsert](/operation/mariadbconnector/bulkinsert) or [BulkMerge](/operation/mariadbconnector/bulkmerge), and [MariaDbBulkImportPseudoTableType](/enumeration/mariadb/mariadbconnector/mariadbbulkimportpseudotabletype) controls the kind of staging table created to stage the bulk-imported data.
