---
layout: default
title: Size
permalink: /attribute/parameter/size
tags: [repodb, attribute, size, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
parent: Parameter
grand_parent: ATTRIBUTES
---

# Size

---

This attribute is used to set the value of the `DbParameter.Size` property via a class property.

### Attribute

Below is a a sample code on how to use this attribute.

```csharp
public class Person
{
    public int Id { get; set; }

    [Size(256)]
    public string Name { get; set; }
}
```

This works exactly the same as [Map](/attribute/map) or the `Table` attribute of the `System.ComponentModel.DataAnnotations.Schema` namespace.

### Fluent Mapping

Below is a sample code on how to use this attribute via [FluentMapper](/mapper/fluentmapper).

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Name, new [] { new SizeAttribute(256) })
```

> The value from the database table/view is taking the precedence if present.