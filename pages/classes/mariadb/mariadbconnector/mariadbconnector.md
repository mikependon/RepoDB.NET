---
layout: default
title: "MariaDbConnector"
has_children: true
permalink: /class/mariadb/mariadbconnector
parent: CLASSES
---

# Classes (MariaDbConnector)

---

Classes specific to the `MySqlConnector`-based [RepoDb.MariaDbConnector](https://www.nuget.org/packages/RepoDb.MariaDbConnector) package (via [RepoDb.Connector.MariaDbConnector](https://www.nuget.org/packages/RepoDb.Connector.MariaDbConnector)). These cover connection bootstrapping and configuration ([MariaDbBootstrap](/class/mariadb/mariadbconnector/mariadbbootstrap), [MariaDbGlobalConfiguration](/class/mariadb/mariadbconnector/mariadbglobalconfiguration)), schema discovery ([MariaDbDbHelper](/class/mariadb/mariadbconnector/mariadbdbhelper)), SQL generation ([MariaDbStatementBuilder](/class/mariadb/mariadbconnector/mariadbstatementbuilder)), provider settings ([MariaDbDbSetting](/class/mariadb/mariadbconnector/mariadbdbsetting)), and type resolution between MariaDB column types and .NET CLR types, in both directions ([MariaDbDbTypeNameToClientTypeResolver](/class/mariadb/mariadbconnector/mariadbdbtypenametoclienttyperesolver), [MariaDbDbTypeToStringNameResolver](/class/mariadb/mariadbconnector/mariadbdbtypetostringnameresolver)).

{: .note }
> [RepoDb.MariaDb](/class/mariadb/mariadb) ships its own, separately-compiled set of classes under the same names — they are not interchangeable. Use the ones from whichever package your project references.
