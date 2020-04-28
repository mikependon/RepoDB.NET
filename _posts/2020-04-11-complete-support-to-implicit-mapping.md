---
layout: post
title: "RepoDb complete support to Implicit Mapping"
author: "Michael Camara Pendon"
date: 2020-04-12 5:24:00 +0100
categories: blogs repodb
---

Happy Easter 2020! In the middle of my development of this site ([repodb.net](http://repodb.net)), 2 of the users of the library has asked me to introduce the [Implicit Mapping](/feature/implicitmapping) feature in [RepoDb](https://github.com/mikependon/RepoDb).

The requests can be found [here](https://github.com/mikependon/RepoDb/issues/415).

#### What is an Implicit Mapping?

It is feature that would allow you to map your desired attributes into your class (or class property) without even explicitly using the `Attribute`. This is to make sure that your models are attribute-free and is not bound to a specific ORM.

#### Attributes

In RepoDb, the following attributes were already given.

- [Map](/attribute/map) - is used to map the class (or class property) into its equivalent object in the database.
- [Primary](/attribute/primary) - is used to mark a class property to be a primary.
- [Identity](/attribute/identity) - is used to mark a class property to be an identity.
- [PropertyHandler](/attribute/propertyhandler) - is used the map a class property handler.
- [TypeMap](/attribute/typemap) - is used the map a class property and the database type.

###### Examples:

```csharp
[Map("[sales].[Customer]")]
public class Customer
{
    [Primary, Identity]
    public string Id { get; set; }

    [Map("FName")]
    public string FirstName { get; set; }

    [Map("LName")]
    public string LastName { get set; }

    [Map("DOB"), TypeMap(DbType.DateTime2), PropertyHandler(PersonDateOfBirthPropertyHandler)]
    public DateTime DateOfBirth { get; set; }

    public DateTime CreatedDateUtc { get; set; }
}
```

#### Mappers

Then following mappers were added.

- [ClassMapper](/mapper/typemapper) - as an alternative to [Map](/attribute/map), it is used to map a class into its equivalent database object.
- [PropertyMapper](/mapper/propertymapper) - as an alternative to [Map](/attribute/map), it is used to map a property into its equivalent column in the database.
- [PrimaryMapper](/mapper/primarymapper) - as an alternative to [Primary](/attribute/primary), it is used to mark a class property to be a primary.
- [IdentityMapper](/mapper/identitymapper) - as an alternative to [Identity](/attribute/identity), it is used to mark a class property to be an identity.
- [PropertyHandlerMapper](/mapper/propertyhandlermapper) - as an alternative to [PropertyHandler](/attribute/propertyhandler), it is used to map a property handler into a class property.
- [TypeMapper](/mapper/typemapper) - as an alternative to [TypeMap](/attribute/typemap), it is used the map a class property and the database type in both type and property level.

> The [TypeMapper](/mapper/typemapper) and [PropertyHandlerMapper](/mapper/propertyhandlermapper) were already given since the early days of the library. 

###### Examples:

Let us say you have a class below. 

```csharp
public class Customer
{
    public string Id { get; set; }
    public string FirstName { get; set; }
    public string LastName { get set; }
    public DateTime DateOfBirth { get; set; }
    public DateTime CreatedDateUtc { get; set; }
}
```

You can then associate the mappings via the code snippets below.

```csharp
// Class Mapping
ClassMapper.Add<Customer>("[sales].[Customer]");

// Property Mapping
PropertyMapper.Add<Customer>(c => c.FirstName, "FName");
PropertyMapper.Add<Customer>(c => c.LastName, "LName");
PropertyMapper.Add<Customer>(c => c.DateOfBirth, "DOB");

// Primary Mapping
PrimaryMapper.Add<Customer>(c => c.Id);

// Identity Mapping
IdentityMapper.Add<Customer>(c => c.Id);

// PropertyHandler Mapping (Property Level)
PropertyHandlerMapper.Add<Customer>(c => c.DateOfBirth, typeof(PersonDateOfBirthPropertyHandler));

// PropertyHandler Mapping (Type Level) - all properties with DateTime column
PropertyHandlerMapper.Add<DateTime>(typeof(DateTimePropertyHandler));

// Type Mapping (Property Level)
TypeMapper.Add<Customer>(c => c.DateOfBirth, DbType.DateTime2);

// Type Mapping (Type Level) - all properties with DateTime column
TypeMapper.Add<DateTime>(DbType.DateTime2);
```

#### Cachers

Then following cachers were added to cache the mappings. It is usually used for retrieval purposes.

- [ClassMappedNameCache](/cacher/classmappednamecache) - a cacher for [ClassMapper](/mapper/typemapper) mapper class and [Map](/attribute/map) attribute (class level).
- [PropertyMappedNameCache](/cacher/propertymappednamecache) - a cacher for [PropertyMapper](/mapper/propertymapper) mapper class and [Map](/attribute/map) attribute (property level).
- [PrimaryCache](/cacher/primarycache) - a cacher for [PrimaryMapper](/mapper/primarymapper) mapper class and [Primary](/attribute/primary) attribute.
- [IdentityCache](/cacher/identitycache) - a cacher for [IdentityMapper](/mapper/identitymapper) mapper class and [Identity](/attribute/identity) attribute.
- [PropertyHandlerCache](/cacher/propertyhandlercache) - a cacher for [PropertyHandlerMapper](/mapper/propertyhandlermapper) mapper class and [PropertyHandler](/attribute/propertyhandler) attribute.
- [TypeMapCache](/cacher/typemapcache) - a cacher for [TypeMapper](/mapper/typemapper) mapper class and [TypeMap](/attribute/typemap) attribute.

###### Examples

To get the mappings, use the code snippets below.

```csharp
// Class Mapping
var className = ClassMappedNameCache.Get<Customer>();

// Property Mapping
var propertyName = PropertyMappedNameCache.Get<Customer>(c => c.FirstName);

// Primary Mapping
var primary = PrimaryCache.Get<Customer>();

// Identity Mapping
var identity = IdentityCache.Get<Customer>();

// PropertyHandler Mapping (Property Level)
var propertyHandler = PropertyHandlerCache.Get<Customer>(c => c.DateOfBirth);

// PropertyHandler Mapping (Type Level) - all properties with DateTime column
var propertyHandler = PropertyHandlerCache.Get<DateTime>();

// Type Mapping (Property Level)
var dbType = TypeMapCache.Get<Customer>(c => c.DateOfBirth);

// Type Mapping (Type Level) - all properties with DateTime column
var dbType = TypeMapCache.Get<DateTime>();
```

> You can also get the mappings using the mapper classes, however, we highly recommend to use the cachers class to maximize the performance and efficiency.