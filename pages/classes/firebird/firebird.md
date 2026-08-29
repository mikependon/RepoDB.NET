---
layout: default
title: "Firebird"
has_children: true
permalink: /class/firebird
parent: CLASSES
---

# Classes (Firebird)

---

Classes specific to the Firebird data provider ([RepoDb.Firebird](https://www.nuget.org/packages/RepoDb.Firebird), built on [FirebirdSql.Data.FirebirdClient](https://www.nuget.org/packages/FirebirdSql.Data.FirebirdClient)). These cover connection bootstrapping and configuration ([FirebirdBootstrap](/class/firebird/firebirdbootstrap), [FirebirdConfiguration](/class/firebird/firebirdconfiguration)), schema discovery ([FirebirdDbHelper](/class/firebird/firebirddbhelper)), SQL generation ([FirebirdStatementBuilder](/class/firebird/firebirdstatementbuilder)), provider settings ([FirebirdDbSetting](/class/firebird/firebirddbsetting)), and type resolution between .NET, `DbType` and Firebird types ([DbTypeToFirebirdStringNameResolver](/class/firebird/dbtypetofirebirdstringnameresolver), [FirebirdDbTypeNameToClientTypeResolver](/class/firebird/firebirddbtypenametoclienttyperesolver), [FirebirdConvertFieldResolver](/class/firebird/firebirdconvertfieldresolver), [DbTypeNameToColumnNameResolver](/class/firebird/dbtypenametocolumnnameresolver)).

Also included are the support classes used by the [Firebird bulk operations](/operation/firebird) ([FirebirdCommandBatcher](/class/firebird/firebirdcommandbatcher), [FirebirdCommandBatcherMapItem](/class/firebird/firebirdcommandbatchermapitem), [FirebirdCommandBatcherColumnMappingCollection](/class/firebird/firebirdcommandbatchercolumnmappingcollection), [FirebirdTraceKeys](/class/firebird/firebirdtracekeys)), part of the separate [RepoDb.Firebird.BulkOperations](https://www.nuget.org/packages/RepoDb.Firebird.BulkOperations) package.
