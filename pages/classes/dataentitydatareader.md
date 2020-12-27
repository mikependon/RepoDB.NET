---
layout: default
sidebar: classes
title: "DataEntityDataReader"
description: "A customized data reader class that is used to handle the list of data entity objects."
permalink: /class/dataentitydatareader
tags: [repodb, class, dataentitydatareader, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
parent: Classes
---

# DataEntityDataReader

---

This class is used to convert the `IEnumerable<T>` object into a `DbDataReader` object.

### Learnings

Let us say you have a method named `GetPeople()` that creates an enumerable of `Person` model.

```csharp
private void IEnumerable<Person> GetPeople(int count = 10000)
{
    for (var i = 0; i < count; i++)
    {
        yield return new Person
        {
            Name = $"Person-{i}",
            SSN = Guid.NewGuid.ToString(),
            DateInsertedUtc = DateTime.UtcNow
        };
    }
}
```

Then, you can extract it to be a `DbDataReader` object via this class.

```csharp
var people = GetPeople();
using (var reader = new DataEntityDataReader<Person>(people))
{
    // Do the stuffs here
}
```

Then, you can use it like a normal data reader.

```csharp
while (reader.Read())
{
    // Extract the properties here
}
```

Or use it in the bulk operations.

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var people = GetPeople();
    using (var reader = new DataEntityDataReader<Person>(people))
    {
        var insertedRows = connection.BulkInsert("[dbo].[Person]", reader);
    }
}
```

> This class is useful if you are tying to use the bulk operations (i.e.: [BulkDelete](/operation/bulkdelete), [BulkInsert](/operation/bulkinsert), [BulkMerge](/operation/bulkmerge) and [BulkUpdate](/operation/bulkupdate)) with `DbDataReader` object sourced by `IEnumerable<T>`.
