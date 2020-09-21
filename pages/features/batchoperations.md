---
layout: navpage
sidebar: features
title: "Batch Operations"
description: "It is the process of making the multiple single-operations be executed against the database in one-go."
permalink: /feature/batchoperations
tags: [repodb, class, batch, batch-operations, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# Batch Operations

A batch operation is a process of executing multiple single-operations against the database in one-go. The execution is ACID; an implicit transaction is provided if not present.

This operation comes with flexibility as it allows you to control the number of rows to be batched during the execution, and because of this, you can manage the performance based on your own scenarios (i.e.: Network Latency, Number of Columns, etc).

In RepoDB, the following operations (i.e.: [InsertAll](/operation/insertall), [UpdateAll](/operation/updateall) and [MergeAll](/operation/mergeall)) are the batch operations. They are all ACID in nature.

#### Normal executions

Let us say you have a model named `Customer` that corresponds to the `[dbo].[Customer]` table.

By calling the [Insert](/operation/insert) operation below.

```csharp
var customer = new Customer
{
    Name = "John Doe",
    Address = "New York"
};
using (var connection = new SqlConnection(connectionString))
{
    connection.Insert<Customer>(customer);
}
```

The following SQL script will be executed in the database.

```csharp
> INSERT INTO [Customer] (Name, Address) VALUES (@Name, @Address);
```

What if you would like to insert multiple records? Most developers do it this way.

```csharp
var customers = GetCustomers(1000);
using (var connection = new SqlConnection(connectionString))
{
    customers.ForEach(
        c => connection.Insert<Customer>(c));
}
```

We execute it in an atomic way and just simply wrap the calls within the `Transaction` object to make it ACID.

#### Batch executions

With the batch executions, the way the SQL statement is being executed is as follows.

Let us say you have implemented the code snippets below.

```csharp
var customers = GetCustomers(1000);
using (var connection = new SqlConnection(connectionString))
{
    connection.InsertAll<Customer>(customers,
        batchSize: 100);
}
```

> The default value of the batchSize is `10`. The value can be seen at [Constant.DefaultBatchOperationSize](/class/constant).

The library will then create the packed-statements that is executable in one-go. In the case above, the library will create the following SQL statements that is batched by 100.

```csharp
> INSERT INTO [Customer] (Name, Address) VALUES (@Name, @Address);
> INSERT INTO [Customer] (Name, Address) VALUES (@Name1, @Address1);
> ...
> INSERT INTO [Customer] (Name, Address) VALUES (@Name99, @Address98);
> INSERT INTO [Customer] (Name, Address) VALUES (@Name99, @Address99);
```

The packed-statements above are cached and is being executed 10 times with 100 rows each. All the parameters will be passed into its proper indexes, depending on the number of batches. The execution is more optimal than it was in the previous section as it is executing the multiple SQL statements in one-go. Without having the batch operations, the executions will be 1000 times.

> In SQL Server, the ADO.NET maximum number of parameters are 2100. The batch operation will fail if you reach that number. You can set the batch number by passing the value in the `batchSize` argument.

#### Behind the scene of the Batch Operations

When you call any of the push batch operations (i.e.: [InsertAll](/operation/insertall), [UpdateAll](/operation/updateall) or [MergeAll](/operation/mergeall)), the following activities are happening behind the scene.

###### Understanding your Schema

It first touches your database to extract the schema definitions. This includes the retrieval of the `PrimaryKey`, `Identity` and `Nullable-Columns` information. The information will be cached in the memory and will be used for the compilation.

###### Caching the Class Properties

The properties of your class (or model) is being extracted and is being cached in the memory. This enables the library to reuse it in any future calls (that is using the same object).

###### Caching the SQL Statements

The SQL statements are being generated and is cached automatically by the library. The generated SQL statement is a multiple packed-statements that varies on the number of batches you passed in the batchSize argument.

###### Caching the Execution Context

The execution context is being cached. It enables the library to reuse the existing execution context that has already been executed against the database. By having this, the library does not need to extract same operation every time there is an identical calls, those leads to become more high-performant and efficient.

> The execution context contains the SQL Statements, Parameters, Preparations and even the Compiled ILs or Expressions. 

###### Adding an Implicit Transaction

A new `Transaction` object is being assigned to the execution if the caller does not passed any explicit transaction.

###### Preparation

Before executing the `DbCommand` object, the `Prepare()` method is being called to pre-define the execution against the database. In the case of SQL Server, it creates an Execution-Plan in advance.

###### Batch Execution

The generated packed statements are being executed against the database only once. Behind the scene, the library is batching the execution due to the fact that ADO.NET is limited only to 2100 parameters. Through these batches, the caller is able to define the best batch number based on the situations and scenarios (i.e.: Number of Columns, Network Latency, etc).

> We highly recommend to always use the batch operations over normal operation when working with multiple rows. Also, use the batch operations over bulk operations if the number of rows you are working is less than 1000.
