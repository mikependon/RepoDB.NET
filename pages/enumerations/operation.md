---
layout: default
sidebar: enumerations
title: "Operation"
description: "A enumeration that defines the different operations that can be used when composing a query expression."
permalink: /enumeration/operation
tags: [repodb, operation]
parent: ENUMERATIONS
---

# Operation

---

This enum is used to set the value of the `Operation` property of [QueryField](/class/queryfield) object.

### Enum Values

Below is the list of enum values.

| Name | Description |
|:-----|:------------|
| Equal (Default) | An equal operation. |
| NotEqual | A not-equal operation. |
| LessThan | A less-than operation. |
| GreaterThan | A greater-than operation. |
| LessThanOrEqual | A less-than-or-equal operation. |
| GreaterThanOrEqual | A greater-than-or-equal operation. |
| Like | A like operation. Defines the `LIKE` keyword in SQL statement. |
| NotLike | A not-like operation. Defines the `NOT LIKE` keyword in SQL statement. |
| Between | A between operation. Defines the `BETWEEN` keyword in SQL statement. |
| NotBetween | A not-between operation. Defines the `NOT BETWEEN` keyword in SQL statement. |
| In | an in operation. Defines the `IN` keyword in SQL statement. |
| NotIn | A not-in operation. Defines the `NOT IN` keyword in SQL statement. |

### Usability

Simply pass the value of when instantiating the [QueryField](/class/queryfield) object.

```csharp
var field = new QueryField("Id", Operation.Equal, 10045);
```

### Type of Operation

#### Equal

Part of the expression tree used to determine the equality of the field and data.

```csharp
var result = connection.Query<Customer>(new QueryField("Id", Operation.Equal, 10045));
```

#### NotEqual

Part of the expression tree used to determine the inequality of the field and data.

```csharp
var result = connection.Query<Customer>(new QueryField("Name", Operation.NotEqual, "Anna Fullerton" });
```

#### LessThan

Part of the expression tree used to determine whether the field value is less-than of the defined value.

```csharp
var result = connection.Query<Customer>(new QueryField("Id", Operation.LessThan, 100 });
```

#### GreaterThan

Part of the expression tree used to determine whether the field value is greater-than of the defined value.

```csharp
var result = connection.Query<Customer>(new QueryField("Id", Operation.GreaterThan, 0 });
```

#### LessThanOrEqual

Part of the expression tree used to determine whether the field value is less-than-or-equal of the defined value.

```csharp
var result = connection.Query<Customer>.Query(new QueryField("Id", Operation.LessThanOrEqual, 100 });
```

#### GreaterThanOrEqual

Part of the expression tree used to determine whether the field value is greater-than-or-equal of the defined value.

```csharp
var result = connection.Query<Customer>(new QueryField("Id", Operation.GreaterThanOrEqual, 0 });
```

#### Like

Part of the expression tree used to determine whether the field is identitical to a given value.

```csharp
var result = connection.Query<Customer>(new QueryField("Name", Operation.Like, "Anna%" });
```

#### NotLike

Part of the expression tree used to determine whether the field is not-identitical to a given value.

```csharp
var result = connection.Query<Customer>(new QueryField("Name", Operation.NotLike, "Anna%" });
```

#### Between

Part of the expression tree used to determine whether the field value is in-between the given 2 values.

```csharp
var result = connection.Query<Customer>(new QueryField("Id", Operation.Between, new [] { 10045, 10075 } });
```

#### NotBetween

Part of the expression tree used to determine whether the field value is not-in-between of the given 2 values.

```csharp
var result = connection.Query<Customer>(new QueryField("Id", Operation.NotBetween, new [] { 10045, 10075 } });
```

#### In

Part of the expression tree used to determine whether the field value is in the given values.

```csharp
var result = connection.Query<Customer>(new QueryField("Id", Operation.In, new [] { 10045, 10046, 10047, 10048 } });
```

#### NotIn

Part of the expression tree used to determine whether the field value is not-in given values.

```csharp
var result = connection.Query<Customer>(new QueryField("Id", Operation.NotIn, new [] { 10045, 10046, 10047, 10048 } });
```

