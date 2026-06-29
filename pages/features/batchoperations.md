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

A batch operation executes multiple single-operations (or SQL statements) against the database in one call. The execution is ACID; an implicit transaction is provided if none exists.

This operation is flexible, allowing you to control the number of rows batched per execution. This lets you tune performance based on your environment (e.g., network latency, infrastructure, number of columns, type of data).

In RepoDB, the [InsertAll](/operation/insertall), [UpdateAll](/operation/updateall), and [MergeAll](/operation/mergeall) operations are the batch operations. They are all ACID in nature.

## Identity Setting Alignment

The library enforces additional logic to ensure correct identity setting alignment when using [InsertAll](/operation/insertall) and [MergeAll](/operation/mergeall).

During the batch operation, a dedicated [DbParameter](https://learn.microsoft.com/en-us/dotnet/api/system.data.common.dbparameter?view=net-6.0) object is added with a column named `__RepoDb_OrderColumn` in the sub-table. Its value is the entity model index, ensuring that the index correctly maps to the corresponding entity in the [IEnumerable](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.ienumerable-1?view=net-7.0) object. The sub-table result set is ordered by this column in ascending order prior to the data cascade.

When the newly generated identity value is set back to the data model, the `__RepoDb_OrderColumn` value is used to look up the correct index in the [IEnumerable](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.ienumerable-1?view=net-7.0) object, and the compiled identity-setter function assigns the identity value back to the identity property.

## Normal executions

Given a `Customer` model mapped to the `[dbo].[Customer]` table, calling the [Insert](/operation/insert) operation:

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

...executes the following SQL statement:

```csharp
> INSERT INTO [Customer] (Name, Address) VALUES (@Name, @Address);
```

Inserting multiple rows via iteration:

```csharp
var customers = GetCustomers(1000);
using (var connection = new SqlConnection(connectionString))
{
    customers.ForEach(
        c => connection.Insert<Customer>(c));
}
```

The same SQL statement is executed once per row — even when wrapped in a transaction.

## Batch executions

With batch execution, the SQL statements are packed and executed in a single call.

```csharp
var customers = GetCustomers(1000);
using (var connection = new SqlConnection(connectionString))
{
    connection.InsertAll<Customer>(customers,
        batchSize: 100);
}
```

The library generates packed statements batched by the specified size. For a batch size of 100:

```csharp
> INSERT INTO [Customer] (Name, Address) VALUES (@Name, @Address);
> INSERT INTO [Customer] (Name, Address) VALUES (@Name1, @Address1);
> ...
> INSERT INTO [Customer] (Name, Address) VALUES (@Name99, @Address98);
> INSERT INTO [Customer] (Name, Address) VALUES (@Name99, @Address99);
```

{: .note }
> The default value of the `batchSize` is 10. The value can be seen at [Constant.DefaultBatchOperationSize](/class/constant).

The packed statements are cached and executed in batches — 10 times with 100 rows each in the example above. All parameters are mapped to their correct indexes based on the batch number. This approach is significantly more efficient than individual inserts, reducing 1000 separate executions to just 10.

{: .important }
> In SQL Server, ADO.NET supports a maximum of 2100 parameters. The batch operation will fail if this limit is exceeded. Adjust the batch size using the `batchSize` argument accordingly.

## Behind the scene of the Batch Operations

When calling any batch push operation ([InsertAll](/operation/insertall), [UpdateAll](/operation/updateall), or [MergeAll](/operation/mergeall)), the following steps occur internally:

| Behavior  | Description |
|:-------------|:------------------|
| Understanding and Caching your Schema | The database is queried to extract schema definitions, including primary, identity, and nullable columns. The retrieved information is cached in memory for AOT compilation. |
| Caching the Class Properties | Class (model) properties are extracted and cached, enabling reuse across subsequent calls using the same object. |
| Generating and Caching the SQL Statements | Packed SQL statements are generated based on the `batchSize` argument and cached for future reuse. |
| Caching the Execution Context | The execution context (SQL statements, parameters, preparations, and compiled ILs or expressions) is cached to avoid re-processing identical calls, improving performance and efficiency. |
| Adding an Implicit Transaction | A transaction object is assigned to the execution if the caller does not provide one explicitly. |
| Preparation | Before execution, the [Prepare](https://learn.microsoft.com/ro-RO/dotnet/api/system.data.common.dbcommand.prepare?view=net-6.0) method is called to pre-define the execution plan against the database. In SQL Server, this creates an execution plan in advance. |
| Batch Execution | The generated packed statements are executed against the database. The library batches the execution internally due to ADO.NET's 2100-parameter limit. Callers can tune the batch size based on their specific scenarios (e.g., number of columns, network latency, type of data). _(We recommend always using batch operations over normal operations when working with multiple rows, and batch operations over bulk operations when the row count is below 1000.)_ |
