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

A cacher class for the [IPropertyHandler](/interface/ipropertyhandler) objects that has been mapped to .NET CLR type or class property. Underneath, it uses the [PropertyHandlerMapper](/mapper/propertyhandlermapper) class to extract the results and caching it for future use.

## Methods

Below are the methods available from this class.

- `Flush` - allows you to flush the caches.
- `Get` - returns the instance of the property handler.

## Usability

You can simply call the `Get()` method of this class by passing the class type.

#### Type Level

```csharp
var propertyHandler = PropertyHandlerCache.Get<IntTypeProperHandler>(typeof(int));
// Use the 'propertyHandler' here
```

Or via generic.

```csharp
var propertyHandler = PropertyHandlerCache.Get<int, IntTypeProperHandler>();
// Use the 'propertyHandler' here
```

#### Property Level

```csharp
var propertyHandler = PropertyHandlerCache.Get<Customer, CustomerAddressPropertyHandler>("Address");
// Use the 'propertyHandler' here
```

Or via generic.

```csharp
var propertyHandler = PropertyHandlerCache.Get<Customer, CustomerAddressPropertyHandler>(e => e.Address);
// Use the 'propertyHandler' here
```