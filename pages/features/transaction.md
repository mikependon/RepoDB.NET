---
layout: default
sidebar: features
title: "Transaction"
description: "A process of making the executions more ACID in nature."
permalink: /feature/transaction
tags: [repodb, transaction]
parent: FEATURES
---

# Transaction

---

The way transaction works in the library is very identical with the way how it works with ADO.NET.

## Using a Transaction via Connection

Creates a transaction object and pass it when you call any of the operation.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    using (var transaction = connection.EnsureOpen().BeginTransaction())
    {
        // Call the method
        var customerId = connection.Insert<Customer, int>(customer, transaction: transaction);
        var orderId = connection.Insert<Order, int>(order, transaction: transaction);

        // Commit
        transaction.Commit();
    }
}
```

## Using a Transaction via Repository

Same as previous section, simply create a transaction object and pass it when you call any of the operation.

```csharp
using (var repository = new NorthwindRepository(connectionString))
{
    using (var transaction = repository.CreateConnection().EnsureOpen().BeginTransaction())
    {
        // Call the method
        var customerId = repository.Insert<Customer, int>(customer, transaction: transaction);
        var orderId = repository.Insert<Order, int>(order, transaction: transaction);

        // Commit
        transaction.Commit();
    }
}
```
