---
layout: default
sidebar: enumerations
title: "MySqlBulkImportIdentityBehavior"
description: "An enumeration that is being used to define the behavior of the identity property/column when an entity is being bulk-imported towards the target table."
permalink: /enumeration/mysql/mysqlbulkimportidentitybehavior
tags: [repodb, mysqlbulkimportidentitybehavior, mysql]
parent: "MySql"
grand_parent: ENUMERATIONS
---

# MySqlBulkImportIdentityBehavior

---

This enum defines the behavior of the identity property/column when an entity is bulk-imported into a target table. It applies only to [RepoDb.MySql.BulkOperations](https://www.nuget.org/packages/RepoDb.MySql.BulkOperations) (the `MySql.Data`-based driver).

## Enum Values

| Name | Description |
|:-----|:------------|
| KeepIdentity | The identity column is left out of the write, so the target table's own `AUTO_INCREMENT` generates it as usual. (This is the default value) |
| ReturnIdentity | The newly generated (or, for `BulkMerge`, matched) identity value is set back on the entity. |

## Usability

This enum is used by both the [BulkInsert](/operation/mysql/bulkinsert) and [BulkMerge](/operation/mysql/bulkmerge) operations. Pass the value to the `identityBehavior` argument when calling the operation.

For [BulkInsert](/operation/mysql/bulkinsert):

```csharp
using (var connection = new MySqlConnection(connectionString))
{
    var people = GetPeople(1000);
    var insertedRows = connection.BulkInsert(people,
        identityBehavior: MySqlBulkImportIdentityBehavior.ReturnIdentity);
}
```

For [BulkMerge](/operation/mysql/bulkmerge):

```csharp
using (var connection = new MySqlConnection(connectionString))
{
    var people = GetPeople(1000);
    var mergedRows = connection.BulkMerge(people,
        identityBehavior: MySqlBulkImportIdentityBehavior.ReturnIdentity);
}
```

{: .important }
> Requesting `ReturnIdentity` requires `AllowUserVariables=True` on your connection string, since the underlying implementation relies on session user variables to pre-assign and read back the generated values. See [Identity Setting Alignment](/operation/mysql#identity-setting-alignment) for details.
