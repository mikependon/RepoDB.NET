---
layout: navpage
sidebar: features
title: "Transaction"
description: "A process of making the executions more ACID in nature."
permalink: /feature/transaction
tags: [repodb, class, transaction, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# Transaction

The way transaction works in the library is very identical with the way how it works with ADO.NET.

#### Using a Transaction via Connection

Creates a transaction object and pass it when you call any of the operation.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
    using (var transaction = connection.BeginTransaction())
    {
        // Call the method
        var customerId = connection.Insert<Customer, int>(customer, transaction: transaction);
        var orderId = connection.Insert<Order, int>(order, transaction: transaction);

        // Commit
        transaction.Commit();
    }
}
```

#### Using a Transaction via Repository

Same as previous section, simply create a transaction object and pass it when you call any of the operation.

```csharp
using (var repository = new NorthwindRepository(connectionString))
{
    using (var transaction = repository.CreateConnection().BeginTransaction())
    {
        // Call the method
        var customerId = repository.SaveCustomer<int>(customer, transaction: transaction);
        var orderId = repository.SaveOrder<int>(order, transaction: transaction);

        // Commit
        transaction.Commit();
    }
}
```

Please visit the [repository](/feature/repositories) feature page for the full implementation reference.