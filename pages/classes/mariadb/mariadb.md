---
layout: default
title: "MariaDB"
nav_order: 8
has_children: true
permalink: /class/mariadb
parent: CLASSES
---

# Classes (MariaDB)

---

RepoDB supports MariaDB through two separate, drop-in-compatible driver packages, both exposing the same `MariaDbConnection`/`MariaDbParameter`/`MariaDbType` API surface — [MariaDb](/class/mariadb/mariadb) ([RepoDb.MariaDb](https://www.nuget.org/packages/RepoDb.MariaDb), built on [RepoDb.Connector.MariaDb](https://www.nuget.org/packages/RepoDb.Connector.MariaDb), a thin wrapper over [MySql.Data](https://www.nuget.org/packages/MySql.Data)) and [MariaDbConnector](/class/mariadb/mariadbconnector) ([RepoDb.MariaDbConnector](https://www.nuget.org/packages/RepoDb.MariaDbConnector), built on [RepoDb.Connector.MariaDbConnector](https://www.nuget.org/packages/RepoDb.Connector.MariaDbConnector), a thin wrapper over [MySqlConnector](https://www.nuget.org/packages/MySqlConnector)). Install only the one that matches the ADO.NET driver already used by your application — the classes are grouped by which package they belong to.
