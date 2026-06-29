---
layout: default
sidebar: features
title: "Targeted Operations"
description: "This feature allows you to make a targeted invocation when executing the operations againts the database."
permalink: /feature/targeted
tags: [repodb, targeted]
parent: FEATURES
---

# Targeted Operations

---

This feature allows targeted invocation when executing operations against the database. It is particularly useful when performance and efficiency matter, as targeting specific columns eliminates unnecessary network traffic.

{: .note }
> This is especially relevant when your table or entity model has many columns and you only need to query, insert, merge, or update a subset of them.

The examples below assume the `[dbo].[Customer]` table and `Customer` entity model have many columns. Each example demonstrates how to target specific columns in the operation.

## Querying a Data

Use the [Query](/operation/query) operation and pass the target fields.

```csharp
using (var connection = new SqlConnection(connectionString))
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

Or via dynamics:

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var fields = Field.From("Id", "FirstName", "LastName");
    var customer = connection.Query("[dbo].[Customer]",
        10045,
        fields: fields).FirstOrDefault();
}
```

## Querying Multiple Data

Use the [QueryAll](/operation/queryall) operation and pass the target fields.

```csharp
using (var connection = new SqlConnection(connectionString))
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

Or via dynamics:

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var fields = Field.From("Id", "FirstName", "LastName");
    var customers = connection.QueryAll("[dbo].[Customer]",
        fields: fields);
}
```

## Inserting a Data

Use the [Insert](/operation/insert) operation and pass the target fields.

```csharp
using (var connection = new SqlConnection(connectionString))
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

Or via dynamics:

```csharp
using (var connection = new SqlConnection(connectionString))
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

Or via dynamics with fields:

```csharp
using (var connection = new SqlConnection(connectionString))
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

## Inserting Multiple Data

Use the [InsertAll](/operation/insertall) operation and pass the target fields.

```csharp
using (var connection = new SqlConnection(connectionString))
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

Or via dynamics:

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var entities = GenerateCustomers();
    var insertedRows = connection.InsertAll("[dbo].[Customer]",
        entities);
}
```

Or via dynamics with fields:

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var entities = GenerateCustomers();
    var fields = Field.From("FirstName", "LastName");
    var insertedRows = connection.InsertAll("[dbo].[Customer]",
        entities,
        fields: fields);
}
```

## Merging a Data

Use the [Merge](/operation/merge) operation and pass the target fields.

```csharp
using (var connection = new SqlConnection(connectionString))
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

Or via dynamics:

```csharp
using (var connection = new SqlConnection(connectionString))
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

Or via dynamics with fields:

```csharp
using (var connection = new SqlConnection(connectionString))
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

## Merging Multiple Data

Use the [MergeAll](/operation/mergeall) operation and pass the target fields.

```csharp
using (var connection = new SqlConnection(connectionString))
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

Or via dynamics:

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var entities = GetCustomersWithUpdatedAddress();
    var mergedRows = connection.MergeAll("[dbo].[Customer]",
        entities);
}
```

Or via dynamics with fields:

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var entities = GetCustomersWithUpdatedAddress();
    var fields = Field.From("Id", "Address");
    var mergedRows = connection.MergeAll("[dbo].[Customer]",
        entities,
        fields: fields);
}
```

## Updating a Data

Use the [Update](/operation/update) operation and pass the target fields.

```csharp
using (var connection = new SqlConnection(connectionString))
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

Or via dynamics:

```csharp
using (var connection = new SqlConnection(connectionString))
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

Or via dynamics with fields:

```csharp
using (var connection = new SqlConnection(connectionString))
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

## Updating Multiple Data

Use the [UpdateAll](/operation/updateall) operation and pass the target fields.

```csharp
using (var connection = new SqlConnection(connectionString))
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

Or via dynamics:

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var entities = GetCustomersWithUpdatedAddress();
    var updatedRows = connection.UpdateAll("[dbo].[Customer]",
        entities);
}
```

Or via dynamics with fields:

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var entities = GetCustomersWithUpdatedAddress();
    var fields = Field.From("Id", "Address");
    var updatedRows = connection.UpdateAll("[dbo].[Customer]",
        entities,
        fields: fields);
}
```
