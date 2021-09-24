---
layout: default
title: IsNullable
permalink: /attribute/parameter/isnullable
tags: [repodb, attribute, isnullable, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
parent: Parameter
grand_parent: ATTRIBUTES
---

# IsNullable

---

This attribute is used to set the value of the `DbParameter.IsNullable` property via a class property.

### Attribute

Below is a a sample code on how to use this attribute.

```csharp
public class Person
{
    public int Id { get; set; }

    [IsNullable(false)]
    public string Name { get; set; }
}
```

### Fluent Mapping

Below is a sample code on how to use this attribute via [FluentMapper](/mapper/fluentmapper).

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Name, new [] { new IsNullableAttribute(false) })
```

> The value from the database table/view is taking the precedence if present.