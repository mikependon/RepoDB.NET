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

This attribute is used to set the value of the `DbParameter.Name` property via a class property.

### Attribute

Below a sample code on how to use this attribute.

```csharp
public class Person
{
    public int Id { get; set; }

    [Name("ColumnName")]
    public string Name { get; set; }
}
```

This works exactly the same as [Map](/attribute/map) or the `Table` attribute of the `System.ComponentModel.DataAnnotations.Schema` namespace.

### Fluent Mapping

Below is a sample code on how to use this attribute via [FluentMapper](/mapper/fluentmapper).

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Name, new NameAttribute("ColumnName"));
```

> The value from the database table/view is taking the precedence if present.

### Retrieval

You can retrieve the attribute via [PropertyValueAttributeCache](/cacher/propertyvalueattributecache).

```csharp
var attribute = PropertyValueAttributeCache
    .Get<Person>(e => e.Name)?
    .FirstOrDefault(e => e.GetType() == typeof(NameAttribute));
```

Or, via the [PropertyValueAttributeMapper](/mapper/propertyvalueattributemapper).

```csharp
var attribute = PropertyValueAttributeMapper
    .Get<Person>(e => e.Name)?
    .FirstOrDefault(e => e.GetType() == typeof(NameAttribute));
```

{: .important }
> We strongly suggest to always use the [PropertyValueAttributeCache](/cacher/propertyvalueattributecache) to maximize the performance.
