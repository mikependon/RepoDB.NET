---
layout: page
title: "Repositories (RepoDb)"
permalink: /feature/repositories
tags: [repodb, class, repository, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

## Repository

A repository is a software design pattern and practice in which it is being implemented as an additional layer between your application and your database. It is being represented as a class object within the application. Through repository, you are managing how the data is being manipulated from/to the database.

In this class (the `Repository`), we usually add the basic database operations/methods (ie: [Insert](/operation/insert), [Delete](/operation/delete), [Update](/operation/update) and etc). But in most cases, we place the advance and reporting operations/methods here (ie: `GetTotalOrdersByMonth` or `RecalculateCustomerOrdersByDateRange`).

Then, the codes in your application is using the repository object instead of directly accessing the database. Those allow the developers to follow the correct chain-of calls and reusability when it comes to data-accessibility.

> In this tutorial, we will use the SQL Server as the database and C# as the programming language.

#### Type of Repositories

In this library, there are 2 kinds of repositories available for implementation.

- [BaseRepository](/class/baserepository) - is used as a base repository for all entity-based repositories.
- [DbRepository](/class/dbrepository) - is used as a generic base repository for any types of entity.

#### Installation

To install, simply type the codes below in your Package Manager Console.

```csharp
> Install-Package RepoDb.SqlServer
```

Then call the bootstrapper once.

```csharp
RepoDb.SqlServerBootstrap.Initialize();
```

Or visit our [installation](/tutorial/installation) page for more information.

#### Creating an Entity-Based Repository

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

Then implement the class that inherits the [BaseRepository](/class/baserepository) and implements the custom interface.

```csharp
public PersonRepository : BaseRepository<Person, SqlConnection>, IPersonRepository
{
    public PersonRepository(IOptions<AppSettings> settings)
        : base(settings.ConnectionString)
    { }

    public int Delete(Person person)
    {
        return base.Delete(person);
    }

    public Person Get(int id)
    {
        return Query(id).FirstOrDefault();
    }

    public IEnumerable<Person> GetAll()
    {
        return QueryAll();
    }

    public int Save(Person person)
    {
        return Insert<int>(person);
    }

    public int SaveAll(IEnumerable<Person> people)
    {
        return InsertAll(people);
    }

    public int Update(Person person)
    {
        return Update(person);
    }
}
```

Then you can inject like below.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers();

    // Registration
    services.AddTransient<IPersonRepository, PersonRepository>();
}
```

Please visit our [BaseRepository (Reference)](/reference/baserepository) page for more information.

#### Creating a Database Level Repository

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

Then implement the class that inherits the [DbRepository](/class/dbrepository) and implements the custom interface.

```csharp
public NorthwindRepository : DbRepository<SqlConnection>, IPersonRepository
{
    public PersonRepository(IOptions<AppSettings> settings)
        : base(settings.ConnectionString)
    { }

    // Customer

    public int DeleteCustomer(Customer customer)
    {
        return Delete<Customer>(customer);
    }

    public Customer GetCustomer(int id)
    {
        return Query<Customer>(id).FirstOrDefault();
    }

    public IEnumerable<Customer> GetAllCustomers()
    {
        return QueryAll<Customer>();
    }

    public int SaveCustomer(Customer customer)
    {
        return Insert<Customer, int>(customer);
    }

    public int SaveAllCustomers(IEnumerable<Customer> people)
    {
        return InsertAll<int><Customer>(people);
    }

    public int UpdateCustomer(Customer customer)
    {
        return Update<Customer>(customer);
    }

    // Order

    public int DeleteOrder(Order order)
    {
        return Delete<Order>(order);
    }

    public Order GetOrder(int id)
    {
        return Query<Order>(id).FirstOrDefault();
    }

    public IEnumerable<Order> GetAllOrders()
    {
        return QueryAll<Order>();
    }

    public int SaveOrder(Order order)
    {
        return Insert<Order, int>(order);
    }

    public int SaveAllOrders(IEnumerable<Order> people)
    {
        return InsertAll<Order>(people);
    }

    public int UpdateOrder(Order order)
    {
        return Update<Order>(order);
    }
}
```

Then you can inject like below.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers();

    // Registration
    services.AddTransient<INorthwindRepository, NorthwindRepository>();
}
```

Please visit our [DbRepository (Reference)](/reference/dbrepository) page for more information.

#### Creating a Custom Generic Repository

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

Then create a class that implements both the interfaces.

```csharp
public PersonRepository : GenericRepository<Person, SqlConnection>, IPersonRepository
{
    private IOptions<AppSettings> m_settings = null;

    public PersonRepository(IOptions<AppSettings> settings)
        : base(settings.ConnectionString)
    {
        m_settings = settings;
    }

    public SqlConnection CreateConnection()
    {
        return new SqlConnection(m_settings.ConnectionString);
    }

    public int Delete(Person entity)
    {
        using (var connection = CreateConnection())
        {
            return connection.Delete<Person>(entity);
        }
    }

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

    public int Update(Person entity)
    {
        using (var connection = CreateConnection())
        {
            return Update<Person>(entity);
        }
    }
}
```

Then you can inject like below.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers();

    // Registration
    services.AddTransient<IPersonRepository, PersonRepository>();
}
```

Please visit our [Generic-Repository (Reference)](/reference/genericrepository) page for more information.

