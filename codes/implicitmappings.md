<h5 class="center code-title">Implicit Mapping</h5>

Make your models free from attributes by leveraging the implicit mapping functionality. [Learn more](/feature/implicitmapping)

```csharp
public class Customer
{
    public int Id { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
}

ClassMapper.Add<Customer>("[sales].[Customer]");
PrimaryMapper.Add<Customer>(e => e.Id);
IdentityMapper.Add<Customer>(e => e.Id);
PropertyMapper.Add<Customer>(e => e.FirstName, "FName");
PropertyMapper.Add<Customer>(e => e.Id, "LName");
```