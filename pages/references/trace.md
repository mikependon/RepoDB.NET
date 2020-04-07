---
layout: navpage
sidebar: references
title: "Trace Reference (RepoDb)"
permalink: /reference/trace
tags: [repodb, class, trace, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# Trace

This page contains the recommended way of implementing a trace object.

#### Class Creation

Create an interface that implements the [ITrace](/interface/itrace) to support the dependency injections.

```csharp
public interface INorthwindTrace : ITrace
{ }
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

#### Usability

Create a factory class.

```csharp
public static class TraceFactory
{
    private static object m_syncLock = new object();
    private static ITrace m_trace = null;
    
    public static ITrace CreateTracer()
    {
        if (m_trace == null)
        {
            lock (m_syncLock)
            {
                if (m_trace == null)
                {
                    m_trace = new NorthwindTrace();
                }
            }
        }
        return m_trace;
    }
}
```

Or inject it as Singleton.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers();

    // Registration
    services.AddSingleton<INorthwindTrace, NorthwindTrace>();
}
```

> Please refer to our [SerilogTrace](/reference/serilogtrace) (In-Progress) page for for more information.