---
layout: default
sidebar: classes
title: "Field"
description: "A class that defines a context of field object."
permalink: /class/field
tags: [repodb, field]
parent: CLASSES
---

# Field

---

This class defines a field and is used extensively throughout the library and in application code.

## Use-Cases

- Specifying fields to query in [Query](/operation/query) and [BatchQuery](/operation/batchquery) operations.
- Specifying fields affected in push operations such as [Insert](/operation/insert), [Merge](/operation/merge), and [Update](/operation/update).
- Defining qualifier fields in [Merge](/operation/merge), [Update](/operation/update), and similar operations.
- Defining field arguments.
- Returned by extension methods such as `ClassProperty.AsField()`.

## Creating an Instance

```csharp
var field = new Field("Id");
```

Or with a type.

```csharp
var field = new Field("Id", typeof(string));
```

Or via the static `From()` method.

```csharp
var fields = Field.From("Id", "Name", "CreatedDateUtc");
```

## Parse Entity

Extract fields from an entity type.

```csharp
var fields = Field.Parse<Person>();
```

Or by type reference.

```csharp
var fields = Field.Parse(typeof(Person));
```

## Parse Expression

Extract fields from a lambda expression.

```csharp
var fields = Field.Parse<Person>(e => e.Id);
```

Or from multiple properties.

```csharp
var fields = Field.Parse<Person>(e => new { e.Id, e.Name, e.DateOfBirth });
```

## Parse Object

Extract fields from a class or anonymous object.

```csharp
var person = new Person();
var fields = Field.Parse(person);
```

{: .note }
> This object can be used anywhere a field definition is required.
