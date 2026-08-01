---
layout: default
title: "Core"
nav_order: 1
has_children: true
permalink: /class/core
parent: CLASSES
---

# Classes (Core)

---

These are the provider-agnostic classes shared across the whole library. They include the query-expression building blocks ([QueryField](/class/queryfield), [QueryGroup](/class/querygroup), [OrderField](/class/orderfield), and function-specific variants like [UpperQueryField](/class/upperqueryfield), [LowerQueryField](/class/lowerqueryfield), [TrimQueryField](/class/trimqueryfield) and [LengthQueryField](/class/lengthqueryfield)), the repository and statement-builder base classes ([BaseRepository](/class/baserepository), [DbRepository](/class/dbrepository), [BaseStatementBuilder](/class/basestatementbuilder), [QueryBuilder](/class/querybuilder)), data extraction and reflection helpers ([DataReader](/class/datareader), [DataEntityDataReader](/class/dataentitydatareader), [ClassExpression](/class/classexpression), [ClassProperty](/class/classproperty), [Field](/class/field), [DbField](/class/dbfield), [PropertyValue](/class/propertyvalue)), caching ([CacheItem](/class/cacheitem), [MemoryCache](/class/memorycache)), operation tracing ([TraceLog](/class/tracelog), [CancellableTraceLog](/class/cancellabletracelog), [ResultTraceLog](/class/resulttracelog)), fluent mapping definitions ([EntityMapFluentDefinition](/class/entitymapfluentdefinition), [TypeMapFluentDefinition](/class/typemapfluentdefinition)), and library-wide configuration ([GlobalConfiguration](/class/globalconfiguration), [GlobalConfigurationOptions](/class/globalconfigurationoptions)).
