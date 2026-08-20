---
layout: default
title: "Db2"
has_children: true
permalink: /class/db2
parent: CLASSES
---

# Classes (Db2)

---

Classes specific to the Db2 data provider ([RepoDb.Db2](https://www.nuget.org/packages/RepoDb.Db2), built on the [IBM Data Server .NET Provider (Net.IBM.Data.Db2)](https://www.nuget.org/packages/Net.IBM.Data.Db2)). These cover connection bootstrapping and configuration ([Db2Bootstrap](/class/db2/db2bootstrap), [Db2GlobalConfiguration](/class/db2/db2globalconfiguration)), schema discovery ([Db2DbHelper](/class/db2/db2dbhelper)), SQL generation ([Db2StatementBuilder](/class/db2/db2statementbuilder)), provider settings ([Db2DbSetting](/class/db2/db2dbsetting)), type resolution between .NET, `DbType` and Db2 types ([DbTypeToDb2StringNameResolver](/class/db2/dbtypetodb2stringnameresolver), [Db2DbTypeNameToClientTypeResolver](/class/db2/db2dbtypenametoclienttyperesolver), [Db2ConvertFieldResolver](/class/db2/db2convertfieldresolver)), property handlers for CLR types with no native Db2 equivalent ([Db2GuidToByteArrayPropertyHandler](/class/db2/db2guidtobytearraypropertyhandler), [Db2ByteToInt16PropertyHandler](/class/db2/db2bytetoint16propertyhandler)), and the column-mapping support class used by the [Db2 bulk operations](/operation/db2) ([Db2BulkInsertMapItem](/class/db2/db2bulkinsertmapitem)).
