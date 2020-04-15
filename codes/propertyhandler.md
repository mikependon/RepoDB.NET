<h5 class="center code-title">Property Handler</h5>

Customize the column transformations in and out from your database via property handlers. [Learn more](/feature/propertyhandlers)

```csharp
public class Customer
{
    public int Id { get; set; }

    [PropertyHandler(typeof(CustomerAddressPropertyHandler))]
    public string Address { get; set; }
}
```