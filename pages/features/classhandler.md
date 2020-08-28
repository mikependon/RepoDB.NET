---
layout: navpage
sidebar: features
title: "Class Handlers"
description: "This is a feature that would allow you to handle the event during the serialization/deserialization process of the Model and the DbDataReader object (inbound/outbound)."
permalink: /feature/classhandlers
tags: [repodb, class, classhandlers, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# Class Handlers

This is a feature that would allow you to handle the event during the serialization/deserialization process of the `Model` and the `DbDataReader` object (inbound/outbound). It allows you to handle the event, validate the model or trigger a workflow after the process of transformation.

Imagine the case of validating the model before pushing to the database and/or transforming the necessary properties before sendind back the model to the caller.

The execution of the event contains the actual `DbDataReader` in used and the affected class model.

###### It uses the following objects

- [IClassHandler](/interface/iclasshandler) - an interface to mark your class as class handler.
- [ClassHandler](/attribute/classhandler) - an attribute used to map a class handler into a specific .NET CLR type.
- [ClassHandlerMapper](/mapper/classhandlermapper) - a mapper used to map a class handler into a specific .NET CLR type.
- [FluentMapper](/mapper/fluentmapper) - a fluent mapper class used to map a class handler into a specific .NET CLR type.

#### Implementing a Class Handler

Create a class that implements the [IClassHandler](/interface/iclasshandler) interface.

```csharp
public class PersonClassHandler : IClassHandler<Person>
{
    public Person Get(Person entity, DbDataReader reader)
    {
        return entity;
    }

    public Person Set(Person entity)
    {
        return entity;
    }
}
```

#### Mapping a Class Handler

There are various ways of mapping a class handler into an entity model. You can use either do the following approach.

Firstly, via the [ClassHandlerMapper](/mapper/classhandlermapper) class.

```csharp
PropertyHandlerMapper
    .Add(typeof(Person), new PersonClassHandler(), true);
```

Secondly, via the [FluentMapper](/mapper/fluentmapper) class.

```csharp
FluentMapper
    .Entity<Person>()
    .ClassHandler<PersonClassHandler>();
```

Lastly, via an explicit [ClassHandler](/attribute/classhandler) attribute.

```csharp
[ClassHandler(typeof(PersonClassHandler))]
publi class Person
{
    ...
}
```