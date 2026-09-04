---
layout: default
title: "SAP HANA"
has_children: true
permalink: /enumeration/saphana
parent: ENUMERATIONS
---

# Enumerations (SAP HANA)

---

Enumerations used by the [SAP HANA bulk operations](/operation/saphana). [SapHanaBulkImportIdentityBehavior](/enumeration/saphana/saphanabulkimportidentitybehavior) controls whether newly generated identity values are returned back to the entities after [BulkInsert](/operation/saphana/bulkinsert) or [BulkMerge](/operation/saphana/bulkmerge), [SapHanaBulkImportPseudoTableType](/enumeration/saphana/saphanabulkimportpseudotabletype) controls the kind of staging table created to stage the bulk-imported data, and [SapHanaWriteToServerExecution](/enumeration/saphana/saphanawritetoserverexecution) controls how the `Async` overloads write rows to the server (via [SapHanaBulkDbSetting](/class/saphana/saphanabulkdbsetting)).
