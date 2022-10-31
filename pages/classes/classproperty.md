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

This class is used as a container of the `System.Reflection.PropertyInfo` object. It is one of the core class that helps the library speed up the manipulation of the class properties.

To extract the properties of the entity, use the [PropertyCache](/cacher/propertycache) object.

```csharp
var properties = PropertyCache.Get<Person>();
```

Or the [ClassExpression](/class/classexpression) object.

```csharp
var properties = ClassExpression.GetProperties<Person>();
```

{: .note }
> The `PropertyCache` is also using the [ClassExpression](/class/classexpression) underneath, however, it caches the already extracted class properties for future reusabilities.

## AsField

This method is used to convert the current instance into [Field](/class/field) object.

```csharp
var primary = PropertyCache.Get<Person>().FirstOrDefault(p => p.IsPrimary() == true);
var field = primary.AsField();
```

## GetDbType

This method is used to get the equivalent `System.DbType` value of the property based on the property type.

```csharp
var primary = PropertyCache.Get<Person>().FirstOrDefault(p => p.IsPrimary() == true);
var dbType = primary.GetDbType();
```

{: .note }
> This is useful when you are creating a `DbParameter` object before passing it to the actual `DbCommand` object for execution.

## GetPropertyHandler

This method is used to get the existing mapped property handler of the property.

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

This method is used to get the existing [Identity](/attribute/identity) attribute if present.

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

This method is used to get the existing [Primary](/attribute/primary) attribute if present.

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

This method is used to get the existing mapping defined on the property. It extracts the value of the [Map](/attribute/map) attribute if present.

```csharp
var primary = PropertyCache.Get<Person>().FirstOrDefault(p => p.IsPrimary() == true);
var mappedName = primary.GetMappedName();
```

## IsIdentity

This method is used to identify whether the property is an identity. It uses the `GetIdentityAttribute` underneath to check whether the property is an identity.

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

This method is used to identify whether the property is a primary property. It uses the `GetPrimaryAttribute` underneath to check whether the property is a primary property.

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

If the [Primary](/attribute/primary) is not present, it tries to identify using the following logics by order.

- If the name is equals to `Id`.
- If the name is equals to `ClassName` + `Id`.
- If the name is equals to `ClassMappedName` + `Id`.

{: .note }
> The logic of extracting the primary property is being compared with case-insensitivity.




