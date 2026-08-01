---
layout: default
sidebar: cachers
title: "PropertyHandlerCache"
description: "A class that is being used to retrieve the cached property handler of the class or data entity property."
permalink: /cacher/propertyhandlercache
tags: [repodb, propertyhandlercache]
parent: CACHERS
---

# PropertyHandlerCache

---

A cacher class for [IPropertyHandler](/interface/ipropertyhandler) objects mapped to .NET CLR types or class properties. It uses [PropertyHandlerMapper](/mapper/propertyhandlermapper) internally to extract results and caches them for future use.

## Methods

Below are the methods available from this class.

| Flush | Allows you to flush the caches. |
| Get | Returns the instance of the property handler. |

## Usability

Call the `Get()` method by passing the class type.

#### Type Level

```csharp
var propertyHandler = PropertyHandlerCache.Get<IntTypeProperHandler>(typeof(int));
// Use the 'propertyHandler' here
```

Or via generic type:

```csharp
var propertyHandler = PropertyHandlerCache.Get<int, IntTypeProperHandler>();
// Use the 'propertyHandler' here
```

#### Property Level

```csharp
var propertyHandler = PropertyHandlerCache.Get<Customer, CustomerAddressPropertyHandler>("Address");
// Use the 'propertyHandler' here
```

Or via expression:

```csharp
var propertyHandler = PropertyHandlerCache.Get<Customer, CustomerAddressPropertyHandler>(e => e.Address);
// Use the 'propertyHandler' here
```