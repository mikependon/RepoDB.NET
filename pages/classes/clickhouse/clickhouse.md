---
layout: default
title: "ClickHouse"
nav_order: 9
has_children: true
permalink: /class/clickhouse
parent: CLASSES
---

# Classes (ClickHouse)

---

Classes specific to the ClickHouse data provider ([RepoDb.ClickHouse](https://www.nuget.org/packages/RepoDb.ClickHouse), built on [ClickHouse.Driver](https://www.nuget.org/packages/ClickHouse.Driver)). These cover connection bootstrapping and configuration ([ClickHouseBootstrap](/class/clickhouse/clickhousebootstrap), [ClickHouseGlobalConfiguration](/class/clickhouse/clickhouseglobalconfiguration)), schema discovery ([ClickHouseDbHelper](/class/clickhouse/clickhousedbhelper)), SQL generation ([ClickHouseStatementBuilder](/class/clickhouse/clickhousestatementbuilder)), provider settings ([ClickHouseDbSetting](/class/clickhouse/clickhousedbsetting)), type resolution between ClickHouse column types and .NET CLR types ([ClickHouseDbTypeNameToClientTypeResolver](/class/clickhouse/clickhousedbtypenametoclienttyperesolver)), and the mapping class used by the [ClickHouse bulk operations](/operation/clickhouse) ([ClickHouseBulkInsertMapItem](/class/clickhouse/clickhousebulkinsertmapitem)).
