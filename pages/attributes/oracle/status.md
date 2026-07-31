---
layout: default
title: Status
permalink: /attribute/oracle/status
tags: [repodb, attribute, status]
parent: "Oracle"
grand_parent: ATTRIBUTES
---

# Status

---

This attribute sets the `OracleParameter.Status` property value via a class property.

### Attribute

Example usage:

```csharp
public class Person
{
    public int Id { get; set; }

    [Status(OracleParameterStatus.Success)]
    public string Name { get; set; }
}
```

### Fluent Mapping

To configure via [FluentMapper](/mapper/fluentmapper):

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Name, new StatusAttribute(OracleParameterStatus.Success));
```

### Retrieval

Retrieve the attribute via [PropertyValueAttributeCache](/cacher/propertyvalueattributecache):

```csharp
var attribute = PropertyValueAttributeCache
    .Get<Person>(e => e.Name)?
    .FirstOrDefault(e => e.GetType() == typeof(StatusAttribute));
```

{: .important }
> We strongly recommend using [PropertyValueAttributeCache](/cacher/propertyvalueattributecache) for maximum performance.
