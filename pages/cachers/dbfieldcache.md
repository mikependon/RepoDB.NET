---
layout: page
title: "DbFieldCache (RepoDb)"
permalink: /cacher/dbfieldcache
tags: [repodb, class, dbfieldcache, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

## DbFieldCache

A cacher class for the database table fields. It provides a 2nd-layer caching for the library when it comes to database fields extraction. As a result, the library is fast-enough when reusing the already extracted database fields on any execution.

#### Methods

Below are the methods available from this class.

- `Flush` - allows you to flush the caches.
- `Get` - returns the list of [DbField](/class/dbfield) objects.

#### Use-Cases

You should use this class if you would like to get the references of the database fields by tablename, or you would like to extract the equivalent database fields of the class model.

#### How to Use?

You can simply call the `Get()` method of this class passing the literal table name.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var dbFields = DbFieldCache.Get("[dbo].[Person]", connection, transaction: null);
    // Use the 'dbFields' here
}
```

Or, you can use the [ClassMappedNameCache](/cacher/classmappednamecache) to extract the mapped entity name.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var dbFields = DbFieldCache.Get(ClassMappedNameCache.Get<Person>(), connection, transaction: null);
    // Use the 'dbFields' here
}
```

> By using the `Get()` method, a `DbConnection` is necessary. If the cache is not yet present, then the entries will be extracted from the database, cache it and sent it back to the caller.