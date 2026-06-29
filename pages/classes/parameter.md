---
layout: default
sidebar: classes
title: "Parameter"
description: "A class that defines a parameter of the data command object."
permalink: /class/parameter
tags: [repodb, parameter]
parent: CLASSES
---

# Parameter

---

Holds the information required to construct a data parameter. Used internally by [QueryGroup](/class/querygroup) and [QueryField](/class/queryfield) to manage parameter creation.

{: .note }
> The library uses this class as the basis for creating [DbParameter](https://learn.microsoft.com/en-us/dotnet/api/system.data.common.dbparameter?view=net-6.0) objects.

## Usability

```csharp
var parameter = new Parameter("@Id", 10045);
```

Or with a [DbType](https://learn.microsoft.com/en-us/dotnet/api/system.data.dbtype?view=net-6.0).

```csharp
var parameter = new Parameter("@Id", 10045, DbType.String);
```

## QueryField

[QueryField](/class/queryfield) instantiates this class internally.

```csharp
var queryField = new QueryField("Id", 10045);
var parameter = queryField.Parameter;
var name = parameter.Name; // @Id
var value = parameter.Value; // 10045
```

## QueryGroup

Since [QueryGroup](/class/querygroup) groups a list of [QueryField](/class/queryfield) objects, parameters can also be extracted from it.

```csharp
var queryFields = new[]
{
    new QueryField("LastName", Operation.Like, "Doe%"),
    new QueryField("State", "Michigan")
};
var queryGroup = new QueryGroup(queryFields);

// Extract
var parameters = queryGroup
    .GetFields(true)
    .Select(qf = qf.Parameter);

// Make the stuffs for the 'parameters' here
```

## Reset

Resets the instance to its initial state after instantiation.

```csharp
var parameter = new Parameter("@Id", 10045, false);
// Do the stuffs for the 'parameter' here
parameter.Reset();
```

{: .important }
> If [QueryField.IsForUpdate()](/class/queryfield#isforupdate-method) was called, the parameter name is prefixed with an underscore (`_`). Calling `Reset()` restores the original name.
