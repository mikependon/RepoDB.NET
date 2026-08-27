---
layout: default
title: "Firebird"
has_children: true
permalink: /class/firebird
parent: CLASSES
---

# Classes (Firebird)

---

Classes specific to the Firebird data provider ([RepoDb.Firebird](https://www.nuget.org/packages/RepoDb.Firebird), built on [FirebirdSql.Data.FirebirdClient](https://www.nuget.org/packages/FirebirdSql.Data.FirebirdClient)). These cover connection bootstrapping and configuration ([FirebirdBootstrap](/class/firebird/firebirdbootstrap), [FirebirdConfiguration](/class/firebird/firebirdconfiguration)), schema discovery ([FirebirdDbHelper](/class/firebird/firebirddbhelper)), SQL generation ([FirebirdStatementBuilder](/class/firebird/firebirdstatementbuilder)), provider settings ([FirebirdDbSetting](/class/firebird/firebirddbsetting)), and type resolution between .NET, `DbType` and Firebird types ([DbTypeToFirebirdStringNameResolver](/class/firebird/dbtypetofirebirdstringnameresolver), [FirebirdDbTypeNameToClientTypeResolver](/class/firebird/firebirddbtypenametoclienttyperesolver), [FirebirdConvertFieldResolver](/class/firebird/firebirdconvertfieldresolver)).
