---
layout: page
title: References (RepoDb)
permalink: /references
---

## References

This page contains all the recommended-way of implementing objects when using this library.

#### Repository Implementations

- [Connection-Based Repository](/reference/connectionrepository) - a direct and simple repository (popular).
- [BaseRepository](/reference/baserepository) - an entity-based repository.
- [DbRepository](/reference/dbrepository) - a shared repository.
- [GenericRepository](/reference/genericrepository) - customize your own base repository.

#### Feature Implementations

- [Caching (JSON)](/reference/jsoncache) - a JSON-based caching (file system).
- [Multiple Resultsets](/reference/multipleresultsets) - query single parent and its children / multiple parents and there children.
- [Property Handler (Property Level)](/reference/propertyhandlerpropertylevel) - string database type to .NET CLR type.
- [Property Handler (Type Level)](/reference/propertyhandlertypelevel) - handling the dates and guids.
- [Query Expressions](/reference/queryexpressions) - calls to object-based, dynamic-based, expression-based, method-`Contains` (both arrays and strings), method-`StartsWith` and method-`EndsWith`.
- [Tracing](/reference/trace) - logging and auditing (or [SerilogTrace](/reference/serilogtrace)).
- [Unit of Work](/reference/unitofwork) - acidity on the multiple operations.
