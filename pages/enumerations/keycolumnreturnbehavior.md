---
layout: default
sidebar: enumerations
title: "KeyColumnReturnBehavior"
description: "A enumeration that defines the type of conversion to be used when extracting a data from/to the database."
permalink: /enumeration/keycolumnreturnbehavior
tags: [repodb, keycolumnreturnbehavior]
parent: ENUMERATIONS
---

# KeyColumnReturnBehavior

---

This enum is used to define how the push operations (i.e.: [Insert](/operation/insert), [InsertAll](/operation/insertall), [Merge](/operation/merge) and [MergeAll](/operation/mergeall)) behaves when returning the value from the column columns (i.e.: Primary and Identity).

### Enum Values

- `Primary` - returns the value of the primary column.
- `Identity` - returns the value of the identity column.
- `PrimaryOrElseIdentity`- returns the coalesced value of the primary and identity columns. This is the default value.
- `IdentityOrElsePrimary`- eturns the coalesced value of the identity and primary columns.
