---
layout: default
sidebar: classes
title: "DbRepository"
description: "A base class that is being used to implement a repository object. This is very useful if you would like to develop a shared or database level repository."
permalink: /class/dbrepository
tags: [repodb, dbrepository]
parent: CLASSES
---


# DbRepository

---

This is the base class for all repository classes. It accepts a `DbConnection` type as the generic type parameter.

## Use-Cases

Implement this class to create a shared repository that processes multiple tables and entity models — commonly referred to as a shared-repository pattern.

## Implementation

Given the following tables:

- `[dbo].[Customer]`
- `[dbo].[Order]`
- `[dbo].[Product]`

And the following classes:

- `Customer`
- `Order`
- `Product`

First, define a repository interface.

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

Then, create a class that inherits [DbRepository](/class/dbrepository) and implements the interface.

```csharp
public class NorthwindRepository : DbRepository<SqlConnection>, INorthwithRepository
{
    public NorthwindRepository(string connectionString)
        : base(connectionString)
    { }
}
```

Implement the interface methods.

#### Create

```csharp
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
```

#### Get

```csharp
public IEnumerable<Customer> GetCustomers()
{
    return QueryAll<Customer>();
}

public IEnumerable<Product> GetProducts()
{
    return QueryAll<Product>();
}

public IEnumerable<Order> GetCustomerOrders(int customerId)
{
    return Query<Order>(o => o.CustomerId == customerId);
}

/* More methods */
```

{: .warning }
> Prepend the `base` keyword when your method name matches a base class method to avoid recursive calls. See the [DbRepository](/reference/dbrepository) reference implementation for details.

## Usability

Create a new instance of the repository to use it.

```csharp
using (var repository = new NorthwindRepository(settings.Value.ConnectionString)) // The settings must be DI(ed) (or,the repository itself must be DI(ed))
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

{: .note }
> The repository is disposable — wrap it in a `using` statement.

## Dependency Injection

Register the interface and the implementation in the service collection.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers();

    // Registration
    services.AddTransient<INorthwindRepository, NorthwindRepository>();
}
```

{: .warning }
> Inject the interface, not the concrete class, to keep your design SOLID.

## The CreateConnection Method

This method creates a new connection instance. If [Connection Persistency](/enumeration/connectionpersistency) is set to `Instance`, it returns the existing active connection.

Pass `true` to the `force` argument to always return a new connection.

```csharp
using (var connection = CreateConnection(true))
{
    // Use the 'connection' here
}
```

## Connection Persistency

This property controls connection lifetime within the repository. See [Connection Persistency](/enumeration/connectionpersistency) for details.

To enable instance-level persistency, pass [ConnectionPersistency.Instance](/enumeration/connectionpersistency#instance) in the constructor.

```csharp
public class NorthwindRepository : DbRepository<Customer, SqlConnection>
{
    public NorthwindRepository(string connectionString)
        : base(connectionString, ConnectionPersistency.Instance)
    { }

    ...
}
```

## Command Timeout

This property sets the execution timeout for all operations. By default it is `null`, inheriting the ADO.NET default.

Pass a custom timeout value in the constructor.

```csharp
public class NorthwindRepository : DbRepository<Customer, SqlConnection>
{
    public NorthwindRepository(string connectionString)
        : base(connectionString, 600)
    { }

    ...
}
```

## Adding a Cache

This property enables second-layer caching for performance. By default, [MemoryCache](/class/memorycache) is used. Override by passing a custom [ICache](/interface/icache) instance in the constructor.

```csharp
public class MyCustomCache : ICache
{
    // Your cache implementation
}
```

Then pass it in the constructor.

```csharp
public class NorthwindRepository : DbRepository<Customer, SqlConnection>
{
    public NorthwindRepository(string connectionString)
        : base(connectionString, new MyCustomCache)
    { }

    ...
}
```

{: .note }
> See the [JSON Cache](/reference/jsoncache) reference implementation for file-based caching using JSON.

## Adding a Trace

This property enables tracing and auditing of operation execution. Pass a custom [ITrace](/interface/itrace) instance in the constructor.

```csharp
public class MyCustomTrace : ITrace
{
    // Your trace implementation
}
```

Then pass it in the constructor.

```csharp
public class NorthwindRepository : DbRepository<Customer, SqlConnection>
{
    public NorthwindRepository(string connectionString)
        : base(connectionString, new MyCustomTrace)
    { }

    ...
}
```

{: .note }
> See the [Trace](/reference/trace) reference implementation for details.

## SQL Builder

This property overrides the default SQL statement generator. Create a custom [Statement Builder](/extensibility/statementbuilder) and pass an [IStatementBuilder](/interface/istatementbuilder) instance in the constructor.

```csharp
public class OptimizedSqlServerStatementBuilder : IStatementBuilder
{
    // Your trace implementation
}
```

Then pass it in the constructor.

```csharp
public class NorthwindRepository : DbRepository<Customer, SqlConnection>
{
    public NorthwindRepository(string connectionString)
        : base(connectionString, new OptimizedSqlServerStatementBuilder)
    { }

    ...
}
```

{: .note }
> The constructor accepts any combination of the arguments described above.
