---
layout: default
title: Direction
permalink: /attribute/parameter/direction
tags: [repodb, attribute, direction]
parent: Parameter
grand_parent: ATTRIBUTES
---

# Direction

---

This attribute is used to set the value of the `DbParameter.Direction` property via a class property.

### Attribute

Below a sample code on how to use this attribute.

```csharp
public class Person
{
    public int Id { get; set; }

    public string Name { get; set; }

    [Direction(ParameterDirection.InputOutput)]
    public double Assets { get; set; }
}
```

### Fluent Mapping

Below is a sample code on how to use this attribute via [FluentMapper](/mapper/fluentmapper).

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Assets, new ParameterDirectionAttribute(ParameterDirection.InputOutput));
```

> Currently, all the properties with output direction (i.e.: `Output`, `InputOutput`, `ReturnValue`) are not being updated with the values from the database. This capability will soon to be added on the library.

### Retrieval

You can retrieve the attribute via [PropertyValueAttributeCache](/cacher/propertyvalueattributecache).

```csharp
var attribute = PropertyValueAttributeCache
    .Get<Person>(e => e.Name, includeMappings: true)?
    .FirstOrDefault(e => e.GetType() == typeof(ParameterDirectionAttribute));
```

Or, via the [PropertyValueAttributeMapper](/mapper/propertyvalueattributemapper).

```csharp
var attribute = PropertyValueAttributeMapper
    .Get<Person>(e => e.Name)?
    .FirstOrDefault(e => e.GetType() == typeof(ParameterDirectionAttribute));
```

> We strongly suggest to always use the [PropertyValueAttributeCache](/cacher/propertyvalueattributecache) to maximize the performance.