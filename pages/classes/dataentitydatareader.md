---
layout: default
sidebar: classes
title: "DataEntityDataReader"
description: "A customized data reader class that is being used to handle the list of data entity objects."
permalink: /class/dataentitydatareader
tags: [repodb, dataentitydatareader]
parent: CLASSES
---

# DataEntityDataReader

---

Converts an [IEnumerable](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.ienumerable-1?view=net-7.0) into a [DbDataReader](https://learn.microsoft.com/en-us/dotnet/api/system.data.common.dbdatareader?view=net-6.0).

Given a method that returns an enumerable of `Person`:

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

Convert it to a [DbDataReader](https://learn.microsoft.com/en-us/dotnet/api/system.data.common.dbdatareader?view=net-6.0):

```csharp
var people = GetPeople();
using (var reader = new DataEntityDataReader<Person>(people))
{
    // Do the stuffs here
}
```

Use it like a standard data reader:

```csharp
while (reader.Read())
{
    // Extract the properties here
}
```

Or pass it to bulk operations:

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

{: .note }
> This class is useful when performing bulk operations ([BulkDelete](/operation/bulkdelete), [BulkInsert](/operation/bulkinsert), [BulkMerge](/operation/bulkmerge), [BulkUpdate](/operation/bulkupdate)) with a [DbDataReader](https://learn.microsoft.com/en-us/dotnet/api/system.data.common.dbdatareader?view=net-6.0) sourced from an [IEnumerable](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.ienumerable-1?view=net-7.0).
