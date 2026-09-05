---
layout: default
title: "ClickHouse"
nav_order: "~"
has_children: true
permalink: /interface/clickhouse
parent: INTERFACES
---

# Interfaces (ClickHouse)

---

Interfaces specific to the ClickHouse data provider. [IClickHouseBulkDbSetting](/interface/clickhouse/iclickhousebulkdbsetting) extends [IDbSetting](/interface/idbsetting) with a mutation-wait setting for the bulk operations, and ships in [RepoDb.ClickHouse.BulkOperations](https://www.nuget.org/packages/RepoDb.ClickHouse.BulkOperations) rather than the core [RepoDb.ClickHouse](https://www.nuget.org/packages/RepoDb.ClickHouse) package.
