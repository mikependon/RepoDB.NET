---
layout: default
sidebar: enumerations
title: "MySqlConnectorBulkImportIdentityBehavior"
description: "An enumeration that is being used to define the behavior of the identity property/column when an entity is being bulk-imported towards the target table."
permalink: /enumeration/mysqlconnector/mysqlconnectorbulkimportidentitybehavior
tags: [repodb, mysqlconnectorbulkimportidentitybehavior]
parent: "MySqlConnector"
grand_parent: ENUMERATIONS
---

# MySqlConnectorBulkImportIdentityBehavior

---

This enum defines the behavior of the identity property/column when an entity is bulk-imported into a target table. It applies only to [MySqlConnector](https://www.nuget.org/packages/RepoDb.MySqlConnector.BulkOperations).

## Enum Values

| Name | Description |
|:-----|:------------|
| KeepIdentity | The identity column is left out of the write, so the target table's own `AUTO_INCREMENT` generates it as usual. (This is the default value) |
| ReturnIdentity | The newly generated (or, for `BulkMerge`, matched) identity value is set back on the entity. |

## Usability

This enum is used by both the [BulkInsert](/operation/mysqlconnector/bulkinsert) and [BulkMerge](/operation/mysqlconnector/bulkmerge) operations. Pass the value to the `identityBehavior` argument when calling the operation.

For [BulkInsert](/operation/mysqlconnector/bulkinsert):

```csharp
using (var connection = new MySqlConnection(connectionString))
{
    var people = GetPeople(1000);
    var insertedRows = connection.BulkInsert(people,
        identityBehavior: MySqlConnectorBulkImportIdentityBehavior.ReturnIdentity);
}
```

For [BulkMerge](/operation/mysqlconnector/bulkmerge):

```csharp
using (var connection = new MySqlConnection(connectionString))
{
    var people = GetPeople(1000);
    var mergedRows = connection.BulkMerge(people,
        identityBehavior: MySqlConnectorBulkImportIdentityBehavior.ReturnIdentity);
}
```

{: .important }
> Requesting `ReturnIdentity` requires `AllowUserVariables=True` on your MySqlConnector connection string, since the underlying implementation relies on session user variables to pre-assign and read back the generated values. See [Identity Setting Alignment](/operation/mysqlconnector#identity-setting-alignment) for details.
