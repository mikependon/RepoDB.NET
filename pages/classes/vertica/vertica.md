---
layout: default
title: "Vertica"
has_children: true
permalink: /class/vertica
parent: CLASSES
---

# Classes (Vertica)

---

Classes specific to the Vertica data provider ([RepoDb.Vertica](https://www.nuget.org/packages/RepoDb.Vertica), built on [Vertica.Data](https://www.nuget.org/packages/Vertica.Data)). These cover connection bootstrapping and configuration ([VerticaBootstrap](/class/vertica/verticabootstrap), [VerticaConfiguration](/class/vertica/verticaconfiguration)), schema discovery ([VerticaDbHelper](/class/vertica/verticadbhelper)), SQL generation ([VerticaStatementBuilder](/class/vertica/verticastatementbuilder)), provider settings ([VerticaDbSetting](/class/vertica/verticadbsetting)), type resolution between .NET, `DbType` and Vertica types ([DbTypeToVerticaStringNameResolver](/class/vertica/dbtypetoverticastringnameresolver), [VerticaDbTypeNameToClientTypeResolver](/class/vertica/verticadbtypenametoclienttyperesolver), [VerticaConvertFieldResolver](/class/vertica/verticaconvertfieldresolver), [DbTypeNameToColumnNameResolver](/class/vertica/dbtypenametocolumnnameresolver)), and a property handler for a CLR type mismatch with no native Vertica equivalent ([TimeToDateTimePropertyHandler](/class/vertica/timetodatetimepropertyhandler)).

Also included are the support classes used by the [Vertica bulk operations](/operation/vertica) ([VerticaBulkInsertMapItem](/class/vertica/verticabulkinsertmapitem), [VerticaTraceKeys](/class/vertica/verticatracekeys)), part of the separate [RepoDb.Vertica.BulkOperations](https://www.nuget.org/packages/RepoDb.Vertica.BulkOperations) package.
