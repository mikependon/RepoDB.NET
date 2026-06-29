---
layout: default
sidebar: classes
title: "QueryField"
description: "A class that is being used to define a query expression for the SQL statement. You can define the name, operation and the value of the target expression."
permalink: /class/queryfield
tags: [repodb, queryfield]
parent: CLASSES
---

# QueryField

---

Represents a single field in a query expression, typically corresponding to a condition in a SQL `WHERE` clause.

It holds [Field](/class/field), [Operation](/enumeration/operation), and [Parameter](/class/parameter) objects as its expression components.

Using this class improves performance because the library's core implementation relies on tree-structured query objects.

## Creating an Instance

```csharp
var field = new QueryField("Id", 10045);
```

With an operation.

```csharp
var field = new QueryField("Id", Operation.Between, new [] { 100, 1000 });
```

Or with a [DbType](https://learn.microsoft.com/en-us/dotnet/api/system.data.dbtype?view=net-6.0).

```csharp
var field = new QueryField("Id", Operation.Between, new [] { 100, 1000 }, DbType.Int);
```

## Use-Cases

Useful for building dynamic queries, such as those driven by APIs (OData, AutoQuery, etc.).

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var where = new []
    {
        new QueryField("LastName", Operation.Like, "Doe%"),
        new QueryField("State", Operation.Equal, "Michigan"),
        new QueryField("Age", Operation.Between, new [] (20, 40))
    };
    var people = connection.Query<Person>(where);
    // Do the stuffs for 'people' here
}
```

Or for targeted column updates.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var where = new []
    {
        new QueryField("State", Operation.Equal, "Michigan"),
        new QueryField("Age", Operation.Between, new [] (20, 40))
    };
    var person = new
    {
        IsActive = true,
        LastUpdatedUtc = DateTime.UtcNow
    };
    var updatedRows = connection.Update("[dbo].[Person]", person, where);
}
```

Or delete operations.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var where = new []
    {
        new QueryField("State", Operation.Equal, "Michigan"),
        new QueryField("Age", Operation.Between, new [] (20, 40))
    };
    var deletedRows = connection.Delete<Person>(where);
}
```

## Converting to an Enumerable

Call `AsEnumerable()` to convert the instance to an `IEnumerable<QueryField>`.

```csharp
var fields = new QueryField("CreatedDateUtc", Operation.GreaterThanOrEqual, DateTime.UtcNow.Date.AddDays(-1)).AsEnumerable();
```

## Retrieving the Operation Text

Call `GetOperationText()` to retrieve the SQL operator string for the [Operation](/enumeration/operation).

```csharp
var field = new QueryField("CreatedDateUtc", Operation.GreaterThanOrEqual, DateTime.UtcNow.Date.AddDays(-1));
var operation = field.GetOperationText();
```

The value of `operation` is `>=`.

## DbParameter Property

This property provides a reference to the associated [DbParameter](https://learn.microsoft.com/en-us/dotnet/api/system.data.common.dbparameter?view=net-6.0) after execution. Use it to retrieve output parameter values.

## GetValue Method

Returns the value of the current [Parameter](/class/parameter). If the instance was used as an output parameter (e.g., via [DirectionalQueryField](/class/directionalqueryfield)), the output value from the [DbParameter](https://learn.microsoft.com/en-us/dotnet/api/system.data.common.dbparameter?view=net-6.0) is returned instead.

## Reusability

Call `Reset()` to reuse an existing instance without recreating the expression.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var where = new []
    {
        new QueryField("LastName", Operation.Like, "Doe%"),
        new QueryField("State", Operation.Equal, "Michigan"),
        new QueryField("Age", Operation.Between, new [] (20, 40))
    };
    var people = connection.Query<Person>(where);
    
    // Do the stuffs for 'people' here

    // Reset here
    where.Reset();

    // Reuse it here
    var customers = connection.Query<Customer>(where);

    // Do the stuffs for 'customers' here
}
```

{: .note }
> The `Reset()` call above applies to the `IEnumerable<QueryField>` collection. It can also be called on individual instances.

## IsForUpdate Method

When the same field is used as both an update target and a qualifier, a parameter name collision occurs.

For example, updating `Name` from `John Doe` to `James Doe` using `Name` as the qualifier generates a collision:

```csharp
> UPDATE [dbo].[Person] SET Name = @Name WHERE Name = @Name;
```

The correct parameterized form requires distinct parameter names:

```csharp
> UPDATE [dbo].[Person] SET Name = @Name WHERE Name = @_Name;
```

Call `IsForUpdate()` explicitly on the qualifier field. After this call, the parameter name is prefixed with an underscore (`_`), resolving the collision.

```csharp
> UPDATE [dbo].[Person] SET Name = @Name WHERE Name = @_Name;
```
