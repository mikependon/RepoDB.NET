---
layout: default
sidebar: enumerations
title: "MariaDbBulkImportIdentityBehavior"
description: "An enumeration that is being used to define the behavior of the identity property/column when an entity is being bulk-imported towards the target table."
permalink: /enumeration/mariadb/mariadbconnector/mariadbbulkimportidentitybehavior
tags: [repodb, mariadbbulkimportidentitybehavior, mariadbconnector]
parent: "MariaDbConnector"
grand_parent: ENUMERATIONS
---

# MariaDbBulkImportIdentityBehavior

---

This enum defines the behavior of the identity property/column when an entity is bulk-imported into a target table. It applies only to [RepoDb.MariaDbConnector.BulkOperations](https://www.nuget.org/packages/RepoDb.MariaDbConnector.BulkOperations) (the `MySqlConnector`-based driver).

## Enum Values

| Name | Description |
|:-----|:------------|
| KeepIdentity | The identity column is left out of the write, so the target table's own `AUTO_INCREMENT` generates it as usual. (This is the default value) |
| ReturnIdentity | The newly generated (or, for `BulkMerge`, matched) identity value is set back on the entity. |

## Usability

This enum is used by both the [BulkInsert](/operation/mariadbconnector/bulkinsert) and [BulkMerge](/operation/mariadbconnector/bulkmerge) operations. Pass the value to the `identityBehavior` argument when calling the operation.

For [BulkInsert](/operation/mariadbconnector/bulkinsert):

```csharp
using (var connection = new MariaDbConnection(connectionString))
{
    var people = GetPeople(1000);
    var insertedRows = connection.BulkInsert(people,
        identityBehavior: MariaDbBulkImportIdentityBehavior.ReturnIdentity);
}
```

For [BulkMerge](/operation/mariadbconnector/bulkmerge):

```csharp
using (var connection = new MariaDbConnection(connectionString))
{
    var people = GetPeople(1000);
    var mergedRows = connection.BulkMerge(people,
        identityBehavior: MariaDbBulkImportIdentityBehavior.ReturnIdentity);
}
```

{: .important }
> Requesting `ReturnIdentity` requires `AllowUserVariables=True` on your MariaDbConnector connection string, since the underlying implementation relies on session user variables to pre-assign and read back the generated values. See [Identity Setting Alignment](/operation/mariadbconnector#identity-setting-alignment) for details.
