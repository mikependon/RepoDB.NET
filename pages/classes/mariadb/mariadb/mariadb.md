---
layout: default
title: "MariaDb"
nav_order: 1
has_children: true
permalink: /class/mariadb/mariadb
parent: "MariaDB"
grand_parent: CLASSES
---

# Classes (MariaDb)

---

Classes specific to the `MySql.Data`-based [RepoDb.MariaDb](https://www.nuget.org/packages/RepoDb.MariaDb) package (via [RepoDb.Connector.MariaDb](https://www.nuget.org/packages/RepoDb.Connector.MariaDb)). These cover connection bootstrapping and configuration ([MariaDbBootstrap](/class/mariadb/mariadb/mariadbbootstrap), [MariaDbGlobalConfiguration](/class/mariadb/mariadb/mariadbglobalconfiguration)), schema discovery ([MariaDbDbHelper](/class/mariadb/mariadb/mariadbdbhelper)), SQL generation ([MariaDbStatementBuilder](/class/mariadb/mariadb/mariadbstatementbuilder)), provider settings ([MariaDbDbSetting](/class/mariadb/mariadb/mariadbdbsetting)), and type resolution between MariaDB column types and .NET CLR types, in both directions ([MariaDbDbTypeNameToClientTypeResolver](/class/mariadb/mariadb/mariadbdbtypenametoclienttyperesolver), [MariaDbDbTypeToStringNameResolver](/class/mariadb/mariadb/mariadbdbtypetostringnameresolver)).

{: .note }
> [RepoDb.MariaDbConnector](/class/mariadb/mariadbconnector) ships its own, separately-compiled set of classes under the same names — they are not interchangeable. Use the ones from whichever package your project references.
