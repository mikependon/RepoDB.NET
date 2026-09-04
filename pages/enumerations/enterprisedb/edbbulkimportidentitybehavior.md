---
layout: default
sidebar: enumerations
title: "EDBBulkImportIdentityBehavior"
description: "An enumeration that is being used to define the behavior of the identity property/column when an entity is being bulk-imported towards the target table."
permalink: /enumeration/enterprisedb/edbbulkimportidentitybehavior
tags: [repodb, edbbulkimportidentitybehavior]
parent: "EnterpriseDB"
grand_parent: ENUMERATIONS
---

# EDBBulkImportIdentityBehavior

---

This enum defines the behavior of the identity property/column when an entity is bulk-imported into a target table. It applies only to [EnterpriseDB](https://www.nuget.org/packages/RepoDb.EnterpriseDb.BulkOperations).

## Enum Values

| Name | Description |
|:-----|:------------|
| KeepIdentity | The value of the identity property/column will be kept and used. (This is the default value) |
| ReturnIdentity | The newly generated identity value from the target table will be set back to the entity. |

## Usability

This enum is used by both the [BulkInsert](/operation/enterprisedb#bulkinsert) and [BulkMerge](/operation/enterprisedb#bulkmerge) operations of [RepoDb.EnterpriseDb.BulkOperations](/operation/enterprisedb). Pass the value to the `identityBehavior` argument when calling the operation.

For [BulkInsert](/operation/enterprisedb#bulkinsert):

```csharp
using (var connection = new EDBConnection(connectionString))
{
    var people = GetPeople(1000);
    var insertedRows = connection.BulkInsert(people,
        identityBehavior: EDBBulkImportIdentityBehavior.ReturnIdentity);
}
```

For [BulkMerge](/operation/enterprisedb#bulkmerge):

```csharp
using (var connection = new EDBConnection(connectionString))
{
    var people = GetPeople(1000);
    var mergedRows = connection.BulkMerge(people,
        identityBehavior: EDBBulkImportIdentityBehavior.ReturnIdentity);
}
```

{: .note }
> The `DbDataReader` overloads of [BulkInsert](/operation/enterprisedb#bulkinsert) and [BulkMerge](/operation/enterprisedb#bulkmerge) have no `identityBehavior` argument — a forward-only, single-pass reader cannot be rewound to correlate generated identity values back onto a source row.
