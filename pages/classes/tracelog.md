---
layout: default
sidebar: classes
title: "TraceLog"
description: "A class that contains all the information of the trace."
permalink: /class/tracelog
tags: [repodb, tracelog]
parent: CLASSES
---

# TraceLog

---

A trace-logging class used during operation execution. It provides execution details via the [ITrace](/interface/itrace) object.

## Properties

| Name | Description |
|:-----|:------------|
| SessionId | The identifier of the trace execution. |
| Key | The key of the execution. Defaults to the name of the operation being executed. |

## Tracing

All methods of the [ITrace](/interface/itrace) interface use this class. Set breakpoints in [ITrace](/interface/itrace)-based class methods to inspect execution details.

Pass a custom trace object when calling an operation such as [Insert](/operation/insert).

```csharp
using (var connection = new SqlConnection(connectionString))
{
    connection.Insert<Person>(person, trace: new MyCustomTrace());
}
```

In `BeforeExecution()`, inspect or cancel the operation via the [CancellableTraceLog](/class/cancellabletracelog) argument.

```csharp
public void BeforeExecution(CancellableTraceLog log)
{
    Console.WriteLine($"BeforeExecution: {log.Statement}");
    if (log.Statement.IndexOf("INSERT") <= 0)
    {
        log.Cancel(true);
    }
}
```

In `AfterExecution()`, inspect the result after execution.

```csharp
public void AfterExecution<TResult>(ResultTraceLog<TResult> log)
{
    Console.WriteLine($"AfterExecution: {log.Statement}, TotalTime: {log.ExecutionTime.TotalSeconds} second(s)");
}
```

{: .note }
> The same pattern applies to all operational methods of [ITrace](/interface/itrace)-based classes.
