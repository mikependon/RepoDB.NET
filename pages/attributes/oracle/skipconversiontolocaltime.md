---
layout: default
title: SkipConversionToLocalTime
permalink: /attribute/oracle/skipconversiontolocaltime
tags: [repodb, attribute, skipconversiontolocaltime]
parent: "Oracle"
grand_parent: ATTRIBUTES
---

# SkipConversionToLocalTime

---

This attribute sets the `OracleParameter.SkipConversionToLocalTime` property value via a class property. Specifies whether the value bound to this parameter should skip conversion to local time.

### Attribute

Example usage:

```csharp
public class Person
{
    public int Id { get; set; }

    [SkipConversionToLocalTime(true)]
    public DateTime CreatedDateUtc { get; set; }
}
```

### Fluent Mapping

To configure via [FluentMapper](/mapper/fluentmapper):

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.CreatedDateUtc, new SkipConversionToLocalTimeAttribute(true));
```

### Retrieval

Retrieve the attribute via [PropertyValueAttributeCache](/cacher/propertyvalueattributecache):

```csharp
var attribute = PropertyValueAttributeCache
    .Get<Person>(e => e.CreatedDateUtc)?
    .FirstOrDefault(e => e.GetType() == typeof(SkipConversionToLocalTimeAttribute));
```

{: .important }
> We strongly recommend using [PropertyValueAttributeCache](/cacher/propertyvalueattributecache) for maximum performance.
