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

This enum sets the `Operation` property of a [QueryField](/class/queryfield) object.

### Enum Values

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

Pass the value when instantiating a [QueryField](/class/queryfield) object.

```csharp
var field = new QueryField("Id", Operation.Equal, 10045);
```

### Type of Operation

#### Equal

Determines equality between the field and the specified value.

```csharp
var result = connection.Query<Customer>(new QueryField("Id", Operation.Equal, 10045));
```

#### NotEqual

Determines inequality between the field and the specified value.

```csharp
var result = connection.Query<Customer>(new QueryField("Name", Operation.NotEqual, "Anna Fullerton" });
```

#### LessThan

Determines whether the field value is less than the specified value.

```csharp
var result = connection.Query<Customer>(new QueryField("Id", Operation.LessThan, 100 });
```

#### GreaterThan

Determines whether the field value is greater than the specified value.

```csharp
var result = connection.Query<Customer>(new QueryField("Id", Operation.GreaterThan, 0 });
```

#### LessThanOrEqual

Determines whether the field value is less than or equal to the specified value.

```csharp
var result = connection.Query<Customer>.Query(new QueryField("Id", Operation.LessThanOrEqual, 100 });
```

#### GreaterThanOrEqual

Determines whether the field value is greater than or equal to the specified value.

```csharp
var result = connection.Query<Customer>(new QueryField("Id", Operation.GreaterThanOrEqual, 0 });
```

#### Like

Determines whether the field matches a given pattern.

```csharp
var result = connection.Query<Customer>(new QueryField("Name", Operation.Like, "Anna%" });
```

#### NotLike

Determines whether the field does not match a given pattern.

```csharp
var result = connection.Query<Customer>(new QueryField("Name", Operation.NotLike, "Anna%" });
```

#### Between

Determines whether the field value falls between two specified values.

```csharp
var result = connection.Query<Customer>(new QueryField("Id", Operation.Between, new [] { 10045, 10075 } });
```

#### NotBetween

Determines whether the field value does not fall between two specified values.

```csharp
var result = connection.Query<Customer>(new QueryField("Id", Operation.NotBetween, new [] { 10045, 10075 } });
```

#### In

Determines whether the field value is present in a set of values.

```csharp
var result = connection.Query<Customer>(new QueryField("Id", Operation.In, new [] { 10045, 10046, 10047, 10048 } });
```

#### NotIn

Determines whether the field value is absent from a set of values.

```csharp
var result = connection.Query<Customer>(new QueryField("Id", Operation.NotIn, new [] { 10045, 10046, 10047, 10048 } });
```
