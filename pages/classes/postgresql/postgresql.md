---
layout: default
title: "PostgreSQL"
nav_order: 4
has_children: true
permalink: /class/postgresql
parent: CLASSES
---

# Classes (PostgreSQL)

---

Classes specific to the PostgreSQL data provider ([RepoDb.PostgreSql](https://www.nuget.org/packages/RepoDb.PostgreSql), built on [Npgsql](https://www.npgsql.org/)). These cover connection bootstrapping and configuration ([PostgreSqlBootstrap](/class/postgresql/postgresqlbootstrap), [PostgreSqlGlobalConfiguration](/class/postgresql/postgresqlglobalconfiguration)), schema discovery ([PostgreSqlDbHelper](/class/postgresql/postgresqldbhelper)), SQL generation ([PostgreSqlStatementBuilder](/class/postgresql/postgresqlstatementbuilder)), provider settings ([PostgreSqlDbSetting](/class/postgresql/postgresqldbsetting)), and type resolution between .NET, `DbType`, `NpgsqlDbType` and PostgreSQL types ([ClientTypeToNpgsqlDbTypeResolver](/class/postgresql/clienttypetonpgsqldbtyperesolver), [DbTypeToPostgreSqlStringNameResolver](/class/postgresql/dbtypetopostgresqlstringnameresolver), [PostgreSqlDbTypeNameToClientTypeResolver](/class/postgresql/postgresqldbtypenametoclienttyperesolver), [PostgreSqlDbTypeNameToNpgsqlDbTypeResolver](/class/postgresql/postgresqldbtypenametonpgsqldbtyperesolver), [PostgreSqlConvertFieldResolver](/class/postgresql/postgresqlconvertfieldresolver)).
