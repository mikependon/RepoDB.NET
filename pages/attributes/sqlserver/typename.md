---
layout: default
title: TypeName
permalink: /attribute/sqlserver/typename
tags: [repodb, attribute, typename]
parent: "SQL Server"
grand_parent: ATTRIBUTES
---

# TypeName

---

This attribute sets the `SqlParameter.TypeName` property value via a class property.

### Attribute

Example usage:

```csharp
public class Person
{
    public int Id { get; set; }

    [TypeName("TypeName")]
    public string Name { get; set; }
}
```

### Fluent Mapping

To configure via [FluentMapper](/mapper/fluentmapper):

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Name, new TypeNameAttribute("TypeName"));
```

### Retrieval

Retrieve the attribute via [PropertyValueAttributeCache](/cacher/propertyvalueattributecache):

```csharp
var attribute = PropertyValueAttributeCache
    .Get<Person>(e => e.Name)?
    .FirstOrDefault(e => e.GetType() == typeof(TypeNameAttribute));
```

Or via [PropertyValueAttributeMapper](/mapper/propertyvalueattributemapper):

```csharp
var attribute = PropertyValueAttributeMapper
    .Get<Person>(e => e.Name)?
    .FirstOrDefault(e => e.GetType() == typeof(TypeNameAttribute));
```

{: .important }
> We strongly recommend using [PropertyValueAttributeCache](/cacher/propertyvalueattributecache) for maximum performance.
