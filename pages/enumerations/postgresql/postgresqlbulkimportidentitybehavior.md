---
layout: default
sidebar: enumerations
title: "PostgreSqlBulkImportIdentityBehavior"
description: "An enumeration that is being used to define the behavior of the identity property/column when an entity is being bulk-imported towards the target table."
permalink: /enumeration/postgresql/postgresqlbulkimportidentitybehavior
tags: [repodb, postgresqlbulkimportidentitybehavior]
parent: "PostgreSQL"
grand_parent: ENUMERATIONS
---

# PostgreSqlBulkImportIdentityBehavior

---

{: .warning }
> This enum was previously named `BulkImportIdentityBehavior`. That name is now deprecated in favor of `PostgreSqlBulkImportIdentityBehavior` and will be tagged starting v1.16.0 of the `RepoDb.PostgreSql` and `RepoDb.PostgreSql.BulkOperations` packages.

This enum defines the behavior of the identity property/column when an entity is bulk-imported into a target table. It applies only to the [PostgreSQL](https://www.nuget.org/packages/RepoDb.PostgreSql.BulkOperations) RDBMS.

## Enum Values

| Name | Description |
|:-----|:------------|
| KeepIdentity | A value that indicates whether the value of the identity property/column will be kept and used. |
| ReturnIdentity | A value that indicates whether the newly generated identity value from the target table will be set back to the entity. |
| Unspecified | No action is required. (This is the default value) |

## Usability

This enum is used by both the [BulkInsert](/operation/postgresql/bulkinsert) and [BulkMerge](/operation/postgresql/bulkmerge) operations. Pass the value to the `identityBehavior` argument when calling the operation.

For the [BulkInsert](/operation/postgresql/bulkinsert) operation:

```csharp
using (var connection = new NpgsqlConnection(connectionString))
{
    var people = GetPeople(1000);
    var insertedRows = connection.BulkInsert(people,
        identityBehavior: PostgreSqlBulkImportIdentityBehavior.ReturnIdentity);
}
```

For the [BulkMerge](/operation/postgresql/bulkmerge) operation:

```csharp
using (var connection = new NpgsqlConnection(connectionString))
{
    var people = GetPeople(1000);
    var mergedRows = connection.BulkMerge(people,
        identityBehavior: PostgreSqlBulkImportIdentityBehavior.ReturnIdentity);
}
```
