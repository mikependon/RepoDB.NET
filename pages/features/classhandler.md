---
layout: default
sidebar: features
title: "Class Handlers"
description: "This is a feature that would allow you to handle the event during the serialization/deserialization process of the Model and the DbDataReader object (inbound/outbound)."
permalink: /feature/classhandlers
tags: [repodb, classhandlers]
parent: FEATURES
---

# Class Handlers

---

This feature allows you to intercept the serialization and deserialization process of an entity model and the data reader object. It enables you to handle serialization events, validate serializable data, or trigger workflows during and after the transformation process.

It uses the following objects:

| Name         | Description  | 
|:-------------|:-------------|
| [IClassHandler](/interface/iclasshandler) | An interface to mark your class as a class handler. |
| [ClassHandler](/attribute/classhandler) | An attribute used to map a class handler to a specific .NET CLR type. |
| [ClassHandlerMapper](/mapper/classhandlermapper) | A mapper used to map a class handler to a specific .NET CLR type. |
| [FluentMapper](/mapper/fluentmapper) | A fluent mapper class used to map a class handler to a specific .NET CLR type. |

## How does it works?

When reading data from the database (e.g., [ExecuteQuery](/operation/executequery), [Query](/operation/query), [BatchQuery](/operation/batchquery)), the `Get()` method is invoked after deserializing the model. When pushing data to the database (e.g., [Insert](/operation/insert), [Merge](/operation/merge), [Update](/operation/update)), the `Set()` method is invoked before the actual database operation.

## Implementing a Class Handler

Create a class that implements the [IClassHandler](/interface/iclasshandler) interface.

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

## Mapping a Class Handler

There are several ways to map a class handler to an entity model.

Via the [ClassHandlerMapper](/mapper/classhandlermapper) class:

```csharp
ClassHandlerMapper
    .Add<Person, PersonClassHandler>(true);
```

Via the [FluentMapper](/mapper/fluentmapper) class:

```csharp
FluentMapper
    .Entity<Person>()
    .ClassHandler<PersonClassHandler>(true);
```

Via the [ClassHandler](/attribute/classhandler) attribute:

```csharp
[ClassHandler(typeof(PersonClassHandler))]
publi class Person
{
    ...
}
```
