---
layout: default
sidebar: classes
title: "ResultTraceLog"
description: "A class that handles the result of the trace operation."
permalink: /class/resulttracelog
tags: [repodb, ResultTraceLog]
parent: CLASSES
---

# ResultTraceLog

---

A trace-logging class that holds the result of a trace operation. It derives from [TraceLog](/class/tracelog) and is passed to all `AfterExecution` methods of the [ITrace](/interface/itrace) object.

## Properties

| Name | Description |
|:-----|:------------|
| ExecutionTime | The total elapsed time of the operation. |
| Result | The result of the operation. |
| CancellableTraceLog | A reference to the associated [CancellableTracelog](/class/cancellabletracelog) object. |

## Implementation

Pass a custom trace object when calling an operation such as [Insert](/operation/insert).

```csharp
using (var connection = new SqlConnection(connectionString))
{
    connection.Insert<Person>(person, trace: new MyCustomTrace());
}
```

In the `AfterExecution()` method, inspect the trace results.

```csharp
public void AfterExecution<TResult>(ResultTraceLog<TResult> log)
{
    Console.WriteLine($"AfterExecution: {log.Statement}, TotalTime: {log.ExecutionTime.TotalSeconds} second(s)");
}
```

{: .important }
> Setting the `throwException` argument to `true` causes an exception to be thrown back to the caller.
