---
layout: default
sidebar: releases
title: SQL Server
description: "This page contains the latest information of the releases of RepoDb.SqlServer library."
nav_order: 2
permalink: /release/sqlserver
parent: RELEASES
---

# Releases for RepoDb.SqlServer

---

Please click [here](https://www.nuget.org/packages/RepoDb.SqlServer) to see the actual Nuget package. Otherwise, please click [here](https://www.nuget.org/api/v2/package/RepoDb.SqlServer) to download it.


### RepoDB.SqlServer (v1.1.4)

- Added the `net5.0` as part of the target frameworks.
- Upgraded the `RepoDb` package to `v1.12.8-beta5`.
- Upgraded the `System.Data.SqlClient` package to `v4.8.3`.
- Deprecated both the [MicrosoftSqlServerTypeMapAttribute](/attribute/microsoftsqlservertypemap) and [SystemSqlServerTypeMapAttribute](/attribute/systemsqlservertypemap) attributes.
- Added the [CompareInfoAttribute](/attribute/sqlparametercompareinfo) attribute. [#884](https://github.com/mikependon/RepoDb/issues/884)
- Added the [ForceColumnEncryptionAttribute](/attribute/sqlserver/forcecolumnencryption) attribute. [#884](https://github.com/mikependon/RepoDb/issues/884)
- Added the [LocaleIdAttribute](/attribute/sqlserver/localeid) attribute. [#884](https://github.com/mikependon/RepoDb/issues/884)
- Added the [OffsetAttribute](/attribute/sqlserver/offset) attribute. [#884](https://github.com/mikependon/RepoDb/issues/884)
- Added the [SqlDbTypeAttribute](/attribute/sqlserver/sqldbtype) attribute. [#884](https://github.com/mikependon/RepoDb/issues/884)
- Added the [TypeNameAttribute](/attribute/sqlserver/typename) attribute. [#884](https://github.com/mikependon/RepoDb/issues/884)
- Added the [UdtTypeNameAttribute](/attribute/sqlserver/udttypename) attribute. [#884](https://github.com/mikependon/RepoDb/issues/884)
- Added the [XmlSchemaCollectionDatabaseAttribute](/attribute/sqlserver/xmlschemacollectiondatabase) attribute. [#884](https://github.com/mikependon/RepoDb/issues/884)
- Added the [XmlSchemaCollectionNameAttribute](/attribute/sqlserver/xmlschemacollectionname) attribute. [#884](https://github.com/mikependon/RepoDb/issues/884)
- Added the [XmlSchemaCollectionOwningSchemaAttribute](/attribute/sqlserver/xmlschemacollectionowningschema) attribute. [#884](https://github.com/mikependon/RepoDb/issues/884)
- Enhancement: Add the attribute-based parameter setters for the `DbParameter` object. [#886](https://github.com/mikependon/RepoDB/issues/886)


### RepoDB.SqlServer (v1.1.4-beta1)

- Added the `net5.0` as part of the target frameworks.
- Upgraded the `RepoDb` package to `v1.12.8-beta5`.
- Upgraded the `System.Data.SqlClient` package to `v4.8.3`.
- Deprecated both the [MicrosoftSqlServerTypeMapAttribute](/attribute/microsoftsqlservertypemap) and [SystemSqlServerTypeMapAttribute](/attribute/systemsqlservertypemap) attributes.
- Added the [CompareInfoAttribute](/attribute/sqlparametercompareinfo) attribute. [#884](https://github.com/mikependon/RepoDb/issues/884)
- Added the [ForceColumnEncryptionAttribute](/attribute/sqlparameterforcecolumnencryption) attribute. [#884](https://github.com/mikependon/RepoDb/issues/884)
- Added the [LocaleIdAttribute](/attribute/parameter/sqlserver/localeid) attribute. [#884](https://github.com/mikependon/RepoDb/issues/884)
- Added the [OffsetAttribute](/attribute/parameter/sqlserver/offset) attribute. [#884](https://github.com/mikependon/RepoDb/issues/884)
- Added the [SqlDbTypeAttribute](/attribute/parameter/sqlserver/sqldbtype) attribute. [#884](https://github.com/mikependon/RepoDb/issues/884)
- Added the [TypeNameAttribute](/attribute/parameter/sqlserver/typename) attribute. [#884](https://github.com/mikependon/RepoDb/issues/884)
- Added the [UdtTypeNameAttribute](/attribute/parameter/sqlserver/udttypename) attribute. [#884](https://github.com/mikependon/RepoDb/issues/884)
- Added the [XmlSchemaCollectionDatabaseAttribute](/attribute/parameter/sqlserver/xmlschemacollectiondatabase) attribute. [#884](https://github.com/mikependon/RepoDb/issues/884)
- Added the [XmlSchemaCollectionNameAttribute](/attribute/parameter/sqlserver/xmlschemacollectionname) attribute. [#884](https://github.com/mikependon/RepoDb/issues/884)
- Added the [XmlSchemaCollectionOwningSchemaAttribute](/attribute/parameter/sqlserver/xmlschemacollectionowningschema) attribute. [#884](https://github.com/mikependon/RepoDb/issues/884)
- Enhancement: Add the attribute-based parameter setters for the `DbParameter` object. [#886](https://github.com/mikependon/RepoDB/issues/886)


### RepoDB.SqlServer (v1.1.3)

- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.12.7](/release/core#repodb-v1127)).


### RepoDB.SqlServer (v1.1.2)

- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.12.5](/release/core#repodb-v1125)).


### RepoDB.SqlServer (v1.1.1)

- CancellationToken support? [#343](https://github.com/mikependon/RepoDB/issues/343)
- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.12.4](/release/core#repodb-v1124)).


### RepoDb.SqlServer (v1.1.0)

- Complete support to F# programming language as requested by the F# Community. Thank you to [Isaac Abraham](https://twitter.com/isaac_abraham) and [Angel Munoz](https://twitter.com/Daniel_Tuna) for being so collaborative.
- Breaking: Introduced the `fields` argument to the fetch (i.e.: [Query](/operation/query), [ExecuteQuery](/operation/executequery) and [BatchQuery](/operation/batchquery)) and push (i.e.: [Insert](/operation/insert), [Merge](/operation/merge) and [Update](/operation/update)) operations.
- Breaking: Introduced the `tableName` argument to all entity based operations. This completely resolves the `F#` anonymous invocations.
- Breaking: Renamed of argument `whereOrPrimaryKey` to `what`.
- Enhacement: Complete support to the immutable classes and/or types.
- Refactoring: Refactored the compiler implementations by simplifying the code from Monolithic to Smaller/Shorter/Cleaner code.
- Feature: Introduced the support to the [Class Handler](/feature/classhandler).
- Test: More coverage of Integration Tests and Unit Tests.
- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.12.0](/release/core#repodb-v1120)).


### RepoDb.SqlServer (v1.1.0-beta2)

- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.12.0-beta4](/release/core#repodb-v1120-beta4)).


### RepoDb.SqlServer (v1.1.0-beta1)

- Complete support to F# programming language as requested by the F# Community. Thank you to [Isaac Abraham](https://twitter.com/isaac_abraham) and [Angel Munoz](https://twitter.com/Daniel_Tuna) for being so collaborative.
- Breaking: Introduced the `fields` argument to the fetch (i.e.: [Query](/operation/query), [ExecuteQuery](/operation/executequery) and [BatchQuery](/operation/batchquery)) and push (i.e.: [Insert](/operation/insert), [Merge](/operation/merge) and [Update](/operation/update)) operations.
- Breaking: Introduced the `tableName` argument to all entity based operations. This completely resolves the `F#` anonymous invocations.
- Breaking: Renamed of argument `whereOrPrimaryKey` to `what`.
- Enhacement: Complete support to the immutable classes and/or types.
- Refactoring: Refactored the compiler implementations by simplifying the code from Monolithic to Smaller/Shorter/Cleaner code.
- Feature: Introduced the support to the [Class Handler](/feature/classhandler).
- Test: More coverage of Integration Tests and Unit Tests.
- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.12.0-beta1](/release/core#repodb-v1120-beta1)).


### RepoDb.SqlServer (v1.0.13)

- Bug: `NullReferenceException` occurs when `InsertAllAsync` is executed on Connection that belongs to `TransactionScope`. [#490](https://github.com/mikependon/RepoDb/issues/490)
- Enhancement: Remove the `SchemaSeparator` property from the `IDbSetting` interface [#475](https://github.com/mikependon/RepoDb/issues/475)
- Enhancement: Refactor the usage of `Count()` and `ElementAt()`.
- Bug: RepoDB does not seem to work for SQL Server tables with dot in the name [#492](https://github.com/mikependon/RepoDb/issues/492)


### RepoDb.SqlServer (v1.0.12)

- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.11.5](/release/core#repodb-v1115)).


### RepoDb.SqlServer (v1.0.11)

- Updated the version of `Microsoft.Data.SqlClient` into `v2.0.0`.
- Bug: table schema are ignored [#456](https://github.com/mikependon/RepoDb/issues/456)
- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.11.4](/release/core#repodb-v1114)).


### RepoDb.SqlServer (v1.0.11-beta1)

- Bug: table schema are ignored [#456](https://github.com/mikependon/RepoDb/issues/456)


### RepoDb.SqlServer (v1.0.10)

- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.11.3](/release/core#repodb-v1113)).


### RepoDb.SqlServer (v1.0.9)

- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.11.2](/release/core#repodb-v1112)).


### RepoDb.SqlServer (v1.0.8)

- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.11.1](/release/core#repodb-v1111)).


### RepoDb.SqlServer (v1.0.7)

- Bug: Truncates string and bytes [#423](https://github.com/mikependon/RepoDb/issues/423)
- Referenced the Microsoft.Data.SqlClient version 1.1.2.
- Referenced the System.Data.SqlClient version 4.8.1. 
- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.11.0](/release/core#repodb-v1110)).


### RepoDb.SqlServer (v1.0.6)

- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.10.11](/release/core#repodb-v11011)).


### RepoDb.SqlServer (v1.0.5)

- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.10.11](/release/core#repodb-v11011)).


### RepoDb.SqlServer (v1.0.4)

- Referenced the version [RepoDb.Core](https://www.nuget.org/packages/RepoDb) ([v1.10.10](/release/core#repodb-v11010)).


### RepoDb.SqlServer (v1.0.3)

- Feature: Introduce the SQL Server support to `SqlParameter.SqlDbType` via `MicrosoftSqlServerTypeMapAttribute` class (for Microsoft.Data.SqlClient) and `SystemSqlServerTypeMapAttribute` class (for System.Data.SqlClient).
- Added more Unit Tests.


### RepoDb.SqlServer (v1.0.2)

- Resume the support to System.Data.SqlClient.


### RepoDb.SqlServer(v1.0.1)

- Initial release for RepoDb.SqlServer.


### RepoDb.SqlServer (v1.0.0)

- Initial release for RepoDb.SqlServer.
