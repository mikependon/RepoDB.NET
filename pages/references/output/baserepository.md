---
layout: page
title: "BaseRepository Reference Output (RepoDb)"
permalink: /reference/output/baserepository
tags: [repodb, class, baserepository, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

#### PersonRepository (BaseRepository)

```csharp
public class PersonRepository : BaseRepository<Person, SqlConnection>, IPersonRepository
{
    public PersonRepository(IOptions<AppSetting> settings)
        : base(settings.ConnectionString,
            commandTimeout: 0,
            connectionPersistency: ConnectionPersistency.PerCall,
            cache: CacheFactory.CreateCacher(),
            cacheItemExpiration: 180 /* 3 hours */,
            trace: TraceFactory.CreateTracer(),
            statementBuilder: null /* Not necessary */ )
    { }

    // Sync

    public IEnumerable<Person> GetAll(string cacheKey = null,
        IDbTransaction transaction = null)
    {
        return QueryAll(cacheKey: cacheKey,
            transaction: transaction);
    }

    public Person Get(int id,
        string cacheKey = null,
        IDbTransaction transaction = null)
    {
        return Query(id,
            cacheKey: cacheKey,
            transaction: transaction).FirstOrDefault();
    }

    public Person GetByName(string name,
        string cacheKey = null,
        IDbTransaction transaction = null)
    {
        return Query(p => p.Name == name,
            cacheKey: cacheKey,
            transaction: transaction).FirstOrDefault();
    }
    
    public object Save(Person person,
        IDbTransaction transaction = null)
    {
        return Insert(person,
            transaction: transaction);
    }

    public int Update(Person person,
        IDbTransaction transaction = null)
    {
        return base.Update(person,
            transaction: transaction);
    }

    // Async

    public async Task<IEnumerable<Person>> GetAllAsync(string cacheKey = null,
        IDbTransaction transaction = null)
    {
        return await QueryAllAsync(cacheKey: cacheKey,
            transaction: transaction);
    }

    public async Task<Person> GetAsync(int id,
        string cacheKey = null,
        IDbTransaction transaction = null)
    {
        return (await QueryAsync(id,
            cacheKey: cacheKey,
            transaction: transaction)).FirstOrDefault();
    }

    public async Task<Person> GetByNameAsync(string name,
        string cacheKey = null,
        IDbTransaction transaction = null)
    {
        return (await QueryAsync(p => p.Name == name,
            cacheKey: cacheKey,
            transaction: transaction)).FirstOrDefault();
    }

    public async Task<object> SaveAsync(Person person,
        IDbTransaction transaction = null)
    {
        return await InsertAsync(person,
            transaction: transaction);
    }

    public async Task<int> UpdateAsync(Person person,
        IDbTransaction transaction = null)
    {
        return await base.UpdateAsync(person,
            transaction: transaction);
    }
}
```

#### Settings

```csharp
public class AppSetting
{
    public string ConnectionString { get; set; }
}
```

#### Dependency Injection (Normal)

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers();

    // Registration
    services.AddTransient<IPersonRepository, PersonRepository>();
}
```

#### Dependency Injection (with Cache or ConnectionPersistency.Instace)

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers();

    // Registration
    services.AddSingleton<IPersonRepository, PersonRepository>();
}
```

#### CacheFactory

```csharp
public static class CacheFactory
{
    private static object m_syncLock = new object();
    private static ICache m_cache = null;
    
    public static ICache CreateCacher()
    {
        if (m_trace == null)
        {
            lock (m_syncLock)
            {
                if (m_trace == null)
                {
                    m_trace = new MyCustomTrace();
                }
            }
        }
        return m_trace;
    }
}
```

#### TraceFactory

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
                    m_trace = new MyCustomTrace();
                }
            }
        }
        return m_trace;
    }
}
```
