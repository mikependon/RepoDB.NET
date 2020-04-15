<h5 class="center code-title">Hints</h5>

**Optimize** your query execution by **maximizing** and **leveraging** the power of **table optimizers**. [Learn more](#)

```csharp
using (var connection = new SqlConnection(connectionString))
{
    connection.Query<Customer>(c => c.Id == 10045,
        hints: SqlServerTableHints.NoLock);
}
```