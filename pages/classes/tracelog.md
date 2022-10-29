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

A trace-logging class that is used when executing an operation. It provides vital information of the actual execution via the [ITrace](/interface/itrace) object.

## Properties

Below is the list of properties.

| Name | Description |
|:-----|:------------|
| SessionId | Handles the identifier of the trace execution. |
| Key | Handles the key of the of the actual execution. By default, the value is the name of the operation that is being executed. |

## Tracing

All methods of the [ITrace](/interface/itrace) interface is using this class. You can debug to any method of the [ITrace](/interface/itrace)-based classes to trace the actual execution information.

Let us say you have a custom trace class named `MyCustomTrace`. Then, you pass this object when you call the [Insert](/operation/insert) operation.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    connection.Insert<Person>(person, trace: new MyCustomTrace());
}
```

Then, you can set a breakpoint at your `MyCustomTrace.BeforeExecution()` method and see the actual trace information before the actual execution via [CancellableTraceLog](/class/cancellabletracelog) argument.

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

Also, you can set a breakpoint at your `MyCustomTrace.AfterExecution()` method and see the actual trace information after the insert operation.

```csharp
public void AfterExecution<TResult>(ResultTraceLog<TResult> log)
{
    Console.WriteLine($"AfterExecution: {log.Statement}, TotalTime: {log.ExecutionTime.TotalSeconds} second(s)");
}
```

> You can do the same in all operational method of the [ITrace](/interface/itrace)-based classes.