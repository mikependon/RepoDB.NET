---
layout: default
sidebar: classes
title: "ClassExpression"
description: "A helper class to extract class properties and values."
permalink: /class/classexpression
tags: [repodb, classexpression]
parent: CLASSES
---

# ClassExpression

---

A helper class for extracting class objects via the `Linq.Expressions` namespace. All operations are pre-compiled.

## GetEntitiesPropertyValues

Returns the property values of a collection of entities as an array of the specified `TResult` type.

The following example extracts all `Id` values from an array of `Person` objects.

```csharp
var people = GetPeople(); // As a given variable
var keys = ClassExpression.GetEntitiesPropertyValues<Person, long>(people, "Id");
// Do the stuffs for the 'keys' here
```

{: .note }
> Extraction is also supported via [Field](/class/field), [DbField](/class/dbfield), or [ClassProperty](/class/classproperty) objects.

## GetProperties

Returns a list of [ClassProperty](/class/classproperty) objects for the target entity.

```csharp
var properties = ClassExpression.GetProperties<Person>();
// Do the stuffs for the 'properties' here
```

{: .important }
> Use [PropertyCache](/cacher/propertycache) when extracting class properties, as it caches results for reuse.

## GetPropertiesAndValues

Returns a list of [PropertyValue](/class/propertyvalue) objects containing the properties and their values for the target entity.

```csharp
var propertiesAndValues = ClassExpression.GetProperties<Person>(person);
// Do the stuffs for the 'propertiesAndValues' here
```
