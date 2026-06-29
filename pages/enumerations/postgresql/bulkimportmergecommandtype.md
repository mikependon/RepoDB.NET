---
layout: default
sidebar: enumerations
title: "BulkImportMergeCommandType"
description: "A enumeration that is being used to define the command text to use when executing the BinaryBulkMerge operation."
permalink: /enumeration/postgresql/bulkimportmergecommandtype
tags: [repodb, bulkimportmergecommandtype]
parent: "PostgreSQL"
grand_parent: ENUMERATIONS
---

# BulkImportMergeCommandType

---

This enum defines the command used when executing the [BinaryBulkMerge](/operation/binarybulkmerge) operation. It applies only to the [PostgreSQL](https://www.nuget.org/packages/RepoDb.PostgreSql.BulkOperations) RDBMS.

## Enum Values

| Name | Description |
|:-----|:------------|
| InsertAndUpdate | An explicit INSERT and UPDATE commands will be used during the operation. It is the legacy UPSERT operation. (This is the default value) |
| OnConflictDoUpdate | An existing ON CONFLICT DO UPDATE command will be used during the operation. By using this value, it  requires that every entity only targets a single row from the underlying table, otherwise, an exception will be thrown. To ensure that the operation is targetting the correct row, we highly recommend to always pass the value of the primary column from your entities. If the 'qualifiers' argument is used, ensures that every qualifier field being in-used is present from the table indexes, otherwise, a unique or exclusion constraint exception will be thrown. (The exceptions thrown are default from PostgreSQL) |

## Usability

To merge a large dataset using the PostgreSQL `ON CONFLICT DO UPDATE` command, pass `OnConflictDoUpdate` to the `mergeCommandType` argument.

```csharp
using (var connection = new NpgsqlConnection(connectionString))
{
    var people = GetPeople(1000);
    var insertedRows = connection.BinaryBulkInsert(people,
        mergeCommandType: BulkImportMergeCommandType.OnConflictDoUpdate);
}
```

{: .important }
> By default, the `InsertAndUpdate` is used. An explicit calls to the UPDATE and INSERT commands of the underlying database (PostgreSQL) are used when merging the information towards the target table. The execution is under a single transaction context.
