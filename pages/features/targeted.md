---
layout: navpage
sidebar: features
title: "Dynamics"
description: "This feature allows you to make a targeted invocation when executing the operations againts the database."
permalink: /feature/targeted
tags: [repodb, class, targeted, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# Targeted Operations

This feature allows you to make a targeted invocation when executing the operations against the database. This feature is very useful if you do really care on the performance and efficiency of your execution against the database. The execution will help your application runs in a very efficient manner, as using the targeted operations completely eliminate the high-usage of the network traffic.

#### Use Case

Imagine if your `Customer` model has plenty of columns and you are only Querying, Inserting, Merging or Updating the specific columns.

#### Querying a Data

Use the [Query](/operation/query) operation and pass the target fields to query.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
    var fields = Field.Parse<Customer>(e => new
    {
        e.Id,
        e.FirstName,
        e.LastName
    });
    var customer = connection.Query<Customer>(10045,
        fields: fields).FirstOrDefault();
}
```

Or via dynamics.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
    var fields = Field.From("Id", "FirstName", "LastName");
    var customer = connection.Query("[dbo].[Customer]",
        10045,
        fields: fields).FirstOrDefault();
}
```

#### Querying Multiple Data

Use the [QueryAll](/operation/queryall) operation and pass the target fields to query.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
    var fields = Field.Parse<Customer>(e => new
    {
        e.Id,
        e.FirstName,
        e.LastName
    });
    var customers = connection.QueryAll<Customer>(fields: fields);
}
```

Or via dynamics.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
    var fields = Field.From("Id", "FirstName", "LastName");
    var customers = connection.QueryAll("[dbo].[Customer]",
        fields: fields);
}
```

#### Inserting a Data

Use the [Insert](/operation/insert) operation and pass the target fields to insert.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
    var entity = new Customer
    {
        FirstName = "John",
        LastName = "Doe"
    };
    var fields = Field.Parse<Customer>(e => new
    {
        e.FirstName,
        e.LastName
    });
    var id = connection.Insert<Customer, int>(entity,
        fields: fields);
}
```

Or via dynamics.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
    var entity = new
    {
        FirstName = "John",
        LastName = "Doe"
    };
    var id = connection.Insert<int>("[dbo].[Customer]",
        entity);
}
```

Or via dynamics with fields.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
    var entity = new Customer
    {
        FirstName = "John",
        LastName = "Doe"
    };
    var fields = Field.From("FirstName", "LastName");
    var id = connection.Insert<int>("[dbo].[Customer]",
        entity, fields: fields);
}
```

#### Inserting Multiple Data

Use the [InsertAll](/operation/insertall) operation and pass the target fields to insert.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
    var entities = GenerateCustomers();
    var fields = Field.Parse<Customer>(e => new
    {
        e.FirstName,
        e.LastName
    });
    var insertedRows = connection.InsertAll<Customer, int>(entities,
        fields: fields);
}
```

Or via dynamics.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
    var entities = GenerateCustomers();
    var insertedRows = connection.InsertAll("[dbo].[Customer]",
        entities);
}
```

Or via dynamics with fields.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
    var entities = GenerateCustomers();
    var fields = Field.From("FirstName", "LastName");
    var insertedRows = connection.InsertAll("[dbo].[Customer]",
        entities,
        fields: fields);
}
```

#### Merging a Data

Use the [Merge](/operation/merge) operation and pass the target fields to merge.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
    var entity = new Customer
    {
        Id = 10045,
        FirstName = "John",
        LastName = "Doe",
        LastUpdatedDateUtc = DateTime.UtcNow
    };
    var fields = Field.Parse<Customer>(e => new
    {
        e.Id,
        e.FirstName,
        e.LastName,
        e.LastUpdatedUtc
    });
    var id = connection.Merge<Customer, int>(entity,
        fields: fields);
}
```

Or via dynamics.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
    var entity = new Customer
    {
        Id = 10045,
        FirstName = "John",
        LastName = "Doe",
        LastUpdatedDateUtc = DateTime.UtcNow
    };
    var id = connection.Merge<int>("[dbo].[Customer]",
        entity);
}
```

Or via dynamics with fields.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
    var entity = new Customer
    {
        Id = 10045,
        FirstName = "John",
        LastName = "Doe",
        LastUpdatedDateUtc = DateTime.UtcNow
    };
    var fields = Field.From("Id", "FirstName", "LastName", "LastUpdatedDateUtc");
    var id = connection.Merge<int>("[dbo].[Customer]",
        entity,
        fields: fields);
}
```

#### Merging Multiple Data

Use the [MergeAll](/operation/mergeall) operation and pass the target fields to merge.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
    var entities = GetCustomersWithUpdatedAddress();
    var fields = Field.Parse<Customer>(e => new
    {
        e.Id,
        e.Address
    });
    var mergedRows = connection.MergeAll<Customer>(entities,
        fields: fields);
}
```

Or via dynamics.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
    var entities = GetCustomersWithUpdatedAddress();
    var mergedRows = connection.MergeAll("[dbo].[Customer]",
        entities);
}
```

Or via dynamics with fields.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
    var entities = GetCustomersWithUpdatedAddress();
    var fields = Field.From("Id", "Address");
    var mergedRows = connection.MergeAll("[dbo].[Customer]",
        entities,
        fields: fields);
}
```

#### Updating a Data

Use the [Update](/operation/update) operation and pass the target fields to update.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
    var entity = new Customer
    {
        Id = 10045,
        FirstName = "John",
        LastName = "Doe",
        LastUpdatedDateUtc = DateTime.UtcNow
    };
    var fields = Field.Parse<Customer>(e => new
    {
        e.Id,
        e.FirstName,
        e.LastName,
        e.LastUpdatedUtc
    });
    var updatedRows = connection.Update<Customer>(entity,
        fields: fields);
}
```

Or via dynamics.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
    var entity = new Customer
    {
        Id = 10045,
        FirstName = "John",
        LastName = "Doe",
        LastUpdatedDateUtc = DateTime.UtcNow
    };
    var updatedRows = connection.Update("[dbo].[Customer]",
        entity);
}
```

Or via dynamics with fields.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
    var entity = new Customer
    {
        Id = 10045,
        FirstName = "John",
        LastName = "Doe",
        LastUpdatedDateUtc = DateTime.UtcNow
    };
    var fields = Field.From("Id", "FirstName", "LastName", "LastUpdatedDateUtc");
    var updatedRows = connection.Update("[dbo].[Customer]",
        entity,
        fields: fields);
}
```

#### Updating Multiple Data

Use the [UpdateAll](/operation/updateall) operation and pass the target fields to update.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
    var entities = GetCustomersWithUpdatedAddress();
    var fields = Field.Parse<Customer>(e => new
    {
        e.Id,
        e.Address
    });
    var updatedRows = connection.UpdateAll<Customer>(entities,
        fields: fields);
}
```

Or via dynamics.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
    var entities = GetCustomersWithUpdatedAddress();
    var updatedRows = connection.UpdateAll("[dbo].[Customer]",
        entities);
}
```

Or via dynamics with fields.

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
    var entities = GetCustomersWithUpdatedAddress();
    var fields = Field.From("Id", "Address");
    var updatedRows = connection.UpdateAll("[dbo].[Customer]",
        entities,
        fields: fields);
}
```
