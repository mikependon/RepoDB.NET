---
layout: default
title: ArrayBindStatus
permalink: /attribute/oracle/arraybindstatus
tags: [repodb, attribute, arraybindstatus]
parent: "Oracle"
grand_parent: ATTRIBUTES
---

# ArrayBindStatus

---

This attribute sets the `OracleParameter.ArrayBindStatus` property value via a class property. Only meaningful for an Array Bind or PL/SQL Associative Array Bind execution.

### Attribute

Example usage:

```csharp
public class Person
{
    public int Id { get; set; }

    [ArrayBindStatus(new [] { OracleParameterStatus.Success })]
    public string Name { get; set; }
}
```

### Fluent Mapping

To configure via [FluentMapper](/mapper/fluentmapper):

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Name, new ArrayBindStatusAttribute(new [] { OracleParameterStatus.Success }));
```

### Retrieval

Retrieve the attribute via [PropertyValueAttributeCache](/cacher/propertyvalueattributecache):

```csharp
var attribute = PropertyValueAttributeCache
    .Get<Person>(e => e.Name)?
    .FirstOrDefault(e => e.GetType() == typeof(ArrayBindStatusAttribute));
```

{: .important }
> We strongly recommend using [PropertyValueAttributeCache](/cacher/propertyvalueattributecache) for maximum performance.
