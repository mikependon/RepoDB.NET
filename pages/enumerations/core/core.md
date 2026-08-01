---
layout: default
title: "Core"
nav_order: 1
has_children: true
permalink: /enumeration/core
parent: ENUMERATIONS
---

# Enumerations (Core)

---

Enumerations that are not tied to any specific database provider. These control how query expressions are composed and evaluated — [Conjunction](/enumeration/conjunction) (AND/OR) and [Operation](/enumeration/operation) (the comparison operators available to [QueryField](/class/queryfield)) — as well as broader library behavior: [Order](/enumeration/order) for sorting query results, [ConnectionPersistency](/enumeration/connectionpersistency) for controlling how a repository reuses its underlying connection, [ConversionType](/enumeration/conversiontype) for automatic type conversion during data extraction, and [KeyColumnReturnBehavior](/enumeration/keycolumnreturnbehavior) for controlling which key value push operations return.
