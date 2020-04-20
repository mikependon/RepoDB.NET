---
layout: navpage
sidebar: classes
title: "CancellableTraceLog"
description: "A class that is being used to cancel the current trace operation."
permalink: /class/cancellabletracelog
tags: [repodb, class, cancellabletracelog, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# CancellableTraceLog

A trace-logging class that is used to cancel the existing operation. It is used in all *Before* methods of the [ITrace](/interface/itrace) object. It derives from [TraceLog](/class/tracelog) class.

> In this tutorial, we will use the SQL Server as the database and C# as the programming language.

##### Properties

- `IsCancelled` - is used to determine whether the operation has been cancelled.
- `IsThrowException` - is used to determine whether an exception will be thrown back to the actual operation.

##### Methods

- `Cancel` - is used to cancel the actual operation. It accepts the `throwException` argument to define whether an exception will be thrown back to the operation during cancellation.

##### Learnings

Let us say you have a custom trace class named `MyCustomTrace`. Then you pass this object when you call the [Insert](/operation/insert) operation.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
    connection.Insert<Person>(person, trace: new MyCustomTrace());
}
```

You can set a breakpoint at your `MyCustomTrace.BeforeInsert()` method and can call the `Cancel()` method to cancel the operation.

```csharp
public void BeforeInsert(CancellableTraceLog log)
{
    if (log.Statement.IndexOf("INSERT") <= 0)
    {
        log.Cancel(true);
    }
}
```

> By setting the `throwException` argument to `True`, an exception will be thrown back to the actual operation.
