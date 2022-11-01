---
layout: default
title: PropertyValueAttribute
nav_order: 1
permalink: /attribute/parameter/propertyvalueattribute
tags: [repodb, attribute, propertyvalueattribute]
parent: Parameter
grand_parent: ATTRIBUTES
---

# PropertyValueAttribute

---

This is the base class for all the attributes that are used when setting the properties of the [DbParameter](https://learn.microsoft.com/en-us/dotnet/api/system.data.common.dbparameter?view=net-6.0) object.

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
        new PropertyValueAttribute(typeof(DbParameter), nameof(DbParameter.ParameterName), "CompleteName"));
```

You can also map multiple instances.

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Name, new []
    {
        new PropertyValueAttribute(typeof(DbParameter), nameof(DbParameter.ParameterName), "CompleteName")
        new PropertyValueAttribute(typeof(DbParameter), nameof(DbParameter.DbType), DbType.NVarChar)
    });
```

### Retrieval

You can retrieve the list of mapped attributes via [PropertyValueAttributeCache](/cacher/propertyvalueattributecache).

```csharp
var attributes = PropertyValueAttributeCache.Get<Person>(e => e.Name);
```

Or, via the [PropertyValueAttributeMapper](/mapper/propertyvalueattributemapper).

```csharp
var attributes = PropertyValueAttributeMapper.Get<Person>(e => e.Name);
```

{: .important }
> We strongly suggest to always use the [PropertyValueAttributeCache](/cacher/propertyvalueattributecache) to maximize the performance.

### Implementation

Below is a sample code on how to implement a class that inherits this class.

```csharp
public class CustomizedNameAttribute : PropertyValueAttribute
{
    public CustomizedNameAttribute(string parameterName)
        : base(typeof(DbParameter), nameof(DbParameter.ParameterName), parameterName)
    { }
}
```

After that, your customized attribute can then be used on a class.

```csharp
public class Person
{
    public int Id { get; set; }

    [CustomizedName("CompleteName")]
    public string Name { get; set; }
}
```

Or via [FluentMapper](/mapper/fluentmapper).

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Name,
        new [] { new CustomizedNameAttribute("CompleteName" })
```

{: .note }
> The implementation is dynamic, therefore, you can target any property of the `IDbDataParameter` regardless of the data providers (i.e.: SQL Server, PostgreSQL, MySQL or SQLite).

