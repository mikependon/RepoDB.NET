---
layout: navpage
sidebar: classes
title: "BaseRepository"
description: "A base repository class of all entity-based repository."
permalink: /class/baserepository
tags: [repodb, class, baserepository, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# BaseRepository

This is the base class of all entity-based repository classes. It accepts 2 generic types, the model type and the `DbConnection` type. It also use the [DbRepository](/class/dbrepository) as the underlying controlling repository.

> In this tutorial, we will use the SQL Server as the database and C# as the programming language.

#### Use-Cases

You should use this class if you wish to work with table-specific operations.

#### How to Implement?

Let us say you have a table named `[dbo].[Person]` and a class named `Person`. Then, you can implement the repository with complete methods like below.

```csharp
public class PersonRepository : BaseRepository<Person, SqlConnection>
{
    public PersonRepository(ISettings settings)
        : base(settings.ConnectionString)
    { }

    public object Create(Person person)
    {
        return Insert(person);
    }

    public Person GetById(int id)
    {
        return Query<Person>(id).FirstOrDefault();
    }

    public IEnumerable<Person> GetAll()
    {
        return QueryAll<Person>();
    }

    public int Update(Person person)
    {
        return base.Update(person);
    }
    
    public int Merge(Person person)
    {
        return base.Merge(person);
    }
    
    public int Remove(Person person)
    {
        return Delete(person);
    }
    
    public int RemoveById(int id)
    {
        return Delete(id);
    }
}
```

> Beware of the recurring calls. Ensure to prepend the `base` keyword if your method name is with the same signature as with the base.

#### How to Use?

Simply create (or inject) a new instance of the class to use the repository.

```csharp
using (var repository = new PersonRepository(settings)) // The settings must be DI(ed) (or,the repository itself must be DI(ed))
{
    var person = repository.Get(10045);
}
```

> A respository is disposable, so please do not forget to wrap it with `using` keyword.

#### Dependency Injection

To make your repository dependency injectable, we recommend you to create your own interface with the necessary methods, then implement it in the class and inject it with your dependency injector.

```csharp
public interface IPersonRepository
{
    object Create(Person person);
    Person GetById(int id);
    IEnumerable<Person> GetAll();
    int Update(Person person);
    int Merge(Person person);
    int Remove(Person person);
    int RemoveById(int id);
}
```

Then implement it in your class.

```csharp
public class PersonRepository : BaseRepository<Person, SqlConnection>, IPersonRepository
{
    public PersonRepository(ISettings settings)
        : base(settings.ConnectionString)
    { }

    ...
}
```

Lastly, inject it.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers();

    // Registration
    services.AddTransient<IPersonRepository, PersonRepository>();
}
```

> Do not use the repository class directly as the injected object to make your design more SOLID (adhering the single-responsibility concepts).

#### The CreateConnection Method

This method is used to create a new instance of connection object. If the value of [Connection Persistency](/enumeration/connectionpersistency) enumeration is `Instance`, then this method returns the existing active connection.

To always force returning the new instance of connection object, simply pass `true` in the `force` argument.

```csharp
using (var connection = CreateConnection(true))
{
    // Use the 'connection' here
}
```

#### Connection Persistency

This property enables your repository to manage the persistency of your connection within the lifespan of the repository. Please have a look at the [Connection Persistency](/enumeration/connectionpersistency) enumeration to see more details.

To enable an instanced-level connection persistency, simply pass the [Connection Persistency](/enumeration/connectionpersistency#instance) value in the constructor.

```csharp
public class PersonRepository : BaseRepository<Person, SqlConnection>
{
    public PersonRepository(ISettings settings)
        : base(settings.ConnectionString, ConnectionPersistency.Instance)
    { }

    ...
}
```

#### Command Timeout

This property is used as the execution timeout of every operation. By default it is null; defaultly using the ADO.NET execution timeout.

To enable your customized timeout, simply pass it on the constructor.

```csharp
public class PersonRepository : BaseRepository<Person, SqlConnection>
{
    public PersonRepository(ISettings settings)
        : base(settings.ConnectionString, 600)
    { }

    ...
}
```

#### Adding a Cache

This property allows the repository to enable the 2nd-layer cache for the purpose of performance. By default, the caching is enabled with the use of [MemoryCache](/class/memorycache). You can override the caching by passing the instance of [ICache](/interface/icache)-based class in the constructor.

```csharp
public class MyCustomCache : ICache
{
    // Your cache implementation
}
```

Then, pass it on the constructor.

```csharp
public class PersonRepository : BaseRepository<Person, SqlConnection>
{
    public PersonRepository(ISettings settings)
        : base(settings.ConnectionString, new MyCustomCache)
    { }

    ...
}
```

#### Adding a Trace

This property allows you as a developer to trace and audit the execution of any operation in the repository. To enable the trace, you have to pass the instance of [ITrace](/interface/itrace)-based class in the constructor.

```csharp
public class MyCustomTrace : ITrace
{
    // Your trace implementation
}
```

Then, pass it on the constructor.

```csharp
public class PersonRepository : BaseRepository<Person, SqlConnection>
{
    public PersonRepository(ISettings settings)
        : base(settings.ConnectionString, new MyCustomTrace)
    { }

    ...
}
```

#### SQL Builder

This property allows you to override the default SQL statement generator of the library. To override, you have to create your custom [Statement Builder](/extensibility/statementbuilder) and pass the instance of [IStatementBuilder](/interface/istatementbuilder)-based class in the constructor.

```csharp
public class OptimizedSqlServerStatementBuilder : IStatementBuilder
{
    // Your trace implementation
}
```

Then, pass it on the constructor.

```csharp
public class PersonRepository : BaseRepository<Person, SqlConnection>
{
    public PersonRepository(ISettings settings)
        : base(settings.ConnectionString, new OptimizedSqlServerStatementBuilder)
    { }

    ...
}
```

> The constructor of this class accepts all the possible combinations of the argument mentioned above.