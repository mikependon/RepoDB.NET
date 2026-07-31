---
layout: default
title: UdtTypeName
permalink: /attribute/oracle/udttypename
tags: [repodb, attribute, udttypename]
parent: "Oracle"
grand_parent: ATTRIBUTES
---

# UdtTypeName

---

This attribute sets the `OracleParameter.UdtTypeName` property value via a class property. Required whenever the parameter binds an Oracle user-defined type (object type or collection).

### Attribute

Example usage:

```csharp
public class Person
{
    public int Id { get; set; }

    [UdtTypeName("SCHEMA.MY_TYPE")]
    public object Metadata { get; set; }
}
```

### Fluent Mapping

To configure via [FluentMapper](/mapper/fluentmapper):

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Metadata, new UdtTypeNameAttribute("SCHEMA.MY_TYPE"));
```

### Retrieval

Retrieve the attribute via [PropertyValueAttributeCache](/cacher/propertyvalueattributecache):

```csharp
var attribute = PropertyValueAttributeCache
    .Get<Person>(e => e.Metadata)?
    .FirstOrDefault(e => e.GetType() == typeof(UdtTypeNameAttribute));
```

{: .important }
> We strongly recommend using [PropertyValueAttributeCache](/cacher/propertyvalueattributecache) for maximum performance.
