---
layout: default
sidebar: classes
title: "SapHanaTraceKeys"
description: "A class that holds the constant values of the operation tracing keys used by the SAP HANA bulk operations."
permalink: /class/saphana/saphanatracekeys
tags: [repodb, saphanatracekeys, saphana, bulk]
parent: "SAP HANA"
grand_parent: CLASSES
---

# SapHanaTraceKeys

---

This class holds the tracing key constants used by the [RepoDb.SapHana.BulkOperations](https://www.nuget.org/packages/RepoDb.SapHana.BulkOperations) bulk operations, for use with [ITrace](/interface/itrace).

## Fields

| Name | Value |
|:-----|:------|
| SapHanaBulkDelete | `"SapHanaBulkDelete"` |
| SapHanaBulkDeleteByKey | `"SapHanaBulkDeleteByKey"` |
| SapHanaBulkInsert | `"SapHanaBulkInsert"` |
| SapHanaBulkMerge | `"SapHanaBulkMerge"` |
| SapHanaBulkUpdate | `"SapHanaBulkUpdate"` |

## Usability

Pass a custom `traceKey` value, or compare against these constants inside a custom [ITrace](/interface/itrace) implementation.

```csharp
using (var connection = new HanaConnection(connectionString))
{
    var insertedRows = connection.BulkInsert(people,
        trace: new MyCustomTrace(),
        traceKey: SapHanaTraceKeys.SapHanaBulkInsert);
}
```
