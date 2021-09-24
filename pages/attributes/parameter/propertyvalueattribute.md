---
layout: default
title: PropertyValueAttribute
nav_order: 1
permalink: /attribute/parameter/propertyvalueattribute
tags: [repodb, attribute, propertyvalueattribute, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
parent: Parameter
grand_parent: ATTRIBUTES
---

# PropertyValueAttribute

---

This is the base class for all the attributes that are used when setting the properties of the `DbParameter` object.

### Attribute

Below a sample code on how to use this attribute.

```csharp
public class Person
{
    public int Id { get; set; }

    [PropertyValue(typeof(DbParameter), nameof(DbParameter.ParameterName), "CompleteName")]
    public string Name { get; set; }
}
```

### Fluent Mapping

Below is a sample code on how to use this attribute via [FluentMapper](/mapper/fluentmapper).

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Name,
        new [] { new PropertyValueAttribute(typeof(DbParameter), nameof(DbParameter.ParameterName), "CompleteName" })
```

### Implementation

Below is a sample code on how to implement a class that inherits this class.

```csharp
public class ParameterNameAttribute : PropertyValueAttribute
{
    public ParameterNameAttribute(string parameterName)
        : base(typeof(DbParameter), nameof(DbParameter.ParameterName), parameterName)
    { }
}
```

After that, your customized attribute can then be used on a class.

```csharp
public class Person
{
    public int Id { get; set; }

    [ParameterName("CompleteName")]
    public string Name { get; set; }
}
```

Or via [FluentMapper](/mapper/fluentmapper).

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Name,
        new [] { new ParameterNameAttribute("CompleteName" })
```

> The implementation is dynamic, therefore, you can target any property of the `DbParameter` regardless of the data providers (i.e.: SQL Server, PostgreSQL, MySQL or SQLite).

