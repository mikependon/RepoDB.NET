---
layout: navpage
sidebar: classes
title: "Parameter"
description: "A class that defines a parameter of the data command object."
permalink: /class/parameter
tags: [repodb, class, parameter, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# Parameter

This class is used to handle the necesasry information when constructing a data parameter. It is used by both [QueryGroup](/class/querygroup) and [QueryField](/class/queryfield) objects to manage all the parameters creation.

> Internally, the library is using this class as an entries for creating the `DbParameter` objects.

#### How to use?

```csharp
var parameter = new Parameter("@Id", 10045, false);
// Do the stuffs for the 'parameter' here
```

#### QueryField

The [QueryField](/class/queryfield) class is instantiating this class internally during the constructor.

```csharp
var queryField = new QueryField("Id", 10045);
var parameter = queryField.Parameter;
var name = parameter.Name; // @Id
var value = parameter.Value; // 10045
```

#### QueryGroup

Since the [QueryGroup](/class/querygroup) is a grouping class of the list of [QueryField](/class/queryfield) objects, then you can also extract the parameters here.

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

