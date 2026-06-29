---
layout: default
sidebar: classes
title: "PropertyValue"
description: "A class that holds the value of class or data entity property and its values."
permalink: /class/propertyvalue
tags: [repodb, propertyvalue]
parent: CLASSES
---

# PropertyValue

---

Holds the name and value of a class property, along with a reference to its [ClassProperty](/class/classproperty) instance.

{: .note }
> The library uses this class internally as a parameter before executing operations.

## Use Case

- Manually handling properties and values of a data entity.
- Fast extraction of entity properties and values via [ClassExpression](/class/classexpression).

## Usability

```csharp
var person = repository.Query(10045);
var properties = PropertyCache.Get<Person>();
var propertiesAndValues = properties.Select(p => new PropertyValue(p.GetMappedName(), p.PropertyInfo.GetValue(person), p));
// Do the stuffs for the 'propertiesAndValues' here
```

Or via [ClassExpression](/class/classexpression).

```csharp
var person = repository.Query(10045);
var propertiesAndValues = ClassExpression.GetProperties<Person>(person);
// Do the stuffs for the 'propertiesAndValues' here
```
