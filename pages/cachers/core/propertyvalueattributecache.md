---
layout: default
sidebar: cachers
title: "PropertyValueAttributeCache"
description: "A class that is being used to retrieve the cached attributes of the property."
permalink: /cacher/propertyvalueattributecache
tags: [repodb, propertyvalueattributecache]
parent: CACHERS
---

# PropertyValueAttributeCache

---

A cacher class for retrieving mapped [PropertyValueAttribute](/attribute/parameter/propertyvalueattribute) objects on a property. It uses [PropertyValueAttributeMapper](/mapper/propertyvalueattributemapper) internally to extract mappings and caches them for future use.

## Methods

Below are the methods available from this class.

| Flush | Allows you to flush the caches. |
| Get | Returns the list of [PropertyValueAttribute](/attribute/parameter/propertyvalueattribute) objects. |

## Usability

Call the `Get()` method by passing the class type and property name:

```csharp
var attributes = PropertyValueAttributeCache.Get(typeof(Person), "Name");
// Use the 'attributes' here
```

Or via a property expression:

```csharp
var attributes = PropertyValueAttributeCache.Get<Person>(e => e.Name);
// Use the 'attributes' here
```