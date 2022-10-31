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

This is a helper class used for extracting class objects via `Linq.Expressions` namespace. The operations residing on this class are all pre-compiled operations.

## GetEntitiesPropertyValues

This method is used to get the data entities property values. The result is an array of generic type `TResult`.

Let us say you have an array of model `Person`, then the code below would extract all the values of the `Id` property.

```csharp
var people = GetPeople(); // As a given variable
var keys = ClassExpression.GetEntitiesPropertyValues<Person, long>(people, "Id");
// Do the stuffs for the 'keys' here
```

{: .note }
> You can also extract using [Field](/class/field), [DbField](/class/dbfield) or [ClassProperty](/class/classproperty) objects.

## GetProperties

This method is used to get the list of the [ClassProperty](/class/classproperty) objects of the target entity.

```csharp
var properties = ClassExpression.GetProperties<Person>();
// Do the stuffs for the 'properties' here
```

{: .important }
> We recommend that you use the [PropertyCache](/cacher/propertycache) object when extracting the class properties.

## GetPropertiesAndValues

This method is used to extract the list of properties and values of the target entity. The result is an array of [PropertyValue](/class/propertyvalue) object.

```csharp
var propertiesAndValues = ClassExpression.GetProperties<Person>(person);
// Do the stuffs for the 'propertiesAndValues' here
```


