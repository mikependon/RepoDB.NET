---
layout: default
sidebar: features
title: "Class Handlers"
description: "This is a feature that would allow you to handle the event during the serialization/deserialization process of the Model and the DbDataReader object (inbound/outbound)."
permalink: /feature/classhandlers
tags: [repodb, class, classhandlers, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
parent: Features
---

# Class Handlers

---

This is a feature that would allow you to handle the event during the serialization/deserialization process of the entity model and the `DbDataReader` object (inbound/outbound). With this feature, it enables you as developer to handle any kind of serialization event within the model, validation and/or even trigger a workflow during/after the process of transformation.

Imagine the case of validating the model before pushing to the database, or transforming the necessary properties before sending back to the caller.

The execution of the event contains the actual `DbDataReader` object in used and the affected class model.

Below is the high-level diagram of the class handler.

<img src="../../assets/images/site/classhandler.svg" />

#### It uses the following objects

- [IClassHandler](/interface/iclasshandler) - an interface to mark your class as class handler.
- [ClassHandler](/attribute/classhandler) - an attribute used to map a class handler into a specific .NET CLR type.
- [ClassHandlerMapper](/mapper/classhandlermapper) - a mapper used to map a class handler into a specific .NET CLR type.
- [FluentMapper](/mapper/fluentmapper) - a fluent mapper class used to map a class handler into a specific .NET CLR type.

### How does it works?

If you are reading a data from the DB (i.e.: [ExecuteQuery](/operation/executequery), [Query](/operation/query), [BatchQuery](/operation/batchquery)), the method `Get()` will be invoked after deserializing the model. On the other hand, if you are pushing a data into the DB (i.e.: [Insert](/operation/insert), [Merge](/operation/merge), [Update](/operation/update)), the method `Set()` will be invoked prior the actual DB operation.

### Implementing a Class Handler

Create a class that implements the [IClassHandler](/interface/iclasshandler) interface.

```csharp
public class PersonClassHandler : IClassHandler<Person>
{
    public Person Get(Person entity,
        DbDataReader reader)
    {
        return entity;
    }

    public Person Set(Person entity)
    {
        return entity;
    }
}
```

### Mapping a Class Handler

There are various ways of mapping a class handler into an entity model. You can use either do the following approach.

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
