---
layout: default
sidebar: mappers
title: "ClassHandlerMapper"
description: "A class that is used to map a class handler into a class. This class is used as an alternative to ClassHandler attribute."
permalink: /mapper/classhandlermapper
tags: [repodb, class, classhandlermapper, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
parent: MAPPERS
---

# ClassHandlerMapper

---

A class that is used to map a class handler into a class. This class is used as an alternative to the [ClassHandler](/attribute/classhandler) attribute.

Please see the [IClassHandlerMapper](/interface/iclasshandler) for more details about the property handling implementation.

### Methods

Below are the methods available from this class.

- `Add` - adds a class handler mapping on a specific class.
- `Clear` - clears all the existing property handler mappings.
- `Get` - gets the existing mapped class handler object on the class.
- `Remove` - removes the existing mapped class handler of the class.

### Usability

Let us say you had implemented a `Person` handler like below.

```csharp
public class PersonClassHandler : IClassHandler<Person>
{
    public Person Get(Person entity, DbDataReader dataReader)
    {
        return entity;
    }

    public Person Set(Person entity)
    {
        return entity;
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