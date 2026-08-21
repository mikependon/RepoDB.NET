---
layout: default
title: "MySqlConnector"
has_children: true
permalink: /class/mysql/mysqlconnector
parent: CLASSES
---

# Classes (MySqlConnector)

---

Classes specific to the `MySqlConnector`-based [RepoDb.MySqlConnector](https://www.nuget.org/packages/RepoDb.MySqlConnector) package. These cover connection bootstrapping and configuration ([MySqlConnectorBootstrap](/class/mysql/mysqlconnector/mysqlconnectorbootstrap), [MySqlConnectorConfiguration](/class/mysql/mysqlconnector/mysqlconnectorconfiguration)), schema discovery ([MySqlConnectorDbHelper](/class/mysql/mysqlconnector/mysqlconnectordbhelper)), SQL generation ([MySqlConnectorStatementBuilder](/class/mysql/mysqlconnector/mysqlconnectorstatementbuilder)), provider settings ([MySqlConnectorDbSetting](/class/mysql/mysqlconnector/mysqlconnectordbsetting)), bulk-insert column mapping ([MySqlConnectorBulkInsertMapItem](/class/mysql/mysqlconnector/mysqlconnectorbulkinsertmapitem)), and type resolution between MySQL column types and .NET CLR types, in both directions ([MySqlConnectorDbTypeNameToClientTypeResolver](/class/mysql/mysqlconnector/mysqlconnectordbtypenametoclienttyperesolver), [MySqlConnectorDbTypeToMySqlStringNameResolver](/class/mysql/mysqlconnector/mysqlconnectordbtypetomysqlstringnameresolver)).

{: .note }
> [RepoDb.MySql](/class/mysql/mysqldata) ships its own, `MySql.Data`-based set of classes — they are not interchangeable. Use the ones from whichever package your project references.
