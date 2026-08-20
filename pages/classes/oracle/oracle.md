---
layout: default
title: "Oracle"
has_children: true
permalink: /class/oracle
parent: CLASSES
---

# Classes (Oracle)

---

Classes specific to the Oracle data provider ([RepoDb.Oracle](https://www.nuget.org/packages/RepoDb.Oracle), built on [Oracle.ManagedDataAccess.Core](https://www.nuget.org/packages/Oracle.ManagedDataAccess.Core)). These cover connection bootstrapping and configuration ([OracleBootstrap](/class/oracle/oraclebootstrap), [OracleGlobalConfiguration](/class/oracle/oracleglobalconfiguration)), schema discovery ([OracleDbHelper](/class/oracle/oracledbhelper)), SQL generation ([OracleStatementBuilder](/class/oracle/oraclestatementbuilder)), provider settings ([OracleDbSetting](/class/oracle/oracledbsetting)), type resolution between .NET, `DbType` and Oracle types ([DbTypeToOracleStringNameResolver](/class/oracle/dbtypetooraclestringnameresolver), [OracleDbTypeNameToClientTypeResolver](/class/oracle/oracledbtypenametoclienttyperesolver), [OracleConvertFieldResolver](/class/oracle/oracleconvertfieldresolver)), a `Guid`-to-`RAW(16)` property handler ([OracleGuidToByteArrayPropertyHandler](/class/oracle/oracleguidtobytearraypropertyhandler)), and the support classes used by the [Oracle bulk operations](/operation/oracle) ([OracleBulkInsertMapItem](/class/oracle/oraclebulkinsertmapitem), [OracleTraceKeys](/class/oracle/oracletracekeys)).
