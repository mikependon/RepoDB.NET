---
layout: navpage
sidebar: mappers
title: "PropertyHandlerMapper (RepoDb)"
permalink: /mapper/propertyhandlermapper
tags: [repodb, class, propertyhandlermapper, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

# PropertyHandlerMapper

A class that is used to map a property handler into a .NET CLR type or a class property. This class is used as an alternative to [PropertyHandler](/attribute/propertyhandler) attribute.

Please see the [IPropertyHandler](/interface/ipropertyhandler) for more details about the property handling implementation.

This class is a replacement of `PropertyHandlerMapper` class.

#### Methods

Below are the methods available from this class.

- `Add` - adds a property handler mapping on a specific .NET CLR type.
- `Clear` - clears all the mappings for the property handlers.
- `Get` - gets the mapped property handler specific to the .NET CLR type.
- `Remove` - removes the mapping of the .NET CLR type into a property handler.

#### Use-Cases

You should use this class if you do not like to use the [PropertyHandler](/attribute/propertyhandler) attribute. Usually, the purpose of the usability is to make sure that the model is attribute-free and is not bound to a specific ORM.

#### How to Use?

Let us say you implemented a `DateTime` handler like below.

```csharp
public class DateTimeKindToUtcPropertyHandler : IPropertyHandler<DateTime?, DateTime?>
{
    public DateTime? Get(DateTime? input, ClassProperty property)
    {
        return DateTime.SpecifyKind(input, DateTimeKind.Utc);
    }

    public DateTime? Set(DateTime? input, ClassProperty property)
    {
        return DateTime.SpecifyKind(input.GetValueOrDefault(), DateTimeKind.Unspecified);
    }
}
```

###### Property Level Mapping

To add a property level mapping, simply call the `Add` method and pass the target property and the instance of the property handler.

```csharp
PropertyHandlerMapper.Add<Customer, DateTimeKindToUtcPropertyHandler>(e => e.DateOfBirth, new DateTimeKindToUtcPropertyHandler(), true);
```

> An exception will be thrown if the mapping is already exists and you passed a `false` value in the `force` argument.

To get the mapping, use the `Get` method.

```csharp
var propertyHandler = PropertyHandlerMapper.Get<Customer, DateTimeKindToUtcPropertyHandler>(e => e.DateOfBirth);
```

To remove the mapping, use the `Remove` method.

```csharp
PropertyHandlerMapper.Remove<Customer, DateTimeKindToUtcPropertyHandler>(e => e.DateOfBirth);
```

###### Type Level Mapping

To add a class level mapping, simply call the type-level `Add` method and pass the target .NET CLR type and the instance of property handler.

```csharp
PropertyHandlerMapper.Add<DateTime, DateTimeKindToUtcPropertyHandler>(new DateTimeKindToUtcPropertyHandler(), true);
```

> An exception will be thrown if the mapping is already exists and you passed a `false` value in the `force` argument.

To get the mapping, use the type-level `Get` method.

```csharp
var propertyHandler = PropertyHandlerMapper.Get<DateTime, DateTimeKindToUtcPropertyHandler>();
```

To remove the mapping, use the type-level `Remove` method.

```csharp
PropertyHandlerMapper.Remove<DateTime>();
```