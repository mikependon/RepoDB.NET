---
layout: default
title: "ClickHouse"
has_children: true
permalink: /enumeration/clickhouse
parent: ENUMERATIONS
---

# Enumerations (ClickHouse)

---

Enumerations used by the ClickHouse bulk operations ([RepoDb.ClickHouse.BulkOperations](https://www.nuget.org/packages/RepoDb.ClickHouse.BulkOperations)). [ClickHouseBulkImportIdentityBehavior](/enumeration/clickhouse/clickhousebulkimportidentitybehavior) controls whether newly generated identity values are returned back to the entities after [BulkInsert](/operation/clickhouse/bulkinsert) or [BulkMerge](/operation/clickhouse/bulkmerge), and [ClickHouseBulkImportPseudoTableType](/enumeration/clickhouse/clickhousebulkimportpseudotabletype) controls the kind of staging table created to stage the bulk-imported data.
