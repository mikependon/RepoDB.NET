---
layout: default
title: MySqlDbType
permalink: /attribute/mysql/mysqldbtype
tags: [repodb, attribute, mysqldbtype, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
parent: MySQL
grand_parent: ATTRIBUTES
---

# MySqlDbType

---

This attribute is used to set the value of the `MySqlParameter.MySqlDbType` property via a class property.

> Both works on `MySql.Data` and `MySqlConnector` with their corresponding namespaces.

### Attribute

Below is a a sample code on how to use this attribute.

```csharp
public class Person
{
    public int Id { get; set; }

    [MySqlDbType(MySqlDbType.Text)]
    public string Name { get; set; }
}
```

### Fluent Mapping

Below is a sample code on how to use this attribute via [FluentMapper](/mapper/fluentmapper).

```csharp
FluentMapper
    .Entity<Person>()
    .PropertyValueAttributes(e => e.Name, new [] { new MySqlDbTypeAttribute(MySqlDbType.Text) })
```