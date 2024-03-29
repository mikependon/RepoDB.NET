---
layout: default
sidebar: interfaces
title: "IExpirable"
permalink: /interface/iexpirable
tags: [repodb, icache]
parent: INTERFACES
---

# IExpirable

---

This interface is used to make a class expirable in a contextual point of view. Is is used by [CacheItem](/class/cacheitem) class.

## Methods

Below is the list of methods.

| Name | Description |
|:-----|:------------|
| IsExpired | Identifies whether this class is expired. |

## Properties

Below is the list of properties.

| Name | Description |
|:-----|:------------|
| CreatedDate | The creation date. |
| Expiration | The expiration date. |

## Use-Cases

You can use this interface if you wish to manage the expiration lifespan of your class. The implementation must be handled manually (depends on the purpose of the usage).

## How to Implement?

You have to manually create a class that implements this interface.

```csharp
public class CacheItem : IExpirable
{
    public CacheItem(string key,
        object value,
        int expirationInMinutes)
    {
        CreatedDate = DateTime.UtcNow;
        Expiration = CreatedDate.AddMinutes(expirationInMinutes);
    }

    // IExpirable

    public DateTime CreatedDate { get; }

    public DateTime Expiration { get; }

    public bool IsExpired() => Expiration >= DateTime.UtcNow;
}
```