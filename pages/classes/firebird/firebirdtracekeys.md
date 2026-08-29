---
layout: default
sidebar: classes
title: "FirebirdTraceKeys"
description: "A class that holds the constant values of the operation tracing keys used by the Firebird bulk operations."
permalink: /class/firebird/firebirdtracekeys
tags: [repodb, firebirdtracekeys, firebird, bulk]
parent: "Firebird"
grand_parent: CLASSES
---

# FirebirdTraceKeys

---

This class holds the tracing key constants used by the [RepoDb.Firebird.BulkOperations](https://www.nuget.org/packages/RepoDb.Firebird.BulkOperations) bulk operations, for use with [ITrace](/interface/itrace).

## Fields

| Name | Value |
|:-----|:------|
| FirebirdBulkDelete | `"FirebirdBulkDelete"` |
| FirebirdBulkDeleteByKey | `"FirebirdBulkDeleteByKey"` |
| FirebirdBulkInsert | `"FirebirdBulkInsert"` |
| FirebirdBulkMerge | `"FirebirdBulkMerge"` |
| FirebirdBulkUpdate | `"FirebirdBulkUpdate"` |

## Usability

Pass a custom `traceKey` value, or compare against these constants inside a custom [ITrace](/interface/itrace) implementation.

```csharp
using (var connection = new FbConnection(connectionString))
{
    var insertedRows = connection.BulkInsert(people,
        trace: new MyCustomTrace(),
        traceKey: FirebirdTraceKeys.FirebirdBulkInsert);
}
```
