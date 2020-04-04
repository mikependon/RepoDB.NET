---
layout: page
title: "Tracing (RepoDb)"
permalink: /feature/tracing
tags: [repodb, class, tracing, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

## Tracing

This is the feature that would allow you to log, audit and debug the command execution context (ie: SQL Statement, Parameters, Elapsed Time) via [TraceLog](/class/tracelog) class. It also allows you to cancel the existing execution before even the actual execution via [CancellableTraceLog](/class/cancellabletracelog) class.

A corresponding method in the trace class will be hit by the debugger when you call the `IDbConnection` extended method (or any of the `Repository` method), only if the trace object is passed or injected.

To be more precise, if you call the [Insert](/operation/insert) operation, the `BeforeInsert` and `AfterInsert` method of the trace class will be invoked. There you can add the breakpoint to enable the debugging.

> In this tutorial, we will use the SQL Server as the database and C# as the programming language.

#### Create a Customize Trace Class

Create a class that implements the [ITrace](/interface/itrace) interface.

```csharp
public class NorthwindTrace : ITrace
{
    public void AfterQuery(TraceLog log)
    {
        // Some implementations here
    }

    public void BeforeQuery(CancellableTraceLog log)
    {
        // Some implementations here
    }

    ...
}
```

> You have to implement all the interface methods and manually handle each of them. You can leave it empty if you like.

#### Using a Trace in a Connection

Simply pass the trace object when calling the operation.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    connection.Insert<Customer>(customer, trace: new NorthwindTrace());
}
```

#### Injecting the Trace in the Repository

Simply inject it in the contructor. Below is a sample code for [BaseRepository](/class/baserepository) class.

```csharp
// Repository
public class CustomerRepository : BaseRepository<Customer, SqlConnection>
{
    public CustomerRepository(IOptions<AppSettings> settings)
        : base(settings.ConnectionString, new NorthwindTrace())
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
        : base(settings.ConnectionString, new NorthwindTrace())
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
using (var repository = new DbRepository<SqlConnection>(settings.ConnectionString, new NorthwindTrace()))
{
    ...
}
```

#### Create a Trace Factory

Creating a trace `Factory` class is a good way to abstract and ensure single instance of trace object is being created. It is our recommendation when using the trace feature.

The code below ensures that only a single instance of trace object is being used all throughout the application.

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
                    m_trace = new NorthwindRepository();
                }
            }
        }
        return m_trace;
    }
}
```

And use it in the `IDbConnection` object like below.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    connection.Insert<Customer>(customer, trace: TraceFactory.CreateTracer());
}
```

Or via repositories.

```csharp
public class NorthwindRepository : DbRepository<SqlConnection>
{
    public NorthwindRepository(IOptions<AppSettings> settings)
        : base(settings.ConnectionString, TraceFactory.CreateTracer())
    { }

    ...
}
```

#### Injecting a Trace Class

Create a custom interface that implements the [ITrace](/interface/itrace) interface.

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
        : base(settings.ConnectionString, trace)
    { }

    ...
}
```