---
layout: default
sidebar: attributes
title: "ClassHandler"
nav_order: 1
description: "Learn on how to use the RepoDB ClassHandler attribute."
permalink: /attribute/classhandler
tags: [repodb, classhandler]
parent: ATTRIBUTES
---

# ClassHandler

---

This attribute marks a class for evaluation by the library compiler during serialization and deserialization. When present, the compiler automatically invokes the class handler's `Get()` and `Set()` methods for custom handling.

Given a customized [IClassHandler](/interface/iclasshandler) implementation:

```csharp
private class PersonClassHandler : IClassHandler<Person>
{
    public Person Get(Person entity, ClassHandlerGetOptions options)
    {
        EnsureConvertedProperties(entity);
        return entity;
    }

    public Person Set(Person entity, ClassHandlerSetOptions options)
    {
        ValidatePerson(entity);
        return entity;
    }
}
```

Apply this attribute to the class to use `PersonClassHandler` during transformation.

```csharp
[ClassHandler(typeof(PersonClassHandler))]
public class Person
{
	public long Id { get; set; }
	public string Name { get; set; }
	public int Age { get; set; }
	public Address Address { get; set; }
	public DateTime CreatedDateUtc { get; set; }
}
```