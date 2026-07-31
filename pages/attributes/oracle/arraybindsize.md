---
layout: default
title: ArrayBindSize
permalink: /attribute/oracle/arraybindsize
tags: [repodb, attribute, arraybindsize]
parent: "Oracle"
grand_parent: ATTRIBUTES
---

# ArrayBindSize

---

This attribute sets the `OracleParameter.ArrayBindSize` property value via a class property. Only meaningful for variable-length types (e.g. `Varchar2`, `Clob`, `Blob`) used in an Array Bind or PL/SQL Associative Array Bind execution; ignored for fixed-length types.

### Attribute

Example usage:

```csharp
public class Person
{
    public int Id { get; set; }

    [ArrayBindSize(new [] { 128, 128, 128 })]
    public string Name { get; set; }
}
```

### Fluent Mapping

To configure via [FluentMapper](/mapper/fluentmapper):

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Name, new ArrayBindSizeAttribute(new [] { 128, 128, 128 }));
```

### Retrieval

Retrieve the attribute via [PropertyValueAttributeCache](/cacher/propertyvalueattributecache):

```csharp
var attribute = PropertyValueAttributeCache
    .Get<Person>(e => e.Name)?
    .FirstOrDefault(e => e.GetType() == typeof(ArrayBindSizeAttribute));
```

{: .important }
> We strongly recommend using [PropertyValueAttributeCache](/cacher/propertyvalueattributecache) for maximum performance.
