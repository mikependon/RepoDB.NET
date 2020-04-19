---
layout: navpage
sidebar: references
title: "Unit of Work Reference (RepoDb)"
permalink: /reference/unitofwork
tags: [repodb, class, unitofwork, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# Unit of Work

This page contains the recommended way of implementing Unit of Work.

#### Connection Based

First, create a connection object. Then, create transaction object and pass it on every calls to the connection [operations](/docs#fluent-methods).

```csharp
/* Let us say a variable 'customer', 'order', 'orderDetail' is pre-given */
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
    // Create a transaction
    using (var transaction = connection.BeginTransaction())
    {
        // Call the methods
        order.CustomerId = connection.Insert<Customer, int>(customer, transaction: transaction);
        orderDetail.OrderId = connection.Insert<Order>(order, transaction: transaction);
        connection.Insert<OrderDetail>(orderDetail, transaction: transaction);

        // Commit
        transaction.Commit();
    }
}
```

#### Repository Based

Please refer to our [GenericRepository](/reference/genericrepository) page to create a generic repository.

There are 2 ways of doing Unit of Work in the repository.

###### Expose as Argument

First, expose the transaction argument from every repository method.

```csharp
public class NorthwindRepository : RepositoryBase<SqlConnection>, INorthwindRepository
{
    public NorthwindRepository(IOptions<AppSetting> settings)
        : base(settings)
    { }

    public int SaveCustomer(Customer customer, IDbTransaction transaction)
    {
        return (int)Save<Customer>(customer, transaction: transaction);
    }
    
    public int SaveOrder(Order order, IDbTransaction transaction)
    {
        return (int)Save<Order>(order, transaction: transaction);
    }
    
    public int SaveOrderDetail(OrderDetail orderDetail, IDbTransaction transaction)
    {
        return (int)Save<OrderDetail>(orderDetail, transaction: transaction);
    }
}
```

Then, pass the transaction object in the method calls (out-of-scope of the repositories).

```csharp
/* Let us say a variable 'customer', 'order', 'orderDetail' is pre-given */
using (var repository = new NorthwindRepository())
{
    using (var transaction = repository.CreateConnection().BeginTrasaction())
    {
        // Call the methods
        order.CustomerId = repository.SaveCustomer(customer, transaction: transaction);
        orderDetail.OrderId = repository.SaveOrder(order, transaction: transaction);
        repository.SaveOrderDetail(orderDetail, transaction: transaction);

        // Commit
        transaction.Commit();
    }
}
```

> You have to handle the correct context-switching on every method of the generic repository.

###### Manage Inside Repository

Second, expose the methods `BeginTransaction()`, `Commit()` and `Rollback()` within the generic repository.

```csharp
public class NorthwindRepository : RepositoryBase<SqlConnection>, INorthwindRepository
{
    public NorthwindRepository(IOptions<AppSetting> settings)
        : base(settings)
    { }

    // Unit of Work

    public IDbTransaction BeginTransaction()
    {
        ...
    }

    public void Commit()
    {
        ...
    }

    public void Rollback()
    {
        ...
    }

    // Methods

    public int SaveCustomer(Customer customer, IDbTransaction transaction)
    {
        return (int)Save<Customer>(customer, transaction: transaction);
    }
    
    public int SaveOrder(Order order, IDbTransaction transaction)
    {
        return (int)Save<Order>(order, transaction: transaction);
    }
    
    public int SaveOrderDetail(OrderDetail orderDetail, IDbTransaction transaction)
    {
        return (int)Save<OrderDetail>(orderDetail, transaction: transaction);
    }
}
```

Then call the methods as follows.

```csharp
/* Let us say a variable 'customer', 'order', 'orderDetail' is pre-given */
using (var repository = new NorthwindRepository())
{
    try
    {
        // Begin
        repository.BeginTransaction();

        // Call the methods
        order.CustomerId = repository.SaveCustomer(customer, transaction: transaction);
        orderDetail.OrderId = repository.SaveOrder(order, transaction: transaction);
        repository.SaveOrderDetail(orderDetail, transaction: transaction);

        // Commit
        repository.Commit();
    }
    catch
    {
        // Rollback
        repository.Rollback();

        // Throw
        throw;
    }
}
```

> Ensure that you handle the pointer of the used `Connection` and `Transaction` objects inside the generic repository. In addition, ensure that you dispose both objects everytime the `Commit()` or `Rollback()` method has been called.

