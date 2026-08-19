---
layout: default
title: "MariaDB"
nav_order: 6
has_children: true
permalink: /enumeration/mariadb
parent: ENUMERATIONS
---

# Enumerations (MariaDB)

---

Enumerations used by the MariaDB bulk operations, split by the underlying driver: [MariaDb](/enumeration/mariadb/mariadb) (`MySql.Data`, via [RepoDb.MariaDb.BulkOperations](/operation/mariadb)) and [MariaDbConnector](/enumeration/mariadb/mariadbconnector) (`MySqlConnector`, via [RepoDb.MariaDbConnector.BulkOperations](/operation/mariadbconnector)). Both packages define a `MariaDbBulkImportIdentityBehavior` and a `MariaDbBulkImportPseudoTableType` enum under the same simple name — they are separately-compiled types and are not interchangeable between the two packages.
