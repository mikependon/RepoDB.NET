<h5 class="center code-title">Bulk Merge</h5>

Migrates the big chunks of data back to database via bulk merge. [Learn more](/operation/bulkmerge)

```csharp
using (var connection = new SqlConnection(connectionString))
{
    var zipCodes = GetWorldZipCodes();
    connection.BulkMerge<ZipCode>(zipCodes);
}
```