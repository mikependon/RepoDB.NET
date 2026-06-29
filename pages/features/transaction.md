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

Transactions in this library work identically to ADO.NET transactions.

## Using a Transaction via Connection

Create a transaction object and pass it to any operation.

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

Create a transaction object and pass it to any operation.

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
