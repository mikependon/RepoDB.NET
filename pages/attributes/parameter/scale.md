---
layout: default
title: Scale
permalink: /attribute/parameter/scale
tags: [repodb, attribute, scale, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
parent: Parameter
grand_parent: ATTRIBUTES
---

# Scale

---

This attribute is used to set the value of the `DbParameter.Scale` property via a class property.

### Attribute

Below is a a sample code on how to use this attribute.

```csharp
public class Person
{
    public int Id { get; set; }

    public string Name { get; set; }

    [Scale(1)]
    public double Assets { get; set; }
}
```

### Fluent Mapping

Below is a sample code on how to use this attribute via [FluentMapper](/mapper/fluentmapper).

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Assets, new [] { new ScaleAttribute(1) })
```

> The value from the database table/view is taking the precedence if present.