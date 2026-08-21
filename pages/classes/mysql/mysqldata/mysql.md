---
layout: default
title: "MySql"
has_children: true
permalink: /class/mysql/mysqldata
parent: CLASSES
---

# Classes (MySql)

---

Classes specific to the `MySql.Data`-based [RepoDb.MySql](https://www.nuget.org/packages/RepoDb.MySql) package. These cover connection bootstrapping and configuration ([MySqlBootstrap](/class/mysql/mysqldata/mysqlbootstrap), [MySqlGlobalConfiguration](/class/mysql/mysqldata/mysqlglobalconfiguration)), schema discovery ([MySqlDbHelper](/class/mysql/mysqldata/mysqldbhelper)), SQL generation ([MySqlStatementBuilder](/class/mysql/mysqldata/mysqlstatementbuilder)), provider settings ([MySqlDbSetting](/class/mysql/mysqldata/mysqldbsetting)), and type resolution between MySQL column types and .NET CLR types, in both directions ([MySqlDbTypeNameToClientTypeResolver](/class/mysql/mysqldata/mysqldbtypenametoclienttyperesolver), [MySqlDbTypeToMySqlStringNameResolver](/class/mysql/mysqldata/mysqldbtypetomysqlstringnameresolver)).

{: .note }
> [RepoDb.MySqlConnector](/class/mysql/mysqlconnector) ships its own, separately-compiled set of classes under similar names — they are not interchangeable. Use the ones from whichever package your project references.
