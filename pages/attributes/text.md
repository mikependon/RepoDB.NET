---
layout: page
title: "Text (RepoDb)"
permalink: /attribute/text
tags: [repodb, class, text, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

## Text

This attribute is used to decorate a special string value on any class, property, enumeration or interface.

#### How to Use?

You can add this attribute in any class, property, enumeration or interface for whatever purpose.

##### Class

```csharp
[Text("Corresponds to the [dbo].[Person] table at the database.")]
public class Person
{
	...
}
```

##### Property

```csharp
public class Person
{
	[Text("Corresponds to the [dbo].[Person].[Id] column.")]
	public int Id { get; set; }
}
```

##### Enumeration

```csharp
public enum Operation
{
	[Text("=")]
	Equal,
	[Text("<>")]
	NotEqual,
	[Text(">=")]
	GreaterThanOrEqual,
	[Text(">=")]
	LessThanOrEqual
}
```

##### Interface

```csharp
[Text("This is the base processor interface.")]
public interface IProcessor
{
	void Process();
}
```
