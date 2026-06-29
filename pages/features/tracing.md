---
layout: default
sidebar: features
title: "Tracing"
description: "This is a feature that would allow you to log, audit and debug the command execution context (i.e.: SQL Statement, Parameters, Elapsed Time) via TraceLog class."
permalink: /feature/tracing
tags: [repodb, tracing]
parent: FEATURES
---

# Tracing

---

This feature allows you to log, audit, and debug the command execution context (e.g., SQL statement, parameters, elapsed time) via the [TraceLog](/class/tracelog) class. It also enables cancellation of an execution before it runs via [CancellableTraceLog](/class/cancellabletracelog), and inspection of execution results via [ResultTraceLog](/class/resulttracelog).

When tracing is enabled, the `BeforeExecution()` method of the trace class is invoked before the actual database execution, and `AfterExecution()` is invoked after. Each trace execution has its own dedicated session, keyed by the operation name (e.g., [Insert](/operation/insert)) by default.

Breakpoints can be added inside the trace class to enable debugging.

{: .note }
> By default, the operation name is used as the tracing key. This can be overridden by passing an explicit value to the `traceKey` argument.

## Create a customize Trace Class

Create a class that implements the [ITrace](/interface/itrace) interface.

```csharp
public class NorthwindTrace : ITrace
{
    public void BeforeExecution(CancellableTraceLog log)
    {
        // Some implementations here
    }

    public void AfterExecution<TResult>(ResultTraceLog<TResult> log)
    {
        // Some implementations here
    }
}
```

In `BeforeExecution`, you can add logic to cancel the operation if needed:

```csharp
public void BeforeExecution(CancellableTraceLog log)
{
    if (log.Key == TraceKeys.Insert && !log.Statement.Contains("INSERT"))
    {
        logger.Warning($"Cancelling the operation with SessionId = {log.SessionId}, Statement = {log.Statement}");
        log.Cancel(true);
    }
}
```

In `AfterExecution`, you can inspect the result of the execution:

```csharp
public void AfterExecution<TResult>(ResultTraceLog<TResult> log)
{
    logger.Info($"Session Id = {log.SessioId}, Total Execution Time (in Seconds) = {log.ExecutionTime.TotalSeconds}, Statement = {log.Statement}");
    logger.Info(log.Result.AsJson()); // AsJson() is just a sample extended method
}
```

## Using a Trace in a Connection

Pass the trace object when calling the operation.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    connection.Insert<Customer>(customer, trace: new NorthwindTrace());
}
```

## Injecting the Trace in the Repository

Inject the trace in the constructor. The following is a sample for the [BaseRepository](/class/baserepository) class.

```csharp
// Repository
public class CustomerRepository : BaseRepository<Customer, SqlConnection>
{
    public CustomerRepository(IOptions<AppSettings> settings)
        : base(settings.Value.ConnectionString, new NorthwindTrace())
    { }

    ...
}

// Use it like this (or via DI)
using (var repository = new CustomerRepository(settings))
{
    ...
}
```

For the [DbRepository](/class/dbrepository) class:

```csharp
// Repository
public class NorthwindRepository : DbRepository<SqlConnection>
{
    public NorthwindRepository(IOptions<AppSettings> settings)
        : base(settings.Value.ConnectionString, new NorthwindTrace())
    { }

    ...
}

// Use it like this (or via DI)
using (var repository = new NorthwindRepository(settings))
{
    ...
}
```

Or via direct class instantiation:

```csharp
// Direct class instantiation of DbRepository
using (var repository = new DbRepository<SqlConnection>(settings.Value.ConnectionString, new NorthwindTrace()))
{
    ...
}
```

## Dependency Injection Implementation

Create a custom interface that extends [ITrace](/interface/itrace).

```csharp
public interface INorthwindTrace : ITrace
{
    // More custom methods
}
```

Then implement it in the trace class.

```csharp
public class NorthwindTrace : INorthwindTrace
{
    ...
}
```

Register it in the services collection.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers();

    // Registration
    services.AddSingleton<INorthwindTrace, NorthwindTrace>();
}
```

Inject it into the repository constructor:

```csharp
public class NorthwindRepository : DbRepository<SqlConnection>
{
    public NorthwindRepository(IOptions<AppSettings> settings,
        INorthwindTrace trace) // Injected
        : base(settings.Value.ConnectionString, trace)
    { }

    ...
}
```

## Create a Trace Factory

If dependency injection is not preferred, a trace factory class ensures a single trace instance is used throughout the application.

```csharp
public static class TraceFactory
{
    private readonly static object syncLock;
    private static ITrace trace = null;

    static TraceFactory()
    {
        syncLock = new object();
    }
    
    public static ITrace CreateTrace()
    {
        if (trace == null)
        {
            lock (syncLock)
            {
                if (trace == null)
                {
                    trace = new NorthwindRepository();
                }
            }
        }
        return trace;
    }
}
```

Use it with an [IDbConnection](https://learn.microsoft.com/en-us/dotnet/api/system.data.idbconnection?view=net-7.0) object:

```csharp
using (var connection = new SqlConnection(connectionString))
{
    connection.Insert<Customer>(customer, trace: TraceFactory.CreateTrace());
}
```

Or via a repository:

```csharp
public class NorthwindRepository : DbRepository<SqlConnection>
{
    public NorthwindRepository(IOptions<AppSettings> settings)
        : base(settings.Value.ConnectionString, TraceFactory.CreateTrace())
    { }

    ...
}
```

{: .note }
> Please visit our [Trace](/reference/trace) reference implementation page for more details.
