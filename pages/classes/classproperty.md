---
layout: default
sidebar: classes
title: "ClassProperty"
description: "A container class that holds an instance of property object. It also contains all the necesarry methods that is relevant to the current property object."
permalink: /class/classproperty
tags: [repodb, classproperty]
parent: CLASSES
---

# ClassProperty

---

This class wraps a `System.Reflection.PropertyInfo` object and is one of the core classes that accelerates class property manipulation within the library.

Use [PropertyCache](/cacher/propertycache) to extract entity properties.

```csharp
var properties = PropertyCache.Get<Person>();
```

Or use [ClassExpression](/class/classexpression).

```csharp
var properties = ClassExpression.GetProperties<Person>();
```

{: .note }
> `PropertyCache` uses [ClassExpression](/class/classexpression) internally and caches the results for reuse.

## AsField

Converts the current instance to a [Field](/class/field) object.

```csharp
var primary = PropertyCache.Get<Person>().FirstOrDefault(p => p.IsPrimary() == true);
var field = primary.AsField();
```

## GetDbType

Returns the equivalent `System.DbType` value for the property based on its type.

```csharp
var primary = PropertyCache.Get<Person>().FirstOrDefault(p => p.IsPrimary() == true);
var dbType = primary.GetDbType();
```

{: .note }
> Useful when constructing a [DbParameter](https://learn.microsoft.com/en-us/dotnet/api/system.data.common.dbparameter?view=net-6.0) object before passing it to a `DbCommand`.

## GetPropertyHandler

Returns the mapped property handler for this property, if one exists.

```csharp
var properties = PropertyCache.Get<Person>();
foreach (var property in properties)
{
    var propertyHandler = property.GetPropertyHandler();
    if (propertyHandler != null)
    {
        // This property has property handler
    }
}
```

## GetIdentityAttribute

Returns the [Identity](/attribute/identity) attribute if present.

```csharp
var primary = PropertyCache.Get<Person>().FirstOrDefault(p => p.IsPrimary() == true);
var attribute = primary.GetIdentityAttribute();
var isIdentity = (attribute != null)
if (isIdentity)
{
    // The primary is also an identity
}
```

## GetPrimaryAttribute

Returns the [Primary](/attribute/primary) attribute if present.

```csharp
var properties = PropertyCache.Get<Person>();
foreach (var property in properties)
{
    var attribute = property.GetPrimaryAttribute();
    if (attribute != null)
    {
        // This property is primary
    }
}
```

## GetMappedName

Returns the mapped column name for the property, extracted from the [Map](/attribute/map) attribute if present.

```csharp
var primary = PropertyCache.Get<Person>().FirstOrDefault(p => p.IsPrimary() == true);
var mappedName = primary.GetMappedName();
```

## IsIdentity

Returns `true` if the property is an identity column. Uses `GetIdentityAttribute` internally.

```csharp
var properties = PropertyCache.Get<Person>();
foreach (var property in properties)
{
    if (property.IsIdentity() == true)
    {
        // This property is identity
    }
}
```

## IsPrimary

Returns `true` if the property is the primary key. Uses `GetPrimaryAttribute` internally.

```csharp
var properties = PropertyCache.Get<Person>();
foreach (var property in properties)
{
    if (property.IsIdentity() == true)
    {
        // This property is identity
    }
}
```

If the [Primary](/attribute/primary) attribute is absent, the library falls back to the following checks in order:

- Name equals `Id`.
- Name equals `ClassName` + `Id`.
- Name equals `ClassMappedName` + `Id`.

{: .note }
> Primary property detection is case-insensitive.
