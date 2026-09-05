---
layout: default
title: "ClickHouse"
has_children: true
permalink: /class/clickhouse
parent: CLASSES
---

# Classes (ClickHouse)

---

Classes specific to the ClickHouse data provider ([RepoDb.ClickHouse](https://www.nuget.org/packages/RepoDb.ClickHouse), built on [ClickHouse.Driver](https://www.nuget.org/packages/ClickHouse.Driver)). These cover connection bootstrapping and configuration ([ClickHouseBootstrap](/class/clickhouse/clickhousebootstrap), [ClickHouseGlobalConfiguration](/class/clickhouse/clickhouseglobalconfiguration)), schema discovery ([ClickHouseDbHelper](/class/clickhouse/clickhousedbhelper)), SQL generation ([ClickHouseStatementBuilder](/class/clickhouse/clickhousestatementbuilder)), provider settings ([ClickHouseDbSetting](/class/clickhouse/clickhousedbsetting)), and type resolution between ClickHouse column types and .NET CLR types ([ClickHouseDbTypeNameToClientTypeResolver](/class/clickhouse/clickhousedbtypenametoclienttyperesolver)).

Also included is the mapping class used by the [ClickHouse bulk operations](/operation/clickhouse) ([ClickHouseBulkInsertMapItem](/class/clickhouse/clickhousebulkinsertmapitem)) and [ClickHouseBulkDbSetting](/class/clickhouse/clickhousebulkdbsetting), a [ClickHouseDbSetting](/class/clickhouse/clickhousedbsetting) subclass controlling mutation-wait blocking, both part of the separate [RepoDb.ClickHouse.BulkOperations](https://www.nuget.org/packages/RepoDb.ClickHouse.BulkOperations) package.
