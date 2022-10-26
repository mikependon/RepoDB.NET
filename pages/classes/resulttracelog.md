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

A trace-logging class that holds the reuslt of the trace operation. It derives from [TraceLog](/class/tracelog) class.

#### Properties

- `ExecutionTime` - handles the total elapsed time of the actual execution.
- `Result` - handles the actual execution result.
- `CancellableTraceLog` - handles the actual reference to the associated [CancellableTracelog](/class/cancellabletracelog) object.

#### Learnings

Let us say you have a custom trace class named `MyCustomTrace`. Then, you pass this object when you call the [Insert](/operation/insert) operation.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    connection.Insert<Person>(person, trace: new MyCustomTrace());
}
```

You can set a breakpoint at your `MyCustomTrace.AfterExecution()` and identify the actual results.

```csharp
public void AfterExecution<TResult>(ResultTraceLog<TResult> log)
{
    Console.WriteLine($"AfterExecution: {log.Statement}, TotalTime: {log.ExecutionTime.TotalSeconds} second(s)");
}
```

> By setting the `throwException` argument to `true`, an exception will be thrown back to the actual operation.
