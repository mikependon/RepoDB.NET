---
layout: default
sidebar: classes
title: "QueryGroup"
description: "A class that is being used to group a list of query expressions for the SQL statement. You can define the conjunction to be used during the multiple query expressions."
permalink: /class/querygroup
tags: [repodb, querygroup]
parent: CLASSES
---

# QueryGroup

---

Groups a list of fields used in query expressions, corresponding to a grouped `WHERE` clause in SQL.

It holds child `QueryGroup`(s), [QueryField](/class/queryfield)(s), a [Conjunction](/enumeration/conjunction), and an optional `NOT` unary expression.

Using this class improves performance because the library's core implementation relies on tree-structured query objects.

## Creating an Instance

```csharp
var field = new QueryGroup(new QueryField("Id", 10045));
```

Or with an array of [QueryField](/class/queryfield) objects.

```csharp
var queryFields = new []
{
    new QueryField("LastName", Operation.Like, "Doe%"),
    new QueryField("State", Operation.Equal, "Michigan"),
    new QueryField("Age", Operation.Between, new [] { 20, 40 })
};
var queryGroup = new QueryGroup(queryFields);
```

Child query groups can be nested for deeper expression trees.

```csharp
var whereA = new []
{
    new QueryField("LastName", Operation.Like, "Doe%"),
    new QueryField("State", Operation.Equal, "Michigan"),
    new QueryField("Age", Operation.Between, new [] { 20, 40 })
};
var whereB = new []
{
    new QueryField("LastName", Operation.NoLike, "Doe%"),
    new QueryField("State", Operation.NotEqual, "Michigan"),
    new QueryField("Age", Operation.NotBetween, new [] { 20, 40 })
};
var queryGroup = new QueryGroup(new [] { new QueryGroup(whereA), new QueryGroup(whereB) });
```

## Creating through Parse

Create a query group by parsing an object (.NET CLR type or dynamic).

```csharp
var param = new // This is a dynamic, can also be .NET CLR type
{
    LastName = "Doe",
    State = "Michigan"
};
var queryGroup = QueryGroup.Parse(param);
```

{: .important }
> All fields parsed via this method use the `Equal` operation.

Or via expression.

```csharp
var queryGroup = QueryGroup.Parse<Person>(p => p.LastName == "Doe" && State == "Michigan");
```

## Setting the Conjunction

By default, the conjunction is `AND`.

```csharp
var queryFields = new []
{
    new QueryField("LastName", Operation.Like, "Doe%"),
    new QueryField("State", Operation.Equal, "Michigan"),
    new QueryField("Age", Operation.Between, new [] { 20, 40 })
};
var queryGroup = new QueryGroup(queryFields);
```

Generated SQL:

```csharp
> WHERE ([LastName] LIKE @LastName AND [State] = @State AND [Age] BETWEEN (@Age_1, @Age_2));
```

Pass `Conjunction.Or` to use `OR`.

```csharp
var queryFields = new []
{
    new QueryField("LastName", Operation.Like, "Doe%"),
    new QueryField("State", Operation.Equal, "Michigan"),
    new QueryField("Age", Operation.Between, new [] { 20, 40 })
};
var queryGroup = new QueryGroup(queryFields, Conjunction.Or);
```

Generated SQL:

```csharp
> WHERE ([LastName] LIKE @LastName OR [State] = @State OR [Age] BETWEEN (@Age_1, @Age_2));
```

## Unary IS NOT

Use the `IsNot` property to negate the entire group expression.

To query all records that are not active and not male:

```csharp
var queryFields = new []
{
    new QueryField("IsActive", true),
    new QueryField("Gender", "Male")
};
var queryGroup = new QueryGroup(queryFields);
```

Default SQL:

```csharp
> WHERE ([IsActive] = @IsActive OR [Gender] = @Gender);
```

Pass `true` to `isNot` to negate:

```csharp
var queryGroup = new QueryGroup(queryFields, true);
```

Negated SQL:

```csharp
> WHERE NOT ([IsActive] = @IsActive OR [Gender] = @Gender);
```

{: .important }
> The default value is `false`. Prefer explicit targeted expressions over negation for better query performance.

## Getting all the Children

Use the `QueryGroups` property to retrieve child `QueryGroup`(s).

```csharp
var whereA = new QueryField("FirstName", Operation.Like, "J%");
var whereB = new []
{
    new QueryField("LastName", Operation.Like, "Doe%"),
    new QueryField("State", Operation.Equal, "Michigan"),
    new QueryField("Age", Operation.Between, new [] { 20, 40 })
};
var whereC = new []
{
    new QueryField("LastName", Operation.NoLike, "Doe%"),
    new QueryField("State", Operation.NotEqual, "Michigan"),
    new QueryField("Age", Operation.NotBetween, new [] { 20, 40 })
};
var queryGroup = new QueryGroup(whereA, new [] { new QueryGroup(whereB), new QueryGroup(whereC) });

// Access via `QueryGroups` property
var queryGroups = queryGroup.QueryGroups;

// There will be 2 QueryGroup(s) at the 'queryGroups' variable
```

Use the `QueryFields` property to retrieve child [QueryField](/class/queryfield)(s).

```csharp
var queryFields = queryGroup.QueryFiels;
// There will be 1 QueryField at the 'queryFields' variable
```

Use `GetFields()` to retrieve all [QueryField](/class/queryfield) objects recursively.

```csharp
var queryFields = queryGroup.GetFields(true);
// There will be 7 QueryField(s) at the 'queryFields' variable
```

## Converting to an Enumerable

Call `AsEnumerable()` to convert the instance to an `IEnumerable<QueryGroup>`.

```csharp
var queryField = new QueryField("CreatedDateUtc", Operation.GreaterThanOrEqual, DateTime.UtcNow.Date.AddDays(-1));
var queryGroup = new QueryGroup(queryField).AsEnumerable();
```

## Use-Cases

See the [use-cases](/class/queryfield#use-cases) section of the [QueryField](/class/queryfield) class.

## Retrieving the Conjunction Text

Call `GetConjunctionText()` to retrieve the SQL conjunction string.

```csharp
var where = new []
{
    new QueryField("LastName", Operation.Like, "Doe%"),
    new QueryField("State", Operation.Equal, "Michigan"),
    new QueryField("Age", Operation.NBetween, new [] { 20, 40 })
};
var queryGroup = new QueryGroup(where);
var conjunction = queryGroup.GetConjunctionText();
```

The value of `conjunction` is `AND`.

## The Fix Method

Used internally to resolve parameter name collisions before execution.

Given this expression:

```csharp
var whereA = new QueryField("FirstName", Operation.Like, "J%");
var whereB = new QueryField("FirstName", Operation.Like, "A%");
var whereC = new QueryField("FirstName", Operation.Like, "G%");
var queryGroup = new QueryGroup(new [] { whereA, whereB, whereC });
```

The raw SQL would incorrectly generate duplicate parameter names:

```csharp
> WHERE (FirstName = @FirstName AND FirstName = @FirstName AND FirstName = @FirstName);
```

Calling `Fix()` resolves the collision:

```csharp
queryGroup.Fix();
```

Fixed SQL:

```csharp
> WHERE (FirstName = @FirstName AND FirstName = @FirstName_1 AND FirstName = @FirstName_2);
```

## The GetString Method

Returns the SQL `WHERE` clause string for this group. Used internally during SQL statement construction.

```csharp
var where = new[]
{
    new QueryField("LastName", Operation.Like, "Doe%"),
    new QueryField("State", Operation.Equal, "Michigan"),
    new QueryField("Age", Operation.NotBetween, new [] { 20, 40 })
};
var queryGroup = new QueryGroup(where);
var whereText = queryGroup.GetString(connection.GetDbSetting());
```

The resulting value:

```csharp
([LastName] LIKE @LastName AND [State] = @State AND [Age] NOT BETWEEN @Age_Left AND @Age_Right)
```

## Reusability

Call `Reset()` to reuse an existing instance without recreating the expression.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var queryFields = new []
    {
        new QueryField("LastName", Operation.Like, "Doe%"),
        new QueryField("State", Operation.Equal, "Michigan"),
        new QueryField("Age", Operation.Between, new [] { 20, 40 })
    };
    var where = new QueryGroup(queryFields);
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
> The `Reset()` call above applies to the `IEnumerable<QueryGroup>` collection. It can also be called on individual instances.
