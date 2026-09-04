---
layout: default
title: "EnterpriseDB"
has_children: true
permalink: /class/enterprisedb
parent: CLASSES
---

# Classes (EnterpriseDB)

---

Classes specific to the EnterpriseDB data provider ([RepoDb.EnterpriseDb](https://www.nuget.org/packages/RepoDb.EnterpriseDb), built on the Npgsql-backed [RepoDb.Connector.EnterpriseDb](https://www.nuget.org/packages/RepoDb.Connector.EnterpriseDb) connector). These cover connection bootstrapping and configuration ([EnterpriseDbBootstrap](/class/enterprisedb/enterprisedbbootstrap), [EnterpriseDbGlobalConfiguration](/class/enterprisedb/enterprisedbglobalconfiguration)), schema discovery ([EnterpriseDbDbHelper](/class/enterprisedb/enterprisedbdbhelper)), SQL generation ([EnterpriseDbStatementBuilder](/class/enterprisedb/enterprisedbstatementbuilder), [EnterpriseDbConvertFieldResolver](/class/enterprisedb/enterprisedbconvertfieldresolver)), provider settings ([EnterpriseDbDbSetting](/class/enterprisedb/enterprisedbdbsetting)), and type resolution between .NET, `EDBType`, and EnterpriseDB database types ([EnterpriseDbDbTypeNameToClientTypeResolver](/class/enterprisedb/enterprisedbdbtypenametoclienttyperesolver), [EnterpriseDbDbTypeNameToEDBDbTypeResolver](/class/enterprisedb/enterprisedbdbtypenametoedbdbtyperesolver), [ClientTypeToEDBDbTypeResolver](/class/enterprisedb/clienttypetoedbdbtyperesolver), [DbTypeToEnterpriseDbStringNameResolver](/class/enterprisedb/dbtypetoenterprisedbstringnameresolver)).

Also included are the support classes used by the [EnterpriseDB bulk operations](/operation/enterprisedb) ([EDBBulkInsertMapItem](/class/enterprisedb/edbbulkinsertmapitem), [EDBTraceKeys](/class/enterprisedb/edbtracekeys)), part of the separate [RepoDb.EnterpriseDb.BulkOperations](https://www.nuget.org/packages/RepoDb.EnterpriseDb.BulkOperations) package.
