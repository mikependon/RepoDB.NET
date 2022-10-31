---
layout: default
sidebar: classes
title: "BaseRepository"
description: "A base class that is being used to implement an entity-based repositories."
permalink: /class/baserepository
tags: [repodb, baserepository]
parent: CLASSES
---

# BaseRepository

---

This is the base class of all entity-based repository classes. It accepts 2 generic types, the model type and the connection type. It uses the [DbRepository](/class/dbrepository) as the underlying controlling repository.

## Use-Cases

You should inherit this class if you wish to create a repository that is meant for processing a single entity model/table only.

## Implementation

Let us say you have a table named `[dbo].[Person]` and a class named `Person`.

First, create a customized entity-based repository interface.

```csharp
public interface IPersonRepository<TEntity, TPrimaryKey>
{
    int Create(Person person);
    Person GetById(int id);
    IEnumerable<Person> GetAll();
    int Update(Person person);
    int Merge(Person person);
    int Remove(Person person);
    int RemoveById(int id);
}
```

Secondly, create a class that inherits from [BaseRepository](/class/baserepository) class. In this class, implement the newly created interface-repository above.

```csharp
public class PersonRepository : BaseRepository<Person, SqlConnection>, IPersonRepository
{
    public PersonRepository(string connectionString)
        : base(connectionString)
    { }
}
```

Then, implement the needed interface methods.

#### Create

```csharp
public int Create(Person person)
{
    return Insert<int>(person);
}
```

#### Get

```csharp
public Person GetById(int id)
{
    return Query(id).FirstOrDefault();
}

public IEnumerable<Person> GetAll()
{
    return QueryAll();
}
```

#### Update

```csharp
public int Update(Person person)
{
    return base.Update(person);
}
```

#### Merge

```csharp
public int Merge(Person person)
{
    return base.Merge(person);
}
```

#### Remove

```csharp
public int Remove(Person person)
{
    return Delete(person);
}

public int RemoveById(int id)
{
    return Delete(id);
}
```

{: .warning }
> Beware of the recurring calls. Ensure to prepend the `base` keyword if your method name is with the same signature as with the base. Please visit our [BaseRepository](/reference/baserepository) reference implementation page for the detailed implementation.

## Usability

Simply create a new instance of the class to use the repository.

```csharp
// The settings must be DI(ed) (or,the repository itself must be DI(ed))
using (var repository = new PersonRepository(settings.Value.ConnectionString))
{
    var person = repository.Get(10045);
}
```

{: .important }
> A respository is disposable, so please do not forget to wrap it with `using` keyword.

## Dependency Injection

Simply register the `IPersonRepository` interface and the `PersonRepository` class in the service registration.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers();

    // Registration
    services.AddTransient<IPersonRepository, PersonRepository>();
}
```

{: .important }
> Avoid using the repository class directly as the injected object to make your design more SOLID (adhering the single-responsibility concepts).

## The CreateConnection Method

This method is used to create a new instance of connection object. If the value of [Connection Persistency](/enumeration/connectionpersistency) enumeration is `Instance`, then this method returns the existing active connection.

To always force returning the new instance of connection object, simply pass `true` in the `force` argument.

```csharp
using (var connection = CreateConnection(true))
{
    // Use the 'connection' here
}
```

## Connection Persistency

This property enables your repository to manage the persistency of your connection within the lifespan of the repository. Please have a look at the [Connection Persistency](/enumeration/connectionpersistency) enumeration to see more details.

To enable an instanced-level connection persistency, simply pass the [Connection Persistency](/enumeration/connectionpersistency#instance) value in the constructor.

```csharp
public class PersonRepository : BaseRepository<Person, SqlConnection>, IPersonRepository
{
    public PersonRepository(string connectionString)
        : base(connectionString, ConnectionPersistency.Instance)
    { }

    ...
}
```

## Command Timeout

This property is used as the execution timeout of every operation. By default it is null; defaultly using the ADO.NET execution timeout.

To enable your customized timeout, simply pass it on the constructor.

```csharp
public class PersonRepository : BaseRepository<Person, SqlConnection>, IPersonRepository
{
    public PersonRepository(string connectionString)
        : base(connectionString, 600)
    { }

    ...
}
```

## Adding a Cache

This property allows the repository to enable the 2nd-layer cache for the purpose of performance. By default, the caching is enabled with the use of [MemoryCache](/class/memorycache). You can override the caching by passing the instance of [ICache](/interface/icache)-based class in the constructor.

```csharp
public class MyCustomCache : ICache
{
    // Your cache implementation
}
```

Then, pass it on the constructor.

```csharp
public class PersonRepository : BaseRepository<Person, SqlConnection>, IPersonRepository
{
    public PersonRepository(string connectionString)
        : base(connectionString, new MyCustomCache())
    { }

    ...
}
```

{: .note }
> Please visit our [JSON Cache](/reference/jsoncache) reference implementation page for the detailed implementation about file-based caching using JSON.

## Adding a Trace

This property allows you as a developer to trace and audit the execution of any operation in the repository. To enable the trace, you have to pass the instance of [ITrace](/interface/itrace)-based class in the constructor.

```csharp
public class MyCustomTrace : ITrace
{
    // Your trace implementation
}
```

Then, pass it on the constructor.

```csharp
public class PersonRepository : BaseRepository<Person, SqlConnection>, IPersonRepository
{
    public PersonRepository(string connectionString)
        : base(connectionString, new MyCustomTrace())
    { }

    ...
}
```

{: .note }
> Please visit our [Trace](/reference/trace) reference implementation page for the detailed implementation.

## SQL Builder

This property allows you to override the default SQL statement generator of the library. To override, you have to create your custom [Statement Builder](/extensibility/statementbuilder) and pass the instance of [IStatementBuilder](/interface/istatementbuilder)-based class in the constructor.

```csharp
public class OptimizedSqlServerStatementBuilder : IStatementBuilder
{
    // Your trace implementation
}
```

Then, pass it on the constructor.

```csharp
public class PersonRepository : BaseRepository<Person, SqlConnection>, IPersonRepository
{
    public PersonRepository(string connectionString)
        : base(connectionString, new OptimizedSqlServerStatementBuilder)
    { }

    ...
}
```

{: .note }
> The constructor of this class accepts all the possible combinations of the argument mentioned above.
