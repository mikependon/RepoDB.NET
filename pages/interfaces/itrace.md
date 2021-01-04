---
layout: default
sidebar: interfaces
title: "ITrace"
permalink: /interface/itrace
tags: [repodb, class, itrace, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
parent: INTERFACES
---

# ITrace

---

This interface is used to mark a class to be a trace object. This interface provides all the `BEFORE` and `AFTER` operation events.

### Methods

Below are the `BEFORE` methods available from this interface.

- `BeforeAverage` - triggered before the [Average](/operation/average) method is being executed.
- `BeforeAverageAll` - triggered before the [AverageAll](/operation/averageall) method is being executed.
- `BeforeBatchQuery` - triggered before the [BatchQuery](/operation/batchquery) method is being executed.
- `BeforeCount` - triggered before the [Count](/operation/count) method is being executed.
- `BeforeCountAll` - triggered before the [CountAll](/operation/countall) method is being executed.
- `BeforeDelete` - triggered before the [Delete](/operation/delete) method is being executed.
- `BeforeDeleteAll` - triggered before the [DeleteAll](/operation/deleteall) method is being executed.
- `BeforeExists` - triggered before the [Exists](/operation/exists) method is being executed.
- `BeforeExecuteNonQuery` - triggered before the [ExecuteNonQuery](/operation/executenonquery) method is being executed.
- `BeforeExecuteQuery` - triggered before the [ExecuteQuery](/operation/executequery) method is being executed.
- `BeforeExecuteReader` - triggered before the [ExecuteReader](/operation/executereader) method is being executed.
- `BeforeExecuteScalar` - triggered before the [ExecuteScalar](/operation/executescalar) method is being executed.
- `BeforeInsert` - triggered before the [Insert](/operation/insert) method is being executed.
- `BeforeInsertAll` - triggered before the [InsertAll](/operation/insertall) method is being executed.
- `BeforeMax` - triggered before the [Max](/operation/max) method is being executed.
- `BeforeMaxAll` - triggered before the [MaxAll](/operation/maxall) method is being executed.
- `BeforeMerge` - triggered before the [Merge](/operation/merge) method is being executed.
- `BeforeMergeAll` - triggered before the [MergeAll](/operation/mergeall) method is being executed.
- `BeforeMin` - triggered before the [Min](/operation/min) method is being executed.
- `BeforeMinAll` - triggered before the [MinAll](/operation/minall) method is being executed.
- `BeforeQuery` - triggered before the [Query](/operation/query) method is being executed.
- `BeforeQueryAll` - triggered before the [QueryAll](/operation/queryall) method is being executed.
- `BeforeQueryMultiple` - triggered before the [QueryMultiple](/operation/querymultiple) method is being executed.
- `BeforeSum` - triggered before the [Sum](/operation/sum) method is being executed.
- `BeforeSumAll` - triggered before the [SumAll](/operation/sumall) method is being executed.
- `BeforeTruncate` - triggered before the [Truncate](/operation/truncate) method is being executed.
- `BeforeUpdate` - triggered before the [Update](/operation/update) method is being executed.
- `BeforeUpdateAll` - triggered before the [UpdateAll](/operation/updateall) method is being executed.

Below are the `AFTER` methods available from this interface.

- `AfterAverage` - triggered after the [Average](/operation/average) method has been executed.
- `AfterAverageAll` - triggered after the [AverageAll](/operation/averageall) method has been executed.
- `AfterBatchQuery` - triggered after the [BatchQuery](/operation/batchquery) method has been executed.
- `AfterCount` - triggered after the [Count](/operation/count) method has been executed.
- `AfterCountAll` - triggered after the [CountAll](/operation/countall) method has been executed.
- `AfterDelete` - triggered after the [Delete](/operation/delete) method has been executed.
- `AfterDeleteAll` - triggered after the [DeleteAll](/operation/deleteall) method has been executed.
- `AfterExists` - triggered after the [Exists](/operation/exists) method has been executed.
- `AfterExecuteNonQuery` - triggered after the [ExecuteNonQuery](/operation/executenonquery) method has been executed.
- `AfterExecuteQuery` - triggered after the [ExecuteQuery](/operation/executequery) method has been executed.
- `AfterExecuteReader` - triggered after the [ExecuteReader](/operation/executereader) method has been executed.
- `AfterExecuteScalar` - triggered after the [ExecuteScalar](/operation/executescalar) method has been executed.
- `AfterInsert` - triggered after the [Insert](/operation/insert) method has been executed.
- `AfterInsertAll` - triggered after the [InsertAll](/operation/insertall) method has been executed.
- `AfterMax` - triggered after the [Max](/operation/max) method has been executed.
- `AfterMaxAll` - triggered after the [MaxAll](/operation/maxall) method has been executed.
- `AfterMerge` - triggered after the [Merge](/operation/merge) method has been executed.
- `AfterMergeAll` - triggered after the [MergeAll](/operation/mergeall) method has been executed.
- `AfterMin` - triggered after the [Min](/operation/min) method has been executed.
- `AfterMinAll` - triggered after the [MinAll](/operation/minall) method has been executed.
- `AfterQuery` - triggered after the [Query](/operation/query) method has been executed.
- `AfterQueryAll` - triggered after the [QueryAll](/operation/queryall) method has been executed.
- `AfterQueryMultiple` - triggered after the [QueryMultiple](/operation/querymultiple) method has been executed.
- `AfterSum` - triggered after the [Sum](/operation/sum) method has been executed.
- `AfterSumAll` - triggered after the [SumAll](/operation/sumall) method has been executed.
- `AfterTruncate` - triggered after the [Truncate](/operation/truncate) method has been executed.
- `AfterUpdate` - triggered after the [Update](/operation/update) method has been executed.
- `AfterUpdateAll` - triggered after the [UpdateAll](/operation/updateall) method has been executed.

### Use-Cases

If you woud like to see and trace the following.

- The actual SQL statements composed by the library.
- The parameter to be used before the execution.
- The actual result of the execution.
- The elapsed execution time.

Also, you can use this class to make an audit and cancel the operation before even the actual execution. Please visit the [TraceLog](/class/tracelog) and [CancellableTraceLog](/class/cancellabletracelog) classes to see more information about the trace logging.

### How to Implement?

You have to manually create a class that implements this interface.

```csharp
public class MyCustomTrace : ITrace
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

With this, you can log the statement like below.

```csharp
public class MyCustomTrace : ITrace
{
    public void AfterQuery(TraceLog log)
    {
        Console.WriteLine($"After Query: {log.Statement}");
    }

    public void BeforeQuery(CancellableTraceLog log)
    {
        Console.WriteLine($"Before Query: {log.Statement}");
    }

    ...
}
```

Or you can even audit and cancel the operation like below.

```csharp
public class MyCustomTrace : ITrace
{
    public void BeforeQuery(CancellableTraceLog log)
    {
        var invalidActions = new [] { "INSERT", "UDPATE", "DELETE", "DROP", "ALTER", "EXECUTE" };
        if (invalidActions.Any(action => log.Statement.ToUpper().Indexof(action) >= 0))
        {
            Console.WriteLine($"Before Query: A suspicious statement has been passed (SQL = {log.Statement}).");
            log.Cancel(true);
        }
    }

    ...
}
```

> All the [fluent methods](/docs#fluent-methods) will be having the `BEFORE` and `AFTER` methods within this interface.

### How to use?

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
