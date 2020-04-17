<h5 class="center code-title">Batch Operations</h5>

Significantly improve the CRUD operations by utilizing the batch operations. [Learn more](/feature/batchoperations)

```csharp
using (var connection = CreateDbConnection(connectionString))
{
    var customers = GetCustomers();
    var insertedRows = connection.InsertAll<Customer>(customers);
}
```
