---
layout: default
title: "SQL Server"
has_children: true
permalink: /class/sqlserver
parent: CLASSES
---

# Classes (SQL Server)

---

Classes specific to the SQL Server data provider ([RepoDb.SqlServer](https://www.nuget.org/packages/RepoDb.SqlServer), built on [Microsoft.Data.SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient)). These cover connection bootstrapping and configuration ([SqlServerBootstrap](/class/sqlserver/sqlserverbootstrap), [SqlServerGlobalConfiguration](/class/sqlserver/sqlserverglobalconfiguration)), schema discovery ([SqlServerDbHelper](/class/sqlserver/sqlserverdbhelper)), SQL generation ([SqlServerStatementBuilder](/class/sqlserver/sqlserverstatementbuilder) — the default statement builder used by the library), provider settings ([SqlServerDbSetting](/class/sqlserver/sqlserverdbsetting)), type resolution ([DbTypeToSqlServerStringNameResolver](/class/sqlserver/dbtypetosqlserverstringnameresolver), [SqlServerDbTypeNameToClientTypeResolver](/class/sqlserver/sqlserverdbtypenametoclienttyperesolver), [SqlServerConvertFieldResolver](/class/sqlserver/sqlserverconvertfieldresolver)), table optimizer hints ([SqlServerTableHints](/class/sqlserver/sqlservertablehints)), and the mapping class used by the [SQL Server bulk operations](/operation/sqlserver) ([SqlServerBulkInsertMapItem](/class/sqlserver/sqlserverbulkinsertmapitem)).
