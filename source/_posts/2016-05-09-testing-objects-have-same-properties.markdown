---
layout: post
title: "Testing that different objects have the same properties"
date: 2016-05-09 19:25:17 +0200
comments: true
categories: [c#, testing, fluentassertions, tdd]
---
Sometimes you want to ensure that 2 unrelated objects share a set of properties -- without using an interface.

Here is an example:

``` csharp
namespace Demo
{
    public class Customer
    {
        public string FirstName { get; set; }
        public string LastName { get; set; }
    }

    public class Person
    {
        public string FirstName { get; set; }
        public string LastName { get; set; }
    }
}
```
First thought for C# developers: [AutoMapper](http://automapper.org/)

Let's do that:

``` csharp
using AutoMapper;

namespace Demo
{
    public class MyMapping
    {
        public static IMapper Mapper;

        public static void Init()
        {
            var cfg = new MapperConfiguration(x =>
            {
                x.CreateMap<Customer, Person>();
            });
            Mapper = cfg.CreateMapper();
        }
    }
}
```

Now we can write a unit test to see if we can convert a Customer to a Person:

``` csharp
using Xunit;

namespace Demo
{
    public class SomeTests
    {
        [Fact]
        public void Given_Customer_Should_ConvertTo_Person()
        {
            // Arrange
            const string firstname = "foo";
            const string lastname = "bar";

            var customer = new Customer
            {
                FirstName = firstname,
                LastName = lastname
            };

            MyMapping.Init();

            // Act
            var person = MyMapping.Mapper.Map<Customer, Person>(customer);
            
            // Assert
            person.FirstName.Should().Be(firstname);
            person.LastName.Should().Be(lastname);
        }
	}
}	
```
This test passes.

But what happens when we want to ensure that a new Customer property (for example `Email`) is reflected in the Person object?

``` csharp
namespace Demo
{
    public class Customer
    {
        public string FirstName { get; set; }
        public string LastName { get; set; }
        public string Email { get; set; } // <-- new property
    }

    public class Person
    {
        public string FirstName { get; set; }
        public string LastName { get; set; }
    }
}
```

Our unit test still passes. &#9785;

**Wouldn't it be nice to have our unit test fail if the classes are not in sync?**

Here is where [FluentAssertions](http://www.fluentassertions.com/) `ShouldBeEquivalentTo` comes in handy:

``` csharp
using FluentAssertions;
using Xunit;

[Fact]
public void Given_Customer_Should_ConvertTo_Person_With_CurrentProperties()
{
    //Arrange
    const string firstname = "foo";
    const string lastname = "bar";

    var customer = new Customer
    {
        FirstName = firstname,
        LastName = lastname
    };

    MyMapping.Init();

    // Act
    var person = MyMapping.Mapper.Map<Customer, Person>(customer);

    // Assert
    customer.ShouldBeEquivalentTo(person);
}
```

{% img /images/posts/dotnet/equivalentto_result.png %}

`Subject has a member  Email that the other object does not have.`

Cool: This is the kind of message I want to have from a unit test!

`ShouldBeEquivalentTo` also takes an optional Lambda expression in case you need more fine grained control which properties are included in the comparison. Here is an example where we exlude the `Email` property on purpose:

``` csharp
using FluentAssertions;
using Xunit;

[Fact]
public void Given_Customer_Should_ConvertTo_Person_With_CurrentProperties_Excluding_Email()
{
    //Arrange
    const string firstname = "foo";
    const string lastname = "bar";

    var customer = new Customer
    {
        FirstName = firstname,
        LastName = lastname
    };

    MyMapping.Init();

    // Act
    var person = MyMapping.Mapper.Map<Customer, Person>(customer);

    // Assert
    customer.ShouldBeEquivalentTo(person,
        options =>
            options.Excluding(x => x.Email));
}
```

This test passes.


The complete documentation for FluentAssertions' `ShouldBeEquivalentTo` method can be found [here](https://github.com/dennisdoomen/fluentassertions/wiki#object-graph-comparison).

## Source code for this post

You can clone a copy of this project here: [https://github.com/draptik/blog-demo-shouldbeequivalentto](https://github.com/draptik/blog-demo-shouldbeequivalentto).

``` sh
git clone https://github.com/draptik/blog-demo-shouldbeequivalentto.git
```
