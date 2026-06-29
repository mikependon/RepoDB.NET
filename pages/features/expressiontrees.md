---
layout: default
sidebar: features
title: "Expression Trees"
description: "This is a feature that would allow you to compose a conditional expressions (to filter a data) when doing an operation in the database."
permalink: /feature/expressiontrees
tags: [repodb, expressiontrees]
parent: FEATURES
---

# Expression Trees

---

This feature allows you to compose conditional expressions to filter data during database operations. Conditions can be applied to both push and pull operations (e.g., [Insert](/operation/insert), [Delete](/operation/delete), [Update](/operation/update), and [Query](/operation/query)).

Expression trees can be composed using the following objects:

| Object | Description  | 
|:-------------|:-------------|
| [Anonymous Types](#anonymous-type) | The simplest and most direct way to filter results using an anonymous object. |
| [Linq-Expression](#linq-expression) | The most common way to filter data. Works only with entity-model-based operations. |
| [ExpandoObject or IDictionary<string, object>](#expandoobject-and-dictionarystring-object) | The most dynamic way to filter data. |
| [QueryField or QueryGroup](#queryfieldquerygroup) | The most advanced, efficient, and powerful way to compose tree expressions, though more verbose. |

While the library provides broad support for query objects, the Linq-Expression parser is not as extensive as macro-ORMs. For complex expressions, [QueryGroup](/class/querygroup) and [QueryField](/class/queryfield) are recommended.

## Anonymous Type

Query via an anonymous type:

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var customer = connection.Query<Customer>(new { Id = 10045 }).FirstOrDefault();
}
```

Or with multiple columns:

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var customer = connection.Query<Customer>(new { FirstName = "John", LastName = "Doe" }).FirstOrDefault();
}
```

{: .warning }
> The compiler does not validate anonymous types — column name changes do not trigger compile-time exceptions. Additionally, anonymous types only support equality expressions and cannot be used for other comparisons (e.g., not-equal, greater-than, less-than).

## Linq-Expression

Query via a Linq expression:

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var customer = connection.Query<Customer>(e => e.Id == 10045).FirstOrDefault();
}
```

Or with multiple columns:

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var customer = connection.Query<Customer>(e => e.FirstName == "John" && e.LastName == "Doe" }).FirstOrDefault();
}
```

Or with additional operations:

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var states = new [] { "Chicago", "Washington", "Kansas" };
    var customers = connection.Query<Customer>(e => states.Contains(e.State) && e.IsActive == false && e.DateOfBirth >= DateTime.Parse("1970-01-01") });
}
```

## ExpandoObject and Dictionary<string, object>

Query via an [ExpandoObject](https://learn.microsoft.com/en-us/dotnet/api/system.dynamic.expandoobject?view=net-7.0) or [IDictionary<string, object>](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.idictionary-2?view=net-6.0):

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var where = new Dictionary<string, object>
    {
        { nameof(Customer.Id), 10045 }
    };
    var customer = connection.Query<Customer>(where).FirstOrDefault();
}
```

Or with multiple columns:

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var where = new new Dictionary<string, object>
    {
        { nameof(Customer.FirstName), "John") },
        { nameof(Customer.LastName), "Doe" }
    };
    var customer = connection.Query<Customer>(where).FirstOrDefault();
}
```

{: .important }
> Both [ExpandoObject](https://learn.microsoft.com/en-us/dotnet/api/system.dynamic.expandoobject?view=net-7.0) and [IDictionary<string, object>](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.idictionary-2?view=net-6.0) only support equality expressions and cannot be used for other comparisons (e.g., not-equal, greater-than, less-than).

## QueryField/QueryGroup

Query via [QueryField](/class/queryfield):

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var customer = connection.Query<Customer>(new QueryField(nameof(Customer.Id), 10045)).FirstOrDefault();
}
```

Or with multiple columns:

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var where = new []
    {
        new QueryField(nameof(Customer.FirstName), "John"),
        new QueryField(nameof(Customer.LastName), "Doe")
    };
    var customer = connection.Query<Customer>(where).FirstOrDefault();
}
```

Or with additional operations:

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var states = new [] { "Chicago", "Washington", "Kansas" };
    var where = new []
    {
        new QueryField(nameof(Customer.State), Operation.In, states),
        new QueryField(nameof(Customer.IsActive),false),
        new QueryField(nameof(Customer.DateOfBirth), Operation.GreaterThanOrEqual, DateTime.Parse("1970-01-01"))
    };
    var customers = connection.Query<Customer>(where);
}
```

{: .note }
> Please visit our [Query Expressions](/reference/queryexpressions) reference implementation page for more details.
