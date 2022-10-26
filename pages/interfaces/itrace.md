---
layout: default
sidebar: interfaces
title: "ITrace"
permalink: /interface/itrace
tags: [repodb, itrace]
parent: INTERFACES
---

# ITrace

---

This interface is used to mark a class to be a trace object.

### Methods

Below are the `BEFORE` methods available from this interface.

- `BeforeExecution` - triggered before the method is being executed.
- `AfterExecution` - triggered after the method has been executed.

**Note:** The methods mentioned above has its corresponding `Async` methods.

### Use-Cases

If you woud like to see and trace the following.

- The actual SQL statements composed by the library.
- The parameter to be used before the execution.
- The actual result of the execution.
- The elapsed execution time.

Also, you can use this class to make an audit and cancel the operation before even the actual execution. Please visit the [TraceLog](/class/tracelog), [ResultTraceLog](/class/resulttracelog) and [CancellableTraceLog](/class/cancellabletracelog) classes to see more information about the trace logging.

### How to Implement?

You have to manually create a class that implements this interface.

```csharp
public class MyCustomTrace : ITrace
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

With this, you can log the statement like below.

```csharp
public class MyCustomTrace : ITrace
{

    public void BeforeExecution(CancellableTraceLog log)
    {
        Console.WriteLine($"Before Execution: {log.SessionId}, {log.Key}, {log.Statement}");
    }

    public void AfterExecution<TResult>(ResultTraceLog<TResult> log)
    {
        Console.WriteLine($"After Execution: {log.SessionId}, {log.Key}, {log.Statement}");
    }
}
```

Or you can even audit and cancel the operation like below.

```csharp
public class MyCustomTrace : ITrace
{
    public void BeforeExecution(CancellableTraceLog log)
    {
        var invalidActions = new [] { "INSERT", "UDPATE", "DELETE", "DROP", "ALTER", "EXECUTE" };
        if (invalidActions.Any(action => log.Statement.ToUpper().Indexof(action) >= 0))
        {
            Console.WriteLine($"Before Execution: A suspicious statement has been passed (SQL = {log.Statement}).");
            log.Cancel(true);
        }
    }

    ...
}
```

### Usability

First, as a recommendation, create a factory class that returns the trace.

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
                    _trace = new MyCustomTrace();
                }
            }
        }
        return _trace;
    }
}
```

Then, you can pass it in any extended [fluent methods](/docs#fluent-methods) of the `DbConnection` object like below.

```csharp
using (var connection = new SqlConnection(connectionString))
{
        var person = connection.Query<Person>(p => p.Id == 10045, trace: TraceFactory.CreateTracer()).FirstOrDefault();
}
```

Or by passing it on the constructor of the [BaseRepository](/class/baserepository) object.

```csharp
public class PersonRepository : BaseRepository<Person, SqlConnection>
{
    public PersonRepository(ISettings settings)
        : base(settings.Value.ConnectionString, TraceFactory.CreateTracer())
    { }
}

using (var repository = new PersonRepository(new AppSettings()))
{
    var person = repository.Query(p => p.Id == 10045).FirstOrDefault();
}
```

Or even to the constructor of [DbRepository](/class/dbrepository) object.

```csharp
public class DatabaseRepository : DbRepository<SqlConnection>
{
    public DatabaseRepository(ISettings settings)
        : base(settings.Value.ConnectionString, TraceFactory.CreateTracer())
    { }
}

using (var repository = new DatabaseRepository(new AppSettings()))
{
    var person = repository.Query<Person>(p => p.Id == 10045).FirstOrDefault();
}
```

> Always consider to only create a single trace object within the application and have it passed as a singleton object in any operations or reporitories. This is the reason why we had created the factory class above.
