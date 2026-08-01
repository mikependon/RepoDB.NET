---
layout: default
sidebar: classes
title: "OracleTraceKeys"
description: "A class that holds the constant values of the operation tracing keys used by the Oracle bulk operations."
permalink: /class/oracle/oracletracekeys
tags: [repodb, oracletracekeys, oracle]
parent: "Oracle"
grand_parent: CLASSES
---

# OracleTraceKeys

---

This class extends the core `OracleTraceKeys` with the tracing key constants used by the [RepoDb.Oracle.BulkOperations](https://www.nuget.org/packages/RepoDb.Oracle.BulkOperations) bulk operations, for use with [ITrace](/interface/itrace).

## Fields

| Name | Value |
|:-----|:------|
| OracleBulkDelete | `"OracleBulkDelete"` |
| OracleBulkDeleteByKey | `"OracleBulkDeleteByKey"` |
| OracleBulkInsert | `"OracleBulkInsert"` |
| OracleBulkMerge | `"OracleBulkMerge"` |
| OracleBulkUpdate | `"OracleBulkUpdate"` |

## Usability

Pass a custom `traceKey` value, or compare against these constants inside a custom [ITrace](/interface/itrace) implementation.

```csharp
using (var connection = new OracleConnection(connectionString))
{
    var insertedRows = connection.BulkInsert(people,
        trace: new MyCustomTrace(),
        traceKey: OracleTraceKeys.OracleBulkInsert);
}
```
