---
layout: navpage
sidebar: enumerations
title: "Operation (RepoDb)"
permalink: /enumeration/operation
tags: [repodb, class, operation, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# Operation

This enum is used to set the value of the `Operation` property of [QueryField](/class/queryfield) object.

#### Enum Values

- `Equal (Default)` = an equal operation.
- `NotEqual` - a not-equal operation.
- `LessThan` - a less-than operation.
- `GreaterThan` - a greater-than operation.
- `LessThanOrEqual` - a less-than-or-equal operation.
- `GreaterThanOrEqual` - a greater-than-or-equal operation.
- `Like` - a like operation. Defines the `LIKE` keyword in SQL statement.
- `NotLike` - a not-like operation. Defines the `NOT LIKE` keyword in SQL statement.
- `Between` - a between operation. Defines the `BETWEEN` keyword in SQL statement.
- `NotBetween` - a not-between operation. Defines the `NOT BETWEEN` keyword in SQL statement.
- `In` - an in operation. Defines the `IN` keyword in SQL statement.
- `NotIn` - a not-in operation. Defines the `NOT IN` keyword in SQL statement.

#### How to Use?

Simply pass the value of when instantiating the [QueryField](/class/queryfield) object.

```csharp
var field = new QueryField("Id", Operation.Equal, 10045);
```

#### Type of Operation

##### Equal

Part of the expression tree used to determine the equality of the field and data.

```csharp
var result = connection.Query<Customer>(new QueryField("Id", Operation.Equal, 10045));
```

##### NotEqual

Part of the expression tree used to determine the inequality of the field and data.

```csharp
var result = connection.Query<Customer>(new QueryField("Name", Operation.NotEqual, "Anna Fullerton" });
```

##### LessThan

Part of the expression tree used to determine whether the field value is less-than of the defined value.

```csharp
var result = connection.Query<Customer>(new QueryField("Id", Operation.LessThan, 100 });
```

##### GreaterThan

Part of the expression tree used to determine whether the field value is greater-than of the defined value.

```csharp
var result = connection.Query<Customer>(new QueryField("Id", Operation.GreaterThan, 0 });
```

##### LessThanOrEqual

Part of the expression tree used to determine whether the field value is less-than-or-equal of the defined value.

```csharp
var result = connection.Query<Customer>.Query(new QueryField("Id", Operation.LessThanOrEqual, 100 });
```

##### GreaterThanOrEqual

Part of the expression tree used to determine whether the field value is greater-than-or-equal of the defined value.

```csharp
var result = connection.Query<Customer>(new QueryField("Id", Operation.GreaterThanOrEqual, 0 });
```

##### Like

Part of the expression tree used to determine whether the field is identitical to a given value.

```csharp
var result = connection.Query<Customer>(new QueryField("Name", Operation.Like, "Anna%" });
```

##### NotLike

Part of the expression tree used to determine whether the field is not-identitical to a given value.

```csharp
var result = connection.Query<Customer>(new QueryField("Name", Operation.NotLike, "Anna%" });
```

##### Between

Part of the expression tree used to determine whether the field value is in-between the given 2 values.

```csharp
var result = connection.Query<Customer>(new QueryField("Id", Operation.Between, new [] { 10045, 10075 } });
```

##### NotBetween

Part of the expression tree used to determine whether the field value is not-in-between of the given 2 values.

```csharp
var result = connection.Query<Customer>(new QueryField("Id", Operation.NotBetween, new [] { 10045, 10075 } });
```

##### In

Part of the expression tree used to determine whether the field value is in the given values.

```csharp
var result = connection.Query<Customer>(new QueryField("Id", Operation.In, new [] { 10045, 10046, 10047, 10048 } });
```

##### NotIn

Part of the expression tree used to determine whether the field value is not-in given values.

```csharp
var result = connection.Query<Customer>(new QueryField("Id", Operation.NotIn, new [] { 10045, 10046, 10047, 10048 } });
```

