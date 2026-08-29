---
layout: default
sidebar: features
title: "Bulk Operations"
description: "It is the process of bringing all the data from the application into the database server at once, and at the same time, ignoring some database specific activities behind the scene. Thus gives you maximum performance during the operation."
permalink: /feature/bulkoperations
tags: [repodb, bulk, bulk-operations]
parent: FEATURES
---

# Bulk Operations

---

A bulk operation is a process of bringing all the data from the application into the database server at once. It ignores some database specific activities (i.e.: Logging, Audits, Data-Type Checks, Constraints, etc) behind the scene, thus gives you maximum performance during the operation.

Basically, you mostly do the normal [Delete](/operation/delete), [Insert](/operation/insert), [Merge](/operation/merge) and [Update](/operation/update) operations when interacting with the database. Through this, the data is being processed in an atomic way. If you do call the [batch operations](/feature/batchoperations), it only execute the multiple single-operations together and does not completely eliminate the round-trips between your application and the database.

With the bulk operations, as mentioned above, all data is brought from the client application into the database at one go. Once the data is on the server, it is then being processed together within the database (server), maximizing the performance.

The bulk operations can improve the performance by more than 90% when processing a large dataset.

## Supported Providers

Bulk operations are available for the following providers, each via its own extension package:

- [SQL Server](/operation/sqlserver) — via [RepoDb.SqlServer.BulkOperations](https://www.nuget.org/packages/RepoDb.SqlServer.BulkOperations), built on ADO.NET's [SqlBulkCopy](https://learn.microsoft.com/en-us/dotnet/api/system.data.sqlclient.sqlbulkcopy).
- [Oracle](/operation/oracle) — via [RepoDb.Oracle.BulkOperations](https://www.nuget.org/packages/RepoDb.Oracle.BulkOperations), built on ODP.NET's [OracleBulkCopy](https://docs.oracle.com/en/database/oracle/oracle-data-access-components/23.9/odpnt/OracleBulkCopyClass.html).
- [PostgreSQL](/operation/postgresql) — via [RepoDb.PostgreSql.BulkOperations](https://www.nuget.org/packages/RepoDb.PostgreSql.BulkOperations), built on Npgsql's [NpgsqlBinaryImporter](https://www.npgsql.org/doc/api/Npgsql.NpgsqlBinaryImporter.html) (exposed as the `BinaryBulk*` methods).
- [Db2](/operation/db2) — via [RepoDb.Db2.BulkOperations](https://www.nuget.org/packages/RepoDb.Db2.BulkOperations), built on the IBM Data Server .NET Provider's [DB2BulkCopy](https://www.ibm.com/docs/en/db2/11.5?topic=classes-db2bulkcopy-class).
- [Firebird](/operation/firebird) — via [RepoDb.Firebird.BulkOperations](https://www.nuget.org/packages/RepoDb.Firebird.BulkOperations), built on `FbBatchCommand`, the FirebirdSql.Data.FirebirdClient driver's native ADO.NET batching API.

Each provider page linked above documents its own underlying mechanics, generated SQL statements, special arguments, and identity-setting alignment, since the implementation differs per ADO.NET driver.

## Common Characteristics

Regardless of provider, the bulk operations share the same shape:

- **Supported inputs** — `System.DataTable`, `System.Data.Common.DbDataReader`, `IEnumerable<T>`, `ExpandoObject`, and `IDictionary<string, object>` can all be passed as the data source.
- **Qualifiers** — the delete/merge/update variants accept a `qualifiers` argument (a list of [Field](/class/field) objects) to control matching. When omitted, the primary key is used.
- **BulkDeleteByKey** — every provider, including [Db2](/operation/db2/bulkdeletebykey), also exposes a dedicated `BulkDeleteByKey` operation, which deletes rows by a list of primary key values directly instead of requiring full entities.
- **BatchSize** — a `batchSize` argument overrides how many rows are wired up to the server per round-trip. By default, all rows are sent in one go.
- **Async methods** — every synchronous operation has an equivalent `Async` counterpart.
- **Staging (pseudo) tables** — delete/merge/update operations write to a temporary staging table first (under a transaction), then cascade the changes to the real table via a provider-specific SQL statement.

## Recommendation

Below are the items you may need to consider when to use the right operations (Bulk vs [Batch](/feature/batchoperations)).

- Network Latency
- Infrastructure
- No. of Columns
- Type of Data

Though there is no standard recommendation of when to use what, when using the library, we recommend to use the bulk operations if the datasets you are working is beyond 1000. Otherwhise, use the [batch](/feature/batchoperations) operations.
