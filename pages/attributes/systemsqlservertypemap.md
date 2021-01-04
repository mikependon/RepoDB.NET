---
layout: default
sidebar: attributes
title: "SystemSqlServerTypeMap"
description: "Learn on how to use the RepoDB SystemSqlServerTypeMap attribute."
permalink: /attribute/systemsqlservertypemap
tags: [repodb, class, systemsqlservertypemap, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
parent: ATTRIBUTES
---

# SystemSqlServerTypeMap

---

This attribute is used to map a property into its equivalent type from the database (via `System.Data.SqlDbType`).

It only supports the SQL Server and if you are using the `System.Data.SqlClient` namespace.

> This attribute is setting the value of the `SqlParameter.SqlDbType` property before the actual execution.

### How to use?

Below is a a sample code on how to map an existing property into a `System.Data.SqlDbType` database type.

```csharp
public class Person
{
    public int Id { get; set; }
    [SystemSqlServerTypeMap(SqlDbType.NVarChar)] // Mapping this to 'NVarChar'
    public string Name { get; set; }
    public DateTime DateOfBirth { get; set; }
    public int Age { get; set; }
    public DateTime DateInsertedUtc { get; set; }
}
```

> This attribute is currently not supported by the [FluentMapper](/mapper/fluentmapper).