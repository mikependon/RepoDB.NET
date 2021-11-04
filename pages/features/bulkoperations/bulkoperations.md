---
layout: default
title: "Bulk Operations"
nav_order: 8
has_children: true
permalink: /feature/bulkoperations
parent: FEATURES
---

# Bulk Operations
{: .fs-9 }

Make your executions extremely fast!
{: .fs-6 .fw-300 }

---

A bulk operation is a process of bringing all the data from the application into the database server at once. It ignores some database specific activities (i.e.: Logging, Audits, Data-Type Checks, Constraints, etc) behind the scene, thus gives you maximum performance during the operation.

Basically, you mostly do the normal [Delete](/operation/delete), [Insert](/operation/insert), [Merge](/operation/merge) and [Update](/operation/update) operations when interacting with the database. Through this, the data is being processed in an atomic way. If you do call the [batch operations](/feature/batchoperations), it only execute the multiple single-operations together and does not completely eliminate the round-trips between your application and the database.

With the bulk operations, as mentioned above, all data is brought from the client application into the database at one go. Once the data is on the server, it is then being processed together within the database (server), maximizing the performance.

The bulk operations can improve the performance by more than 90% when processing a large dataset.