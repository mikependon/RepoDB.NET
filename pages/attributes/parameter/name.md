---
layout: default
title: Name
permalink: /attribute/parameter/name
tags: [repodb, attribute, name]
parent: Parameter
grand_parent: ATTRIBUTES
---

# Name

---

This attribute sets the `DbParameter.Name` property value via a class property.

### Attribute

Example usage:

```csharp
public class Person
{
    public int Id { get; set; }

    [Name("ColumnName")]
    public string Name { get; set; }
}
```

This behaves identically to the [Map](/attribute/map) attribute and the [Table](https://learn.microsoft.com/en-us/dotnet/api/system.componentmodel.dataannotations.schema.tableattribute?view=net-6.0) attribute from [System.ComponentModel.DataAnnotations.Schema](https://learn.microsoft.com/en-us/dotnet/api/system.componentmodel.dataannotations.schema?view=net-6.0).

### Fluent Mapping

To configure via [FluentMapper](/mapper/fluentmapper):

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Name, new NameAttribute("ColumnName"));
```

> The value from the database table or view takes precedence if present.

### Retrieval

Retrieve the attribute via [PropertyValueAttributeCache](/cacher/propertyvalueattributecache):

```csharp
var attribute = PropertyValueAttributeCache
    .Get<Person>(e => e.Name)?
    .FirstOrDefault(e => e.GetType() == typeof(NameAttribute));
```

Or via [PropertyValueAttributeMapper](/mapper/propertyvalueattributemapper):

```csharp
var attribute = PropertyValueAttributeMapper
    .Get<Person>(e => e.Name)?
    .FirstOrDefault(e => e.GetType() == typeof(NameAttribute));
```

{: .important }
> We strongly recommend using [PropertyValueAttributeCache](/cacher/propertyvalueattributecache) for maximum performance.
