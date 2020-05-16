<h5 class="center code-title">Implicit Mapping</h5>

Make your entity models free from any attribute by leveraging the implicit mapping functionality. [Learn more](/feature/implicitmapping)

```csharp
public class Customer
{
    public int Id { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
}

FluentMapper
    .Entity<Customer>()
    .Table("[sales].[Customer]")
    .Primary(e => e.Id)
    .Identity(e => e.Id)
    .Column(e => e.FirstName, "FName")
    .Column(e => e.LastName, "LName");
```