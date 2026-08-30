---
layout: default
sidebar: classes
title: "VerticaTraceKeys"
description: "A class that holds the constant values of the operation tracing keys used by the Vertica bulk operations."
permalink: /class/vertica/verticatracekeys
tags: [repodb, verticatracekeys, vertica, bulk]
parent: "Vertica"
grand_parent: CLASSES
---

# VerticaTraceKeys

---

This class holds the tracing key constants used by the [RepoDb.Vertica.BulkOperations](https://www.nuget.org/packages/RepoDb.Vertica.BulkOperations) bulk operations, for use with [ITrace](/interface/itrace).

## Fields

| Name | Value |
|:-----|:------|
| VerticaBulkDelete | `"VerticaBulkDelete"` |
| VerticaBulkDeleteByKey | `"VerticaBulkDeleteByKey"` |
| VerticaBulkInsert | `"VerticaBulkInsert"` |
| VerticaBulkMerge | `"VerticaBulkMerge"` |
| VerticaBulkUpdate | `"VerticaBulkUpdate"` |

## Usability

Pass a custom `traceKey` value, or compare against these constants inside a custom [ITrace](/interface/itrace) implementation.

```csharp
using (var connection = new VerticaConnection(connectionString))
{
    var insertedRows = connection.BulkInsert(people,
        trace: new MyCustomTrace(),
        traceKey: VerticaTraceKeys.VerticaBulkInsert);
}
```
