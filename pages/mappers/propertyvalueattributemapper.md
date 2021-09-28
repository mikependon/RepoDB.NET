---
layout: default
sidebar: mappers
title: "PropertyValueAttributeMapper"
description: "A mapper class that is being used to map the list PropertyValueAttribute objects into a class property. This class is used as an alternative to the PropertyValueAttribute attributes."
permalink: /mapper/propertyvalueattributemapper
tags: [repodb, class, propertyvalueattributemapper, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
parent: MAPPERS
---

# PropertyValueAttributeMapper

---

A mapper class that is being used to map the list [PropertyValueAttribute](/attribute/propertyvalueattribute) objects into a class property. This class is used as an alternative to the [PropertyValueAttribute](/attribute/propertyvalueattribute) attribute.

### Methods

Below are the methods available from this class.

- `Add` - adds a property attributes mapping into a property.
- `Clear` - clears all the existing property attribute mappings.
- `Get` - gets the existing mapped property attributes of the property.
- `Remove` - removes the exising mapped property attributes of the property.

### Usability

To add a mapping, simply call the `Add()` method.

```csharp
PropertyValueAttributeMapper.Add<Customer>(c => c.Name, new NameAttribute("CompleteName"));
```

You can also map multiple instances.

```csharp
PropertyValueAttributeMapper.Add<Customer>(c => c.Name, new PropertyValueAttribute[]
{
    new NameAttribute("CompleteName"),
    new SizeAttribute("256"),
    new DbTypeAttribute(DbType.NVarChar)
});
```

> An exception will be thrown if the mapping is already exists and you passed a `false` value in the `force` argument.

To get the mapping, use the `Get()` method.

```csharp
var property = PropertyValueAttributeMapper.Get<Customer>(c => c.Name);
```

> Please consider to always use the [PropertyValueAttributeCache](/cacher/propertyvalueattributecache) class when extracting the mapped property attributes.

To remove the mapping, use the `Remove()` method.

```csharp
PropertyValueAttributeMapper.Remove<Customer>(c => c.Name);
```