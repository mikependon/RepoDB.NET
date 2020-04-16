<h5 class="center code-title">Batch Operations</h5>

Improve the CRUD operation performance by using the Batch operations. [Learn more](/feature/batchoperations)

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var customers = GetCustomers();
    var insertedRows = connection.InsertAll<Customer>(customers);
}
```