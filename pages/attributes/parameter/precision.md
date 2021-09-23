---
layout: default
title: Precision
permalink: /attribute/parameter/precision
tags: [repodb, attribute, precision, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
parent: PARAMETER
grand_parent: ATTRIBUTES
---

# Precision

---

This attribute is used to set the value of the `DbParameter.Precision` property via a class property.

### Attribute

Below is a a sample code on how to use this attribute.

```csharp
public class Person
{
    public int Id { get; set; }

    public string Name { get; set; }

    [Precision(1)]
    public double Assets { get; set; }
}
```

### Fluent Mapping

Below is a sample code on how to use this attribute via [FluentMapper](/mapper/fluentmapper).

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Assets, new [] { new PrecisionAttribute(1) })
```

> The value from the database table/view is taking the precedence if present.