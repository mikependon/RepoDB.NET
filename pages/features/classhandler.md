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

This is a feature that would allow you to handle the event during the serialization/deserialization process of the entity model and the data reader object. It enables you as developer to handle any kind of serialization event within the model, a validation of the serializable data and/or even trigger a workflow during and after the transformation process.

It uses the following objects.

| Name         | Description  | 
|:-------------|:-------------|
| [IClassHandler](/interface/iclasshandler) | an interface to mark your class as class handler. |
| [ClassHandler](/attribute/classhandler) | an attribute used to map a class handler into a specific .NET CLR type. |
| [ClassHandlerMapper](/mapper/classhandlermapper) | a mapper used to map a class handler into a specific .NET CLR type. |
| [FluentMapper](/mapper/fluentmapper) | a fluent mapper class used to map a class handler into a specific .NET CLR type. |

## How does it works?

If you are reading a data from the DB (i.e.: [ExecuteQuery](/operation/executequery), [Query](/operation/query), [BatchQuery](/operation/batchquery)), the `Get()` method will be invoked after deserializing the model. On the other hand, if you are pushing a data towards the DB (i.e.: [Insert](/operation/insert), [Merge](/operation/merge), [Update](/operation/update)), the `Set()` method will be invoked prior the actual DB operation.

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

There are various ways of mapping a class handler into an entity model.

Firstly, via the [ClassHandlerMapper](/mapper/classhandlermapper) class.

```csharp
ClassHandlerMapper
    .Add<Person, PersonClassHandler>(true);
```

Secondly, via the [FluentMapper](/mapper/fluentmapper) class.

```csharp
FluentMapper
    .Entity<Person>()
    .ClassHandler<PersonClassHandler>(true);
```

Lastly, via an explicit [ClassHandler](/attribute/classhandler) attribute.

```csharp
[ClassHandler(typeof(PersonClassHandler))]
publi class Person
{
    ...
}
```
