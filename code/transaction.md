<h5 class="center code-title">Transaction</h5>

Make the execution more ACID by placing the execution context into a transaction object. [Learn more](/feature/transaction)

```csharp
using (var connection = new SqlConnection(connectionString).EnsureOpen())
{
    using (var transaction = connection.BeginTransaction())
    {
        connection.Insert<Customer>(customer, transaction);
        transaction.Commit();
    }
}
```
