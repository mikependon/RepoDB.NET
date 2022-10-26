---
layout: default
sidebar: interfaces
title: "IClassHandler"
permalink: /interface/iclasshandler
tags: [repodb, iclasshandler]
parent: INTERFACES
---

# IClassHandler

---

This interface is used to mark a class to be a class handler object. This interface has `TEntity` generic types in which being used at both the `Get()` and `Set()`  methods.

### Generic Types

- `TEntity` - refers to the type of the entity type. This type is used as the input to and result type of the `Get()` and `Set()`  methods.

### Methods

- `Get` - the method that is being invoked when the outbound execution is triggered (i.e.: [Query](/operation/query), [QueryAll](/operation/queryall) and [BatchQuery](/operation/batchquery)).
- `Set`  - the method that is being invoked when the inbound execution is triggered (i.e.: [Insert](/operation/insert), [Update](/operation/update), [Merge](/operation/merge) and etc).

> The `Get()` method has an additional argument of type  `DbDataReader`. It refers to the actual instance of the `DbDataReader` in used during the deserialization process.

### How to Implement?

You have to manually create a class that implements this interface.

```csharp
public class PersonClassHandler : IClassHandler<Person>
{
    public Person Get(Person input, ClassHandlerGetOptions options)
    {
        // Handle the Class before sending back to the caller
    }

    public string Set(Person input, ClassHandlerSetOptions options)
    {
        // Handle the Class before sending to DB
    }
}
```

### How to Map?

There are various ways of mapping a class handler into an entity model. You can use either do the following approach.

Via the [ClassHandlerMapper](/mapper/classhandlermapper) class.

```csharp
PropertyHandlerMapper
    .Add(typeof(Person), new PersonClassHandler(), true);
```

Or, via the [FluentMapper](/mapper/fluentmapper) class.

```csharp
FluentMapper
    .Entity<Person>()
    .ClassHandler<PersonClassHandler>();
```

Or, via an explicit [ClassHandler](/attribute/classhandler) attribute.

```csharp
[ClassHandler(typeof(PersonClassHandler))]
publi class Person
{
    ...
}
```


