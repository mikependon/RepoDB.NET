---
layout: page
title: "PropertyTypeHandlerMapper (RepoDb)"
permalink: /mapper/propertytypehandlermapper
tags: [repodb, class, propertytypehandlermapper, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

## PropertyTypeHandlerMapper

A class that is used to map a property handler into a .NET CLR Type. Please see the [IPropertyHandler](/interface/ipropertyhandler) for more details about the property handling implementation.

#### Methods

Below are the methods available from this class.

- `Add` - adds a property handler mapping on a specific .NET CLR Type.
- `Get` - gets the mapped property handler specific to the .NET CLR Type.
- `Remove` - removes the mapping of the .NET CLR Type into a property handler.

#### Use-Cases

You should use this class if you would like to add a property handler mapping on a specific .NET CLR type.

A common use-case is to handle all the `DateTime` objects and correcting the `Kind` property.

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

To add a mapping, simply call the `Add` method.

```csharp
PropertyTypeHandlerMapper.Add(typeof(DateTime), new DateTimeKindToUtcPropertyHandler(), true);
```

> An exception will be be thrown if the mapping is already exists and you passed a `FALSE` value in the `override` argument.

To get the mapping, use the `Get` method.

```csharp
var propertyHandler = PropertyTypeHandlerMapper.Get<DateTimeKindToUtcPropertyHandler>(typeof(DateTime));
```

To remove the mapping, use the `Remove` method.

```csharp
var isRemoved = PropertyTypeHandlerMapper.Remove(typeof(DateTime), false);
```

> An exception will be be thrown if the mapping is not existing and you passed a `TRUE` value in the `throwException` argument.