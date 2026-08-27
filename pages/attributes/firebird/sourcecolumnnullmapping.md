---
layout: default
title: SourceColumnNullMapping
permalink: /attribute/firebird/sourcecolumnnullmapping
tags: [repodb, attribute, sourcecolumnnullmapping]
parent: "Firebird"
grand_parent: ATTRIBUTES
---

# SourceColumnNullMapping

---

This attribute sets the `FbParameter.SourceColumnNullMapping` property value via a class property.

### Attribute

Example usage:

```csharp
public class Person
{
    public int Id { get; set; }

    [SourceColumnNullMapping(true)]
    public string Name { get; set; }
}
```

### Fluent Mapping

To configure via [FluentMapper](/mapper/fluentmapper):

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Name, new SourceColumnNullMappingAttribute(true));
```

### Retrieval

Retrieve the attribute via [PropertyValueAttributeCache](/cacher/propertyvalueattributecache):

```csharp
var attribute = PropertyValueAttributeCache
    .Get<Person>(e => e.Name)?
    .FirstOrDefault(e => e.GetType() == typeof(SourceColumnNullMappingAttribute));
```

{: .important }
> We strongly recommend using [PropertyValueAttributeCache](/cacher/propertyvalueattributecache) for maximum performance.
