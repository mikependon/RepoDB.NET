---
layout: default
sidebar: features
title: "Batch Operations"
description: "It is the process of making the multiple single-operations be executed against the database in one-go."
permalink: /feature/batchoperations
tags: [repodb, batch, batch-operations]
parent: FEATURES
---

# Batch Operations

---

A batch operation is a process of executing multiple single-operations (or SQL Statement) against the database in one-go. The execution is ACID; an implicit transaction is provided if not present.

This operation comes with flexibility as it allows you to control the number of rows to be batched during the execution. Because of this, you can manage and further optimize the performance based on your own situation (i.e.: Network Latency, Infrastructure, No. of Columns, Type of Data, etc).

In RepoDB, the following operations (i.e.: [InsertAll](/operation/insertall), [UpdateAll](/operation/updateall) and [MergeAll](/operation/mergeall)) are the batch operations. They are all ACID in nature.

## Identity Setting Alignment

The library has enforced an additional logic to ensure the identity setting alignment is correct if the [InsertAll](/operation/insertall) and [MergeAll](/operation/mergeall) operations are being used.

During the batch operation, a dedicated `DbParameter` object is added that adds an additional column named `__RepoDb_OrderColumn` to the sub-table with a value of the entity model index, thus ensuring that the index value is really equating the index of the entity data from the `IEnumerable<T>` object. The resultsets of the sub-table are being ordered using this column in ascending order prior to the actual cascade of data.

When the newly generated identity value is being set back to the data model, the value of the `__RepoDb_OrderColumn` column is being used to look-up the proper index of the equating entity model from the `IEnumerable<T>` object, then, the compiled identity-setter function is used to assign back the identity value into the identity property.

## Normal executions

Let us say you have a model named `Customer` that corresponds to the `[dbo].[Customer]` table.

By calling the [Insert](/operation/insert) operation below...

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

...the following SQL script will be executed in the database.

```csharp
> INSERT INTO [Customer] (Name, Address) VALUES (@Name, @Address);
```

And if you insert multiple rows via iteration like below.

```csharp
var customers = GetCustomers(1000);
using (var connection = new SqlConnection(connectionString))
{
    customers.ForEach(
        c => connection.Insert<Customer>(c));
}
```

The same SQL statement will be executed multiple times against the database, even you wrap the calls within the transaction object.

## Batch executions

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

The library will then create the packed-statements that is executable in one-go. In the case above, the library will create the following SQL statements that is batched by 100.

```csharp
> INSERT INTO [Customer] (Name, Address) VALUES (@Name, @Address);
> INSERT INTO [Customer] (Name, Address) VALUES (@Name1, @Address1);
> ...
> INSERT INTO [Customer] (Name, Address) VALUES (@Name99, @Address98);
> INSERT INTO [Customer] (Name, Address) VALUES (@Name99, @Address99);
```

> The default value of the `batchSize` is 10. The value can be seen at [Constant.DefaultBatchOperationSize](/class/constant).

The packed-statements above are cached and is being executed 10 times with 100 rows each. All the parameters will be passed into its proper indexes, depending on the number of batches. The execution is more optimal than it was in the previous section as it is executing the multiple SQL statements in one-go. Without having the batch operations, the executions will be 1000 times.

> In SQL Server, the ADO.NET maximum number of parameters are 2100. The batch operation will fail if you reach that number. You can set the batch number by passing the value in the `batchSize` argument.

## Behind the scene of the Batch Operations

When you call any of the push batch operations (i.e.: [InsertAll](/operation/insertall), [UpdateAll](/operation/updateall) or [MergeAll](/operation/mergeall)), the following activities are happening behind the scene.

| Behavior  | Description |
|:-------------|:------------------|
| Understanding and Caching your Schema | It first touches your database to extract the schema definitions. This includes the retrieval of the primary, identity and the nullable columns. The retrieved information is then cached into the memory and is used for AOT compilation. |
| Caching the Class Properties | The properties of your class (or model) is being extracted and is being cached into the memory. This enables the library to reuse it in any future calls (that is using the same object). |
| Generating and Caching the SQL Statements | The library generates the packed-SQL Statements that varies on the number of batches you passed in the `batchSize` argument. These SQL Statements are being cached into the memory for future reusabilities. |
| Caching the Execution Context | The execution context is being cached. It enables the library to reuse the existing execution context that has already been executed against the database. By having this, the library does not need to extract the same operation every time there is an identical call, those leads to become more performant and efficient. _(The execution context contains the SQL Statements, Parameters, Preparations and even the Compiled ILs or Expressions.)_ |
| Adding an Implicit Transaction | A new transaction object is being assigned to the execution if the caller does not passed any explicit transaction. |
| Preparation | Before executing the command object, the `Prepare()` method is being called to pre-define the execution against the database. In the case of SQL Server, it creates an execution-plan in advance. |
| Batch Execution | The generated packed statements are being executed against the database only once. Behind the scene, the library is batching the execution due to the fact that ADO.NET is limited only to 2100 parameters. Through these batches, the caller is able to define the best batch number based on the situations and scenarios (i.e.: Number of Columns, Network Latency, Type of Data, etc). _(We highly recommend to always use the batch operations over the normal operations when working with multiple rows. Also, use the batch operations over the bulk operations if the number of rows you are working is less than 1000.)_ |
