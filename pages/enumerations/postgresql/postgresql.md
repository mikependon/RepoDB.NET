---
layout: default
title: "PostgreSQL"
has_children: true
permalink: /enumeration/postgresql
parent: ENUMERATIONS
---

# Enumerations (PostgreSQL)

---

Enumerations used by the [PostgreSQL binary bulk operations](/operation/postgresql). [PostgreSqlBulkImportIdentityBehavior](/enumeration/postgresql/postgresqlbulkimportidentitybehavior) controls whether newly generated identity values are returned back to the entities, [PostgreSqlBulkImportPseudoTableType](/enumeration/postgresql/postgresqlbulkimportpseudotabletype) controls the kind of pseudo-temporary staging table created, and [PostgreSqlBulkImportMergeCommandType](/enumeration/postgresql/postgresqlbulkimportmergecommandtype) controls whether [BulkMerge](/operation/postgresql/bulkmerge) is executed via separate `UPDATE`/`INSERT` statements or a single `ON CONFLICT DO UPDATE` statement.
