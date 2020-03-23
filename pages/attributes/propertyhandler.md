---
layout: page
title: "PropertyHandler (RepoDb)"
permalink: /attribute/propertyhandler
tags: [repodb, class, propertyhandler, orm, hybrid-orm, sqlserver, sqlite, mysql, postgresql]
---

## PropertyHandler

This attribute is used to signify a property handling operation on the class property. By having this attribute, the library core's transformer will automatically trigger the property handler `Get` and `Set` method for custom handling.

> In this tutorial, we will use the [SQL Server](https://www.nuget.org/packages/RepoDb.SqlServer) as the database and C# as the programming language.

#### How to Use?

Let us say you have created a customized [IPropertyHandler](/interface/ipropertyhandler) like below.

```csharp
private class PersonAddressHandler : IPropertyHandler<string, Address>
{
	public Address Get(string input, ClassProperty property)
	{
		return JsonConvert.Deserialize<Address>(input);
	}

	public string Set(Address input, ClassProperty property)
	{
		return JsonConvert.Serialize(input);
	}
}
```

Then you can use this attribute to mark the class property to use the `PersonAddressHandler` property handler during the transformation.

```csharp
public class Person
{
	public long Id { get; set; }
	public string Name { get; set; }
	public int Age { get; set; }
	[PropertyHandler(typeof(PersonAddressHandler))]
	public Address Address { get; set; }
	public DateTime CreatedDateUtc { get; set; }
}
```

> The aim of this sample is to show that the property `Address` of the `Person` class is on type `NVARCHAR(MAX)` at the `[dbo].[Person]` table.