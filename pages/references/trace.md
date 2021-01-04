---
layout: default
sidebar: references
title: "Trace"
nav_order: 10
permalink: /reference/trace
tags: [repodb, class, trace, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
parent: REFERENCES
---

# Trace

---

This page contains the recommended way of implementing a trace object that is injectable to the repositories.

### Implementation

Create an interface that implements the [ITrace](/interface/itrace). This is also to support the dependency injection.

```csharp
public interface INorthwindTrace : ITrace
{
    ...
}
```

Then, create a class that implements the custom interface.

```csharp
public class NorthwindTrace : INorthwindTrace
{
    public void AfterQuery(TraceLog log)
    {
        Console.WriteLine(log.Statement);
    }

    public void BeforeQuery(CancellableTraceLog log)
    {
        Console.WriteLine(log.Statement);
    }

    ...
}
```

> Implement all the [ITrace](/interface/itrace) methods manually.

### Factory

Create a trace factory class.

```csharp
public static class TraceFactory
{
    private static object _syncLock = new object();
    private static ITrace _trace = null;
    
    public static ITrace CreateTracer()
    {
        if (_trace == null)
        {
            lock (_syncLock)
            {
                if (_trace == null)
                {
                    _trace = new NorthwindTrace();
                }
            }
        }
        return _trace;
    }
}
```

And pass it in the repository constructor.

```csharp
public class NorthwindRepository : DbRepository<SqlConnection>
{
    public NorthwindRepository(IOptions<AppSettings> settings)
        : base(settings.Value.ConnectionString, TraceFactory.CreateTracer())
    { }

    ...
}
```

### Dependency Injection

Or inject it as a singleton object.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers();

    // Registration
    services.AddSingleton<INorthwindTrace, NorthwindTrace>();
}
```

And inject it in the repository constructor.

```csharp
public class NorthwindRepository : DbRepository<SqlConnection>
{
    public NorthwindRepository(IOptions<AppSettings> settings,
        INorthwindTrace trace)
        : base(settings.Value.ConnectionString, trace)
    { }

    ...
}
```