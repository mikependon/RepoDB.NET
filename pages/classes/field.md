---
layout: default
sidebar: classes
title: "Field"
description: "A class that defines a context of field object."
permalink: /class/field
tags: [repodb, class, field, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
parent: Classes
---

# Field

---

This is the class that is used to define a field in many ways. This class is widely use within the library or even hugely use during the actual development.

### Use-Cases

- It is used to define a field to be queried during the [Query](/operation/query) and [BatchQuery](/operation/batchquery) operations.
- It is used to define a field to be affected during push operations like the [Insert](/operation/insert), [Merge](/operation/merge) and [Update](/operation/update) operations.
- It is used to define a qualifier field on the [Merge](/operation/merge), [Update](/operation/update) and etc.
- It is used to define an argument that defines a field information.
- It is used as an output in many of the extension methods within the library. A method of like `ClassProperty.AsField()` as an example.

### Creating an Instance

```csharp
var field = new Field("Id");
```

Or by passing a type.

```csharp
var field = new Field("Id", typeof(string));
```

Or via a static method named `From()`.

```csharp
var fields = Field.From("Id", "Name", "CreatedDateUtc");
```

### Parse Entity

You can also extract an array of field by extracting an entity.

```csharp
var fields = Field.Parse<Person>();
```

Or by type.

```csharp
var fields = Field.Parse(typeof(Person));
```

### Parse Expression

You can also extract by parsing an expression.

```csharp
var fields = Field.Parse<Person>(e => e.Id);
```

Or by parsing the multiple properties.

```csharp
var fields = Field.Parse<Person>(e => new { e.Id, e.Name, e.DateOfBirth });
```

### Parse Object

You can also extract by parsing a class or an anonymous object.

```csharp
var person = new Person();
var fields = Field.Parse(person);
```

> You can use this object anytime anywhere as long the context of the usage defines the field.
