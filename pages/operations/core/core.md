---
layout: default
title: "Core"
nav_order: 1
has_children: true
permalink: /operation/core
parent: OPERATIONS
---

# Operations (Core)

---

These are the provider-agnostic CRUD and aggregate operations available on every supported RDBMS. They cover fetching data ([Query](/operation/query), [QueryAll](/operation/queryall), [BatchQuery](/operation/batchquery), [QueryMultiple](/operation/querymultiple)), writing data ([Insert](/operation/insert), [InsertAll](/operation/insertall), [Update](/operation/update), [UpdateAll](/operation/updateall), [Merge](/operation/merge), [MergeAll](/operation/mergeall), [Delete](/operation/delete), [DeleteAll](/operation/deleteall)), row/table introspection ([Count](/operation/count), [CountAll](/operation/countall), [Exists](/operation/exists), [Truncate](/operation/truncate)), aggregation ([Average](/operation/average), [AverageAll](/operation/averageall), [Max](/operation/max), [MaxAll](/operation/maxall), [Min](/operation/min), [MinAll](/operation/minall), [Sum](/operation/sum), [SumAll](/operation/sumall)), and raw-SQL execution ([ExecuteQuery](/operation/executequery), [ExecuteQueryMultiple](/operation/executequerymultiple), [ExecuteReader](/operation/executereader), [ExecuteNonQuery](/operation/executenonquery), [ExecuteScalar](/operation/executescalar)). Each operation has both a synchronous and an `Async` counterpart.
