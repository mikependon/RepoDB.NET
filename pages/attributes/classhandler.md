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

This attribute is used to mark a class to be evaluated by the compilers during the serialization and deserialization process. By having this attribute, the library compiler (transformer) will automatically trigger the class handler `Get()` and `Set()`  method for custom handling.

Let us say you have created a customized [IClassHandler](/interface/iclasshandler) like below.

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

Then, you can use this attribute to mark the class to use the `PersonClassHandler` class handler during the transformation.

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