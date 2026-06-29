---
layout: default
sidebar: cachers
title: "DbFieldCache"
description: "A class that is being used to retrieve the cached database fields of the class or data entity."
permalink: /cacher/dbfieldcache
tags: [repodb, dbfieldcache]
parent: CACHERS
---

# DbFieldCache

---

A cacher class for database table fields. It provides a second caching layer for field extraction, allowing the library to reuse previously extracted database fields for faster execution.

## Methods

Below is the list of methods.

| Name | Description |
|:-----|:------------|
| Flush | Allows you to flush the caches. |
| Get | Returns the list of [DbField](/class/dbfield) objects. |

## Usability

Call the `Get()` method by passing the literal table name:

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var dbFields = DbFieldCache.Get("[dbo].[Person]", connection, transaction: null);
    // Use the 'dbFields' here
}
```

Or use [ClassMappedNameCache](/cacher/classmappednamecache) to resolve the mapped entity name:

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var dbFields = DbFieldCache.Get(ClassMappedNameCache.Get<Person>(), connection, transaction: null);
    // Use the 'dbFields' here
}
```

{: .note }
> The `Get()` method requires a `DbConnection`. If the cache is empty, fields are extracted from the database, cached, and returned to the caller.