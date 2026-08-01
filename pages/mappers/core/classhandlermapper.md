---
layout: default
sidebar: mappers
title: "ClassHandlerMapper"
description: "A class that is being used to map a class handler into a class. This class is used as an alternative to ClassHandler attribute."
permalink: /mapper/classhandlermapper
tags: [repodb, classhandlermapper]
parent: MAPPERS
---

# ClassHandlerMapper

---

A mapper class for associating a class handler with a target class. This is the programmatic alternative to the [ClassHandler](/attribute/classhandler) attribute.

See [IClassHandlerMapper](/interface/iclasshandler) for details on the property handling implementation.

## Methods

Below is the list of methods.

| Name | Description |
|:-----|:------------|
| Add | Adds a class handler mapping on a specific class. |
| Clear | Clears all the existing property handler mappings. |
| Get | Gets the existing mapped class handler object on the class. |
| Remove | Removes the existing mapped class handler of the class. |

## Usability

Given a `Person` class handler implementation:

```csharp
public class PersonClassHandler : IClassHandler<Person>
{
    public Person Get(Person entity, ClassHandlerGetOptions options)
    {
        return entity;
    }

    public Person Set(Person entity, ClassHandlerSetOptions options)
    {
        return entity;
    }
}
```

## How to Map?

A class handler can be mapped to an entity model using any of the following approaches.

Via [ClassHandlerMapper](/mapper/classhandlermapper):

```csharp
PropertyHandlerMapper
    .Add(typeof(Person), new PersonClassHandler(), true);
```

Via [FluentMapper](/mapper/fluentmapper):

```csharp
FluentMapper
    .Entity<Person>()
    .ClassHandler<PersonClassHandler>();
```

Via the [ClassHandler](/attribute/classhandler) attribute:

```csharp
[ClassHandler(typeof(PersonClassHandler))]
publi class Person
{
    ...
}
```