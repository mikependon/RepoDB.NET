---
layout: default
title: "Core"
nav_order: 1
has_children: true
permalink: /mapper/core
parent: MAPPERS
---

# Mappers (Core)

---

Mapper classes let you define entity mappings in code as an alternative to decorating your models with [attributes](/attribute/core). [ClassMapper](/mapper/classmapper), [PropertyMapper](/mapper/propertymapper), [IdentityMapper](/mapper/identitymapper) and [PrimaryMapper](/mapper/primarymapper) map a class or property to its table, column, identity and primary-key equivalents; [ClassHandlerMapper](/mapper/classhandlermapper), [PropertyHandlerMapper](/mapper/propertyhandlermapper) and [PropertyValueAttributeMapper](/mapper/propertyvalueattributemapper) wire up custom handlers; [TypeMapper](/mapper/typemapper) maps a .NET CLR type or property to its database type; and [DbHelperMapper](/mapper/dbhelpermapper), [DbSettingMapper](/mapper/dbsettingmapper) and [StatementBuilderMapper](/mapper/statementbuildermapper) map the per-provider helper, setting and statement-builder classes used internally by the library. [FluentMapper](/mapper/fluentmapper) ties all of the above together into a single fluent entry point.
