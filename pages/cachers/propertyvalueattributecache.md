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

A cacher class that is being used to retrieve the existing mapped [PropertyValueAttribute](/attribute/propertyvalueattribute) objects of a property. Underneath, it uses the [PropertyValueAttributeMapper](/mapper/propertyvalueattributemapper) class to extract the mappings and caching it for future use.

### Methods

Below are the methods available from this class.

- `Flush` - allows you to flush the caches.
- `Get` - returns the list of [PropertyValueAttribute](/attribute/propertyvalueattribute) objects.

### Usability

You can simply call the `Get()` method of this class by passing the type of the class and the name of the property.

```csharp
var attributes = PropertyValueAttributeCache.Get(typeof(Person), "Name", includeMappings: true);
// Use the 'attributes' here
```

Or, via a property expression.

```csharp
var attributes = PropertyValueAttributeCache.Get<Person>(e => e.Name, includeMappings: true);
// Use the 'attributes' here
```

> The argument `includeMappings` is used if the existing explicit mappings will be included in the extractions. By default, the value is `false`.