---
layout: default
title: "SAP HANA"
has_children: true
permalink: /class/saphana
parent: CLASSES
---

# Classes (SAP HANA)

---

Classes specific to the SAP HANA data provider ([RepoDb.SapHana](https://www.nuget.org/packages/RepoDb.SapHana), built on [Sap.Data.Hana.Net.v6.0](https://www.nuget.org/packages/Sap.Data.Hana.Net.v6.0)). These cover connection bootstrapping and configuration ([SapHanaBootstrap](/class/saphana/saphanabootstrap), [SapHanaConfiguration](/class/saphana/saphanaconfiguration)), schema discovery ([SapHanaDbHelper](/class/saphana/saphanadbhelper)), SQL generation ([SapHanaStatementBuilder](/class/saphana/saphanastatementbuilder)), provider settings ([SapHanaDbSetting](/class/saphana/saphanadbsetting)), and type resolution between .NET, `HanaDbType`, and SAP HANA types ([SapHanaDbTypeNameToClientTypeResolver](/class/saphana/saphanadbtypenametoclienttyperesolver), [SapHanaDbTypeToStringNameResolver](/class/saphana/saphanadbtypetostringnameresolver)).

{: .note }
> Unlike Db2, Firebird, Oracle, PostgreSQL, and Vertica, SAP HANA has no `ConvertFieldResolver`/property-handler classes of its own — [SapHanaStatementBuilder](/class/saphana/saphanastatementbuilder) is constructed with `null` for both, falling back to [BaseStatementBuilder](/class/basestatementbuilder)'s own defaults.

Also included are the support classes used by the [SAP HANA bulk operations](/operation/saphana) ([SapHanaBulkInsertMapItem](/class/saphana/saphanabulkinsertmapitem), [SapHanaTraceKeys](/class/saphana/saphanatracekeys)), part of the separate [RepoDb.SapHana.BulkOperations](https://www.nuget.org/packages/RepoDb.SapHana.BulkOperations) package.
