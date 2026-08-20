---
layout: default
title: "SQLite"
has_children: true
permalink: /class/sqlite
parent: CLASSES
---

# Classes (SQLite)

---

Classes specific to the SQLite data provider ([RepoDb.SqLite](https://www.nuget.org/packages/RepoDb.SqLite)). RepoDB supports SQLite through two ADO.NET drivers — [Microsoft.Data.Sqlite](https://www.nuget.org/packages/Microsoft.Data.Sqlite) ("Mds") and [System.Data.SQLite.Core](https://www.nuget.org/packages/System.Data.SQLite.Core) ("Sds") — reflected in the driver-specific type resolvers ([MdsSqLiteDbTypeNameToClientTypeResolver](/class/sqlite/mdssqlitedbtypenametoclienttyperesolver) and [SdsSqLiteDbTypeNameToClientTypeResolver](/class/sqlite/sdssqlitedbtypenametoclienttyperesolver)). The remaining classes cover connection bootstrapping and configuration ([SqliteBootstrap](/class/sqlite/sqlitebootstrap), [SqliteGlobalConfiguration](/class/sqlite/sqliteglobalconfiguration)), schema discovery ([SqLiteDbHelper](/class/sqlite/sqlitedbhelper)), SQL generation ([SqLiteStatementBuilder](/class/sqlite/sqlitestatementbuilder)), provider settings ([SqLiteDbSetting](/class/sqlite/sqlitedbsetting)), and further type resolution ([DbTypeToSqLiteStringNameResolver](/class/sqlite/dbtypetosqlitestringnameresolver), [SqLiteConvertFieldResolver](/class/sqlite/sqliteconvertfieldresolver)).
