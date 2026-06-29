---
layout: default
title: UdtTypeName
permalink: /attribute/sqlserver/udttypename
tags: [repodb, attribute, udttypename]
parent: "SQL Server"
grand_parent: ATTRIBUTES
---

# UdtTypeName

---

This attribute sets the `SqlParameter.UdtTypeName` property value via a class property.

### Attribute

Example usage:

```csharp
public class Person
{
    public int Id { get; set; }

    [UdtTypeName("UdtTypeName")]
    public string Name { get; set; }
}
```

### Fluent Mapping

To configure via [FluentMapper](/mapper/fluentmapper):

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Name, new UdtTypeNameAttribute("UdtTypeName"));
```

### Retrieval

Retrieve the attribute via [PropertyValueAttributeCache](/cacher/propertyvalueattributecache):

```csharp
var attribute = PropertyValueAttributeCache
    .Get<Person>(e => e.Name)?
    .FirstOrDefault(e => e.GetType() == typeof(UdtTypeNameAttribute));
```

Or via [PropertyValueAttributeMapper](/mapper/propertyvalueattributemapper):

```csharp
var attribute = PropertyValueAttributeMapper
    .Get<Person>(e => e.Name)?
    .FirstOrDefault(e => e.GetType() == typeof(UdtTypeNameAttribute));
```

{: .important }
> We strongly recommend using [PropertyValueAttributeCache](/cacher/propertyvalueattributecache) for maximum performance.
