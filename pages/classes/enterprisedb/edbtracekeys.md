---
layout: default
sidebar: classes
title: "EDBTraceKeys"
description: "A class that holds the constant values of the operation tracing keys used by the EnterpriseDB bulk operations."
permalink: /class/enterprisedb/edbtracekeys
tags: [repodb, edbtracekeys, enterprisedb, bulk]
parent: "EnterpriseDB"
grand_parent: CLASSES
---

# EDBTraceKeys

---

This class holds the tracing key constants used by the [RepoDb.EnterpriseDb.BulkOperations](https://www.nuget.org/packages/RepoDb.EnterpriseDb.BulkOperations) bulk operations, for use with [ITrace](/interface/itrace).

## Fields

| Name | Value |
|:-----|:------|
| EDBBulkDelete | `"EDBBulkDelete"` |
| EDBBulkDeleteByKey | `"EDBBulkDeleteByKey"` |
| EDBBulkInsert | `"EDBBulkInsert"` |
| EDBBulkMerge | `"EDBBulkMerge"` |
| EDBBulkUpdate | `"EDBBulkUpdate"` |

## Usability

Pass a custom `traceKey` value, or compare against these constants inside a custom [ITrace](/interface/itrace) implementation.

```csharp
using (var connection = new EDBConnection(connectionString))
{
    var insertedRows = connection.BulkInsert(people,
        trace: new MyCustomTrace(),
        traceKey: EDBTraceKeys.EDBBulkInsert);
}
```
