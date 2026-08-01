---
layout: default
title: "PostgreSQL"
nav_order: 3
has_children: true
permalink: /enumeration/postgresql
parent: ENUMERATIONS
---

# Enumerations (PostgreSQL)

---

Enumerations used by the [PostgreSQL binary bulk operations](/operation/postgresql). [BulkImportIdentityBehavior](/enumeration/postgresql/bulkimportidentitybehavior) controls whether newly generated identity values are returned back to the entities, [BulkImportPseudoTableType](/enumeration/postgresql/bulkimportpseudotabletype) controls the kind of pseudo-temporary staging table created, and [BulkImportMergeCommandType](/enumeration/postgresql/bulkimportmergecommandtype) controls whether [BinaryBulkMerge](/operation/postgresql/binarybulkmerge) is executed via separate `UPDATE`/`INSERT` statements or a single `ON CONFLICT DO UPDATE` statement.
