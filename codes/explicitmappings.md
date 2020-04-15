<h5 class="center code-title">Explicit</h5>

Organize the names of your tables and columns with a much more structured naming of your class and properties. [Learn more](#)

```csharp
[Map("[sales].[Customer]")]
public class Customer
{
    [Primary, Identity, Map("CustomerId")]
    public int Id { get; set; }

    [Map("FName")]
    public string FirstName { get; set; }
    
    [Map("LName")]
    public string LastName { get; set; }
    
    [PropertyHandler(typeof(CustomerAddressPropertyHandler))]
    public string Address { get; set; }

    [TypeMap(DbType.DateTime2) Map("DOB")]
    public DateTime DateOfBirth { get; set; }

    public DateTime CreatedDate { get; set; }
}
```