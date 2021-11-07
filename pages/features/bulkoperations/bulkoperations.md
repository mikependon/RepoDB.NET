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

#### When to use the Batch and Bulk Operations?

There is no standard of when to use what. It all depends on your situation (i.e.: Network Latency, No. of Columns, Type of Data, etc).

The pros of using a bulk operation is maximum performance, however, it keeps blocking the underlying table while being under the context of bulk operation transaction. Therefore, it might trigger a deadlock if not handled by the developers properly.

> We highly recommend to use the bulk operations if the data sets you are working is beyond 1000, otherwhise, just use the [batch](/feature/batchoperations) operations.