---
layout: navpage
sidebar: classes
title: "PropertyValue"
description: "A class that holds the value of class or data entity property and its values."
permalink: /class/propertyvalue
tags: [repodb, class, propertyvalue, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# PropertyValue

This class is used to handle the `Name` and `Value` of the class property. It also handles an instance of [ClassProperty](/class/classproperty).

> Internally, the library is using this class as a parameter before executing any operation.

#### Use Case

- If you are to manually handle the properties and values of the data entity.
- It is used for fast extraction of the data entities property and values via [ClassExpression](/class/classexpression).

#### How to Use?

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