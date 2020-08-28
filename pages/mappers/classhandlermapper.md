---
layout: navpage
sidebar: mappers
title: "ClassHandlerMapper"
description: "A class that is used to map a class handler into a .NET CLR type. This class is used as an alternative to ClassHandler attribute."
permalink: /mapper/classhandlermapper
tags: [repodb, class, classhandlermapper, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# ClassHandlerMapper

A class that is used to map a class handler into a .NET CLR type. This class is used as an alternative to [ClassHandler](/attribute/classhandler) attribute.

Please see the [IClassHandlerMapper](/interface/iclasshandler) for more details about the property handling implementation.

#### Methods

Below are the methods available from this class.

- `Add` - adds a class handler mapping on a specific .NET CLR type.
- `Clear` - clears all the mappings for the class handlers.
- `Get` - gets the mapped class handler specific to the .NET CLR type.
- `Remove` - removes the mapping of the .NET CLR type into a class handler.

#### Use-Cases

You should use this class if you do not like to use the [ClassHandler](/attribute/classhandler) attribute. Usually, the purpose of the usability is to make sure that the model is attribute-free and is not bound to a specific ORM.

#### How to Use?

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

#### How to Map?

There are 3 ways of doing the map. You can either do the following.

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