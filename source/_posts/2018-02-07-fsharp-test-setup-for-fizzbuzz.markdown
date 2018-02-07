---
layout: post
title: "F# Test Setup for FizzBuzz"
date: 2018-02-07 20:53:43 +0000
comments: true
categories: [linux, fsharp, kata, fizzbuzz, dotnetcore, .NET, tdd, test, testing]
---
In my [previous post](http://draptik.github.io/blog/2018/02/05/fsharp-setup-linux-fizzbuzz/) we setup a basic F# project in Linux. 

In this post I would like to show how to setup an idiomatic F# testing environment using FsUnit.


#### Side note for people unfamiliar with .NET
Actually, it's not a project, but a "solution". To clear things up for people not familiar with the .NET ecosystem: In .NET, the top level configuration is called a "solution" and resides in a `*.sln` file. A solution references "projects". Each project configuration is stored in a `*.fsproj` file (F#) or a `*.csproj` file (C#). Projects can reference each other. This information is stored in the `*.[f|c]sproj` file.

We have 2 projects (`FizzBuzz` and `FizzBuzz.Tests`), each with a `*.fsproj` file. The `FizzBuzz.Tests.fsproj` references the `FizzBuzz.fsproj` file (see the [previous post](http://draptik.github.io/blog/2018/02/05/fsharp-setup-linux-fizzbuzz/) for details):
```
.
├── FizzBuzz
│   ├── FizzBuzz.fsproj
│   ├── ...
├── FizzBuzz.Tests
│   ├── FizzBuzz.Tests.fsproj
│   ├── ...
└── fsharp-kata-fizzbuzz.sln
```

### Current state

This is the current state of our test:
```fsharp
[<Fact>]
let ``Array with Number 1 returns 'one'`` () =
    let result = FizzBuzz.Generate [1]
    Assert.Equal(result, "one")
```

- `[<Fact>]`: this is F#'s annotation style. The same as C# `[Fact]` or Java `@Fact`
- `Array with Number 1 returns 'one'`: Method name in double back-ticks improves readability, especially in unit tests. No CamelCasing or snake_casing needed. It's an F# language feature.
- `Assert.Equal(...)`: This is probably familiar to everyone who has ever written a unit test. Every assertion library has a different signature: Is it `Equal(expected, actual)` or `Equal(actual, expected)`? I hate this! Thankfully there are alternative assertion libraries. Example: In C# you can write `actual.Should().Be(expected)` (using [`FluentAssertions`](http://fluentassertions.com/)). The same is true for F#.

### FsUnit: Idiomatic assertions

What does "idiomatic" mean? For programming languages, it means: Writing code as most people, who are used to the language, would write the code (how a "native" would express an idea, a concept, an algorithm, etc). Simple example: In Java and JS, the first character of a method name should be lower case. In C#, the first character should be upper case (yes, even if the method is private!). The code will still compile if you don't comply to these conventions, but it's not "idiomatic". Same goes for "For Loops" vs using a "Map" functions: In some languages one concept is preferred over the other.

`FsUnit` brings **pipes** to F# unit tests. Pipes are used extensively in F# and should be familiar to most linux shell users: Bash uses the `|` symbol as operator to redirect the output of one expression to the input of another expression. In F# the pipe operator is `|>`. The concept might seem similar to using "Method Chaining" in C# (it's not, but close enough in this context).

Example:
```fsharp
// instead of
Assert.Equal(1 + 1, 2)

// idiomatic F# (using pipe) with FsUnit:
1 + 1 |> should equal 2
```

#### Installing FsUnit

```sh
cd FizzBuzz.Tests
dotnet add package FsUnit.Xunit
```

File `FizzBuzz.Tests/FizzBuzz.Tests.fsproj` should now look like this (plus/minus some version numbers):
```xml
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <TargetFramework>netcoreapp2.0</TargetFramework>

    <IsPackable>false</IsPackable>
  </PropertyGroup>

  <ItemGroup>
    <Compile Include="Tests.fs" />
    <Compile Include="Program.fs" />
  </ItemGroup>

  <ItemGroup>
    <PackageReference Include="FsUnit.Xunit" Version="3.0.0" />
    <PackageReference Include="Microsoft.NET.Test.Sdk" Version="15.5.0" />
    <PackageReference Include="xunit" Version="2.3.1" />
    <PackageReference Include="xunit.runner.visualstudio" Version="2.3.1" />
    <DotNetCliToolReference Include="dotnet-xunit" Version="2.3.1" />
  </ItemGroup>

  <ItemGroup>
    <ProjectReference Include="..\FizzBuzz\FizzBuzz.fsproj" />
  </ItemGroup>

</Project>
```

Note the line `<PackageReference Include="FsUnit.Xunit" Version="3.0.0" />` (your version number might differ).

#### Using FsUnit

Modify the test file `FizzBuzz.Tests/Tests.fs` to look like this:
```fsharp
module Tests

open System
open FsUnit.Xunit // <-- add FsUnit.Xunit
open Xunit
open FizzBuzz

[<Fact>]
let ``Array with Number 1 returns 'one'`` () =
    FizzBuzz.Generate [1] 
    |> should equal "one" // using "|>" and "should" syntax
```

Running the unit tests within the test folder:

```sh
dotnet test
Build started, please wait...
Build completed.

Test run for /home/patrick/projects/fsharp-blog-fizzbuzz/fsharp-kata-fizzbuzz/FizzBuzz.Tests/bin/Debug/netcoreapp2.0/FizzBuzz.Tests.dll(.NETCoreApp,Version=v2.0)
Microsoft (R) Test Execution Command Line Tool Version 15.5.0
Copyright (c) Microsoft Corporation.  All rights reserved.

Starting test execution, please wait...
[xUnit.net 00:00:00.7436128]   Discovering: FizzBuzz.Tests
[xUnit.net 00:00:00.8627111]   Discovered:  FizzBuzz.Tests
[xUnit.net 00:00:00.8695487]   Starting:    FizzBuzz.Tests
[xUnit.net 00:00:01.1888259]   Finished:    FizzBuzz.Tests

Total tests: 1. Passed: 1. Failed: 0. Skipped: 0.
Test Run Successful.
Test execution time: 2.4787 Seconds
```

### Summary

We can now write unit tests in an F# way ("idiomatic") by using the library `FsUnit`.

Have fun with F# and linux!

Get the source code [here](https://github.com/draptik/blog-fsharp-fizzbuzz-setup)
