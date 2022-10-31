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

This is a feature that would allow you to log, audit and debug the command execution context (i.e.: SQL Statement, Parameters, Elapsed Time) via [TraceLog](/class/tracelog) class. It also enables you to cancel the existing execution before the actual execution via [CancellableTraceLog](/class/cancellabletracelog) class, and also, enables you to examine the result of the execution via [ResultTraceLog](/class/resulttracelog) class.

By having the trace enabled, if you call an operation, the `BeforeExecution()` method of the trace class will be invoked before the actual execution towards the database, while the `AfterExecution()` method will be invoked after the execution. Every trace execution has its own dedicated session and by default having the name of the operation as the key (i.e.: `Insert`).

Within the trace class, you can add a breakpoint to enable the debugging.

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

In the `BeforeExecution` method, you are enable to put a logic and cancel the operation (if necessary) like below.

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

In the `AfterExecution` method, you are able to identify the result of the execution like below.

```csharp
public void AfterExecution<TResult>(ResultTraceLog<TResult> log)
{
    logger.Info($"Session Id = {log.SessioId}, Total Execution Time (in Seconds) = {log.ExecutionTime.TotalSeconds}, Statement = {log.Statement}");
    logger.Info(log.Result.AsJson()); // AsJson() is just a sample extended method
}
```

## Using a Trace in a Connection

Simply pass the trace object when calling the operation.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    connection.Insert<Customer>(customer, trace: new NorthwindTrace());
}
```

## Injecting the Trace in the Repository

Simply inject it in the contructor. Below is the sample code for [BaseRepository](/class/baserepository) class.

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

And below is for [DbRepository](/class/dbrepository) class.

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

Or via direct class instantiation.

```csharp
// Direct class instantiation of DbRepository
using (var repository = new DbRepository<SqlConnection>(settings.Value.ConnectionString, new NorthwindTrace()))
{
    ...
}
```

## Dependency Injection Implementation

Create a custom interface that implements the [ITrace](/interface/itrace) interface.

```csharp
public interface INorthwindTrace : ITrace
{
    // More custom methods
}
```

Then, implement it in the trace class.

```csharp
public class NorthwindTrace : INorthwindTrace
{
    ...
}
```

Lastly, register in the services collection.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers();

    // Registration
    services.AddSingleton<INorthwindTrace, NorthwindTrace>();
}
```

Below is the code on how to inject it in the repositories.

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

If you do not prefer the dependency injection way, creating a simple trace factory class is a good way to abstract and ensure single instance of trace object is being created.

The code below ensures that only a single instance of trace object is being used all throughout the application.

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

And use it in the `IDbConnection` object like below.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    connection.Insert<Customer>(customer, trace: TraceFactory.CreateTrace());
}
```

Or via repositories.

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
> You can as well visit our [Trace](/reference/trace) reference implementation page for more details.
