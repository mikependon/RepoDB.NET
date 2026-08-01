---
layout: default
title: "Core"
nav_order: 1
has_children: true
permalink: /interface/core
parent: INTERFACES
---

# Interfaces (Core)

---

Provider-agnostic interfaces for customizing the library's internals. They cover caching ([ICache](/interface/icache), [IExpirable](/interface/iexpirable)), entity and property conversion ([IClassHandler](/interface/iclasshandler), [IPropertyHandler](/interface/ipropertyhandler)), database-specific behavior ([IDbHelper](/interface/idbhelper), [IDbSetting](/interface/idbsetting), [IStatementBuilder](/interface/istatementbuilder)), generic type resolution ([IResolver](/interface/iresolver)), and operation tracing ([ITrace](/interface/itrace)).
