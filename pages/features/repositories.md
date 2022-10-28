---
layout: default
sidebar: features
title: "Repositories"
description: "A repository is a software design pattern and practice in which it is being implemented as an additional layer between your application and your database. It is being represented as a class object within the application. Through repository, you are managing how the data is being manipulated from/to the database."
permalink: /feature/repositories
tags: [repodb, repository]
parent: FEATURES
---

# Repositories

---

A repository is a software design pattern and practice in which it is being implemented as an additional layer between your application and your database. It is being represented as a class object within the application. Through repository, you are managing how the data is being manipulated from/to the database.

Below is the high-level diagram of the repository.

<img src="../../assets/images/site/repository.svg" />

In the repository, we usually add the basic database operations (i.e.: [Insert](/operation/insert), [Delete](/operation/delete), [Update](/operation/update) and etc), but, here we also place the relevant advance operations usable within the application. Then, all the code in your application that fetches or pushes a data into the database is using this object instead of directly accessing the database. Thus allowing the developers to follow the correct chain-of calls and reusability when it comes to the data accessibility.

## Type of Repositories

In this library, there are 2 kinds of repositories available for implementation.

- [BaseRepository](/class/baserepository) - is used as a base repository for all entity-based repositories.
- [DbRepository](/class/dbrepository) - is used as a generic base repository for any types of entity.

## Creating an Entity-Based Repository

First, create an interface.

```csharp
public interface IPersonRepository
{
    int Delete(Person person);
    Person Get(int id);
    IEnumerable<Person> GetAll();
    int Save(Person person);
    int SaveAll(IEnumerable<Person> people);
    int Update(Person person);
}
```

Then, create a class that inherits from the [BaseRepository](/class/baserepository) class and implements the `IPersonRepository` interface.

```csharp
public class PersonRepository : BaseRepository<Person, SqlConnection>, IPersonRepository
{
    public PersonRepository(IOptions<AppSettings> settings)
        : base(settings.Value.ConnectionString)
    { }
}
```

Then, implement the needed interface methods.

#### Delete

```csharp
public int Delete(Person person)
{
    return base.Delete(person);
}
```

#### Get

```csharp
public Person Get(int id)
{
    return Query(id).FirstOrDefault();
}

public IEnumerable<Person> GetAll()
{
    return QueryAll();
}
```

#### Save

```csharp
public int Save(Person person)
{
    return Insert<int>(person);
}

public int SaveAll(IEnumerable<Person> people)
{
    return InsertAll(people);
}
```

#### Update

```csharp
public int Update(Person person)
{
    return Update(person);
}
```

Then, inject the object like below.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers();

    // Registration
    services.AddTransient<IPersonRepository, PersonRepository>();
}
```

> Please visit our [BaseRepository](/reference/baserepository) reference implementation page for the detailed implementation.

## Creating a Database Level Repository

First, create an interface.

```csharp
public interface INorthwindRepository
{
    // Customer
    int DeleteCustomer(Customer customer);
    Customer GetCustomer(int id);
    IEnumerable<Customer> GetAllCustomers();
    int SaveCustomer(Customer customer);
    int SaveAllPCustomers(IEnumerable<Customer> customers);
    int UpdateCustomer(Customer customer);

    // Order
    int DeleteOrder(Order order);
    Order GetOrder(int id);
    IEnumerable<Order> GetAllOrders();
    int SaveOrder(Order order);
    int SaveAllOrders(IEnumerable<Order> orders);
    int UpdateOrder(Order order);
}
```

Then, create a class that inherits from the [DbRepository](/class/dbrepository) class and implements the `INorthwindRepository` interface.

```csharp
public class NorthwindRepository : DbRepository<SqlConnection>, INorthwindRepository
{
    public PersonRepository(IOptions<AppSettings> settings)
        : base(settings.Value.ConnectionString)
    { }
}
```

Then, implement the needed interface methods.

#### Delete

```csharp
// Customer

public int DeleteCustomer(Customer customer)
{
    return Delete<Customer>(customer);
}

// Order

public int DeleteOrder(Order order)
{
    return Delete<Order>(order);
}
```

#### Get

```csharp
// Customer

public Customer GetCustomer(int id)
{
    return Query<Customer>(id).FirstOrDefault();
}

public IEnumerable<Customer> GetAllCustomers()
{
    return QueryAll<Customer>();
}

// Order

public Order GetOrder(int id)
{
    return Query<Order>(id).FirstOrDefault();
}

public IEnumerable<Order> GetAllOrders()
{
    return QueryAll<Order>();
}

```

#### Save

```csharp
// Customer

public int SaveCustomer(Customer customer)
{
    return Insert<Customer, int>(customer);
}

public int SaveAllCustomers(IEnumerable<Customer> people)
{
    return InsertAll<int><Customer>(people);
}

// Order

public int SaveOrder(Order order)
{
    return Insert<Order, int>(order);
}

public int SaveAllOrders(IEnumerable<Order> people)
{
    return InsertAll<Order>(people);
}
```

#### Update

```csharp
// Customer

public int UpdateCustomer(Customer customer)
{
    return Update<Customer>(customer);
}

// Order

public int UpdateOrder(Order order)
{
    return Update<Order>(order);
}
```

Then, inject the object like below.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers();

    // Registration
    services.AddTransient<INorthwindRepository, NorthwindRepository>();
}
```

> Please visit our [DbRepository](/reference/dbrepository) reference implementation page for the detailed implementation.

## Creating a Custom Generic Repository

First, create a generic interface.

```csharp
public interface IGenericRepository<TEntity, TDbConnection>
{
    TDbConnection CreateConnection();
    int Delete(TEntity entity);
    TEntity Get(int id);
    IEnumerable<TEntity> GetAll();
    int Save(TEntity entity);
    int SaveAll(IEnumerable<TEntity> entities);
    int Update(TEntity entity);
}
```

Then, create a base interface that inherits the generic interface.

```csharp
public interface IPersonRepository : IGenericRepository<Person, SqlConnection>
{
    ...
}
```

Then, create a class that inherits from the `GenericRepository` and implements the `IPersonRepository` interface.

```csharp
public class PersonRepository : GenericRepository<Person, SqlConnection>, IPersonRepository
{
    private IOptions<AppSettings> _settings = null;

    public PersonRepository(IOptions<AppSettings> settings)
        : base(settings.Value.ConnectionString)
    {
        _settings = settings;
    }
}
```

Then, implement the needed interface methods.

#### CreateConnection

```csharp
public SqlConnection CreateConnection()
{
    return new SqlConnection(_settings.Value.ConnectionString);
}
```

#### Delete

```csharp
public int Delete(Person entity)
{
    using (var connection = CreateConnection())
    {
        return connection.Delete<Person>(entity);
    }
}
```

#### Get

```csharp
public Person Get(int id)
{
    using (var connection = CreateConnection())
    {
        return connection.Query(id).FirstOrDefault();
    }
}

public IEnumerable<Person> GetAll()
{
    using (var connection = CreateConnection())
    {
        return connection.QueryAll(id);
    }
}
```

#### Save

```csharp
public int Save(Person entity)
{
    using (var connection = CreateConnection())
    {
        return Insert<Person, int>(entity);
    }
}

public int SaveAll(IEnumerable<Person> entities)
{
    using (var connection = CreateConnection())
    {
        return InsertAll<Person>(entities);
    }
}
```

#### Update

```csharp
public int Update(Person entity)
{
    using (var connection = CreateConnection())
    {
        return Update<Person>(entity);
    }
}
```

Then, inject the object like below.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers();

    // Registration
    services.AddTransient<IPersonRepository, PersonRepository>();
}
```

> Please visit our [Generic Repository](/reference/genericrepository) reference implementation page for the detailed implementation.

