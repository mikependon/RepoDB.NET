---
layout: page
title: "DbRepository (RepoDb)"
permalink: /class/dbrepository
tags: [repodb, class, dbrepository, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---


## DbRepository

This is the base class of repository classes. It accepts the type of `DbConnection` as the generic type.

> In this tutorial, we will use the [SQL Server](https://www.nuget.org/packages/RepoDb.SqlServer) as the database and C# as the programming language.

#### Use-Cases

You should use this class if you wish to work with all the database tables (and multiple models) together within the same repository. This is a common design which we called a shared-repository.

#### How to Implement?

Let us say you have the following tables.

- [dbo].[Customer]
- [dbo].[Order]
- [dbo].[Product]

And the following classes.

- Customer
- Order
- Product

Then you can create your shared repository like below.

```csharp
public class NorthwindRepository : DbRepository<SqlConnection>,
{
    public NorthwindRepository(ISettings settings)
        : base(settings.ConnectionString)
    { }

    // Create
    public object CreateCustomer(Customer customer)
    {
        return Insert<Customer>(customer);
    }

    public object CreateOrder(Order order)
    {
        return Insert<Order>(order);
    }

    public object CreateProduct(Product product)
    {
        return Insert<Product>(product);
    }

    // GetAll
    public IEnumerable<Customer> GetCustomers()
    {
        return QueryAll<Customer>();
    }
    
    public IEnumerable<Product> GetProducts()
    {
        return QueryAll<Product>();
    }

    // GetOrders
    public IEnumerable<Order> GetCustomerOrders(int customerId)
    {
        return Query<Order>(o => o.CustomerId == customerId);
    }

    ...
}
```

> Beware of the recurring calls. Ensure to prepend the `base` keyword if your method name is with the same signature as with the base.

#### How to Use?

Simply create (or inject) a new instance of the class to use the repository.

```csharp
using (var repository = new NorthwindRepository(settings)) // The settings must be DI(ed) (or,the repository itself must be DI(ed))
{
    var customers = repository.GetCustomers();
    customers
        .AsList()
        .ForEach(c =>
        {
            var orders = repository.GetCustomerOrders(c.Id);
            ProcessOrders(orders);
        });
}
```

> A respository is disposable, so please do not forget to wrap it with `using` keyword.

#### Dependency Injection

To make your repository dependency injectable, we recommend you to create your own interface with the necessary methods, then implement it in the class and inject it with your dependency injector.

```csharp
public interface INorthwithRepository
{
    // Create
    object CreateCustomer(Customer customer);
    object CreateOrder(Order order);
    object CreateProduct(Product product);
    // GetAll
    IEnumerable<Customer> GetCustomers();
    IEnumerable<Product> GetProducts();
    // GetOrders
    public IEnumerable<Order> GetCustomerOrders(int customerId);
}
```

Then implement it in your class.

```csharp
public class NorthwindRepository : DbRepository<Customer, SqlConnection>, INorthwindRepository
{
    public NorthwindRepository(ISettings settings)
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
    services.AddTransient<INorthwindRepository, NorthwindRepository>();
}
```

> Do not use the repository directly as the injected object to make your design more SOLID (adhering the single-responsibility concepts).

#### Connection Persistency

This feature enables your repository to manage the persistency of your connection within the lifespan of the repository. Please have a look at the [Connection Persistency](/enumeration/connectionpersistency) enumeration to see more details.

To enable an instanced-level connection persistency, simply pass the [Connection Persistency](/enumeration/connectionpersistency#instance) value in the constructor.

```csharp
public class NorthwindRepository : DbRepository<Customer, SqlConnection>
{
    public NorthwindRepository(ISettings settings)
        : base(settings.ConnectionString, ConnectionPersistency.Instance)
    { }

    ...
}
```

#### Command Timeout

This feature is used as the execution timeout of every operation. By default it is null; defaultly using the ADO.NET execution timeout.

To enable your customized timeout, simply pass it on the constructor.

```csharp
public class NorthwindRepository : DbRepository<Customer, SqlConnection>
{
    public NorthwindRepository(ISettings settings)
        : base(settings.ConnectionString, 600)
    { }

    ...
}
```

#### Adding a Cache

This feature allows the repository to enable the 2nd-layer cache for the purpose of performance. By default, the caching is enabled with the use of [MemoryCache](/class/memorycache). You can override the caching by passing the instance of [ICache](/interface/icache)-based class in the constructor.

```csharp
public class MyCustomCache : ICache
{
    // Your cache implementation
}
```

Then, pass it on the constructor.

```csharp
public class NorthwindRepository : DbRepository<Customer, SqlConnection>
{
    public NorthwindRepository(ISettings settings)
        : base(settings.ConnectionString, new MyCustomCache)
    { }

    ...
}
```

#### Adding a Trace

This feature allows you as a developer to trace and audit the execution of any operation in the repository. To enable the trace, you have to pass the instance of [ITrace](/interface/itrace)-based class in the constructor.

```csharp
public class MyCustomTrace : ITrace
{
    // Your trace implementation
}
```

Then, pass it on the constructor.

```csharp
public class NorthwindRepository : DbRepository<Customer, SqlConnection>
{
    public NorthwindRepository(ISettings settings)
        : base(settings.ConnectionString, new MyCustomTrace)
    { }

    ...
}
```

#### SQL Builder

This feature allows you to override the default SQL statement generator of the library. To override, you have to create your custom [Statement Builder](/extensibility/statementbuilder) and pass the instance of [IStatementBuilder](/interface/istatementbuilder)-based class in the constructor.

```csharp
public class OptimizedSqlServerStatementBuilder : IStatementBuilder
{
    // Your trace implementation
}
```

Then, pass it on the constructor.

```csharp
public class NorthwindRepository : DbRepository<Customer, SqlConnection>
{
    public NorthwindRepository(ISettings settings)
        : base(settings.ConnectionString, new OptimizedSqlServerStatementBuilder)
    { }

    ...
}
```

> The constructor of this class accepts all the possible combinations of the argument mentioned above.