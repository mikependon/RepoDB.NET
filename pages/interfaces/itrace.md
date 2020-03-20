---
layout: page
title: "ITrace (RepoDb)"
permalink: /interface/itrace
tags: [repodb, class, itrace, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

## ITrace

This is an interfaced that is used to mark a class to be a trace object. This interface provides all the `BEFORE` and `AFTER` operation events.

#### Use-Case

If we woud like to see and trace the following.

- The actual SQL statements composed by the library.
- The parameter to be used before the execution.
- The actual result of the execution.
- The elapsed execution time.

Also, you can use this class to make an audit and cancel the operation before even the actual execution. Please visit the [TraceLog](/class/tracelog) and [CancellableTraceLog](/class/cancellabletracelog) classes to see more information about the trace logging.

#### How to Implement?

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

#### How to Use?

You can pass it in any extended [fluent methods](/docs#fluent-methods) of the `DbConnection` object.

```csharp
using (var connection = new SqlConnection(connectionString))
{
        var person = connection.Query<Person>(p => p.Id == 10045, trace: new MyCustomTrace()).FirstOrDefault();
}
```

Or by passing it on the constructor of the [BaseRepository](/class/baserepository) object.

```csharp
public class PersonRepository : BaseRepository<Person, SqlConnection>
{
        public PersonRepository(ISettings settings)
                : base(settings.ConnectionString, new MyCustomTrace())
        { }
}

using (var repository = new PersonRepository(new AppSettings()))
{
        var person = repository.Query(p => p.Id == 10045).FirstOrDefault();
}
```

Or even to the constructor of [DbRepository](/class/dbrepository) object.

```csharp
public class PersonRepository : DbRepository<SqlConnection>
{
        public PersonRepository(ISettings settings)
                : base(settings.ConnectionString, new MyCustomTrace())
        { }
}

using (var repository = new PersonRepository(new AppSettings()))
{
        var person = repository.Query<Person>(p => p.Id == 10045).FirstOrDefault();
}
```

> Please consider to only create a single trace object within the application and have it passed as a Singleton object in any operations or reporitories.