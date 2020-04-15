<h5 class="center code-title">Repository</h5>

Leverage the exising repository objects and have all the operations inheritted without doing more codes. [Learn more](#)

```csharp
public class PersonRepository : BaseRepository<Person, SqlConnection>
{
    public PersonRepository(string connectionString) :
        base(connectionString)
    { }
}

using (var repository = new PersonRepository(connectionString))
{
    repository.Insert(person);
}
```