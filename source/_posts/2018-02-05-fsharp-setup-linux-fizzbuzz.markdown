---
layout: post
title: "F# Setup Linux: FizzBuzz"
date: 2018-02-05 22:31:04 +0000
comments: true
categories: [linux, fsharp, kata, fizzbuzz]
---
One of the first things I always struggle with when learning new languages is the environment. Here is a simple setup for playing with F# and Linux.

## Prerequisite: .NET Core with Linux

I won't go into setting up .NET Core for linux. This should be straightforward either by following [Microsoft instructions](https://docs.microsoft.com/en-us/dotnet/core/linux-prerequisites?tabs=netcore2x) or, in my case, the [Arch Linux homepage](https://wiki.archlinux.org/index.php/.NET_Core). `dotnet --info` should return something similar to:

```
.NET Command Line Tools (2.1.3)

Product Information:
 Version:            2.1.3
 Commit SHA-1 hash:  a0ca411ca5

Runtime Environment:
 OS Name:     arch
 OS Version:
 OS Platform: Linux
 RID:         linux-x64
 Base Path:   /opt/dotnet/sdk/2.1.3/

Microsoft .NET Core Shared Framework Host

  Version  : 2.0.5
  Build    : 17373eb129b3b05aa18ece963f8795d65ef8ea54
```

## Creating a Kata

Let's create a project for the FizzBuzz Kata.

```
mkdir fsharp-kata-fizzbuzz
cd fsharp-kata-fizzbuzz
dotnet new classlib -lang f# -o FizzBuzz
dotnet new xunit -lang f# -o FizzBuzz.Tests
cd FizzBuzz.Tests
dotnet add reference ../FizzBuzz/FizzBuzz.fsproj
cd ..
dotnet new sln
dotnet sln add FizzBuzz/FizzBuzz.fsproj
dotnet sln add FizzBuzz.Tests/FizzBuzz.Tests.fsproj
```

(I really love this new "CLI first" approach! It makes live so much easier for DevOps)

This is our project structure after templating:

```
tree . -L 4
.
├── FizzBuzz
│   ├── bin
│   │   └── Debug
│   │       └── netstandard2.0
│   ├── FizzBuzz.fsproj
│   ├── Library.fs
│   └── obj
│       ├── Debug
│       │   └── netstandard2.0
│       ├── FizzBuzz.fsproj.nuget.cache
│       ├── FizzBuzz.fsproj.nuget.g.props
│       ├── FizzBuzz.fsproj.nuget.g.targets
│       └── project.assets.json
├── FizzBuzz.Tests
│   ├── bin
│   │   └── Debug
│   │       └── netcoreapp2.0
│   ├── FizzBuzz.Tests.fsproj
│   ├── obj
│   │   ├── Debug
│   │   │   └── netcoreapp2.0
│   │   ├── FizzBuzz.Tests.fsproj.nuget.cache
│   │   ├── FizzBuzz.Tests.fsproj.nuget.g.props
│   │   ├── FizzBuzz.Tests.fsproj.nuget.g.targets
│   │   └── project.assets.json
│   ├── Program.fs
│   └── Tests.fs
└── fsharp-kata-fizzbuzz.sln
```

The 3 project files (top - down)...

`fsharp-kata-fizzbuzz.sln` (nothing new here)
```
Microsoft Visual Studio Solution File, Format Version 12.00
# Visual Studio 15
VisualStudioVersion = 15.0.26124.0
MinimumVisualStudioVersion = 15.0.26124.0
Project("{F2A71F9B-5D33-465A-A702-920D77279786}") = "FizzBuzz", "FizzBuzz\FizzBuzz.fsproj", "{C64F3370-DE54-4D58-BDD4-33C4B02F7290}"
EndProject
Project("{F2A71F9B-5D33-465A-A702-920D77279786}") = "FizzBuzz.Tests", "FizzBuzz.Tests\FizzBuzz.Tests.fsproj", "{4AA6DACD-EA0E-4938-BB41-7B055A9A0C8C}"
EndProject
[...]
```

`FizzBuzz/FizzBuzz.fsproj` (not relevant here, but keep in mind that fsharp files have to be in the correct order):
```xml
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <TargetFramework>netstandard2.0</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <Compile Include="Library.fs" />
  </ItemGroup>

</Project>
```


`FizzBuzz.Tests/FizzBuzz.Tests.fsproj`:
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

Running `dotnet test` returns

```
$ dotnet test
Build started, please wait...
Build started, please wait...
Build completed.

Test run for /home/patrick/projects/fsharp-blog-fizzbuzz/fsharp-kata-fizzbuzz/FizzBuzz/bin/Debug/netstandard2.0/FizzBuzz.dll(.NETStandard,Version=v2.0)
Microsoft (R) Test Execution Command Line Tool Version 15.5.0
Copyright (c) Microsoft Corporation.  All rights reserved.

Starting test execution, please wait...
No test is available in /home/patrick/projects/fsharp-blog-fizzbuzz/fsharp-kata-fizzbuzz/FizzBuzz/bin/Debug/netstandard2.0/FizzBuzz.dll. Make sure test project has a nuget reference of package "Microsoft.NET.Test.Sdk" and framework version settings are appropriate and try again.

Test Run Aborted.
Build completed.

Test run for /home/patrick/projects/fsharp-blog-fizzbuzz/fsharp-kata-fizzbuzz/FizzBuzz.Tests/bin/Debug/netcoreapp2.0/FizzBuzz.Tests.dll(.NETCoreApp,Version=v2.0)
Microsoft (R) Test Execution Command Line Tool Version 15.5.0
Copyright (c) Microsoft Corporation.  All rights reserved.

Starting test execution, please wait...
[xUnit.net 00:00:00.9263576]   Discovering: FizzBuzz.Tests
[xUnit.net 00:00:01.0646319]   Discovered:  FizzBuzz.Tests
[xUnit.net 00:00:01.0733357]   Starting:    FizzBuzz.Tests
[xUnit.net 00:00:01.2961789]   Finished:    FizzBuzz.Tests

Total tests: 1. Passed: 1. Failed: 0. Skipped: 0.
Test Run Successful.
Test execution time: 2.5956 Seconds
```

Ok, `dotnet test` does not recognize which project actually contains tests. But it runs all tests!

Let's add a test.

The file `FizzBuzz.Tests/Tests.fs` (generated by `dotnet new xunit...`) looks like this:
```fsharp
module Tests

open System
open Xunit

[<Fact>]
let ``My test`` () =
    Assert.True(true)
```

TDD approach: We will create a failing test first, then implement something.

Replace the content of `FizzBuzz.Tests/Tests.fs` with

```fsharp
module Tests

open System
open Xunit
open FizzBuzz

[<Fact>]
let ``Array with Number 1 returns 'one'`` () =
    let result = FizzBuzz.Generate [1]
    Assert.Equal(result, "one")
```
 
We verify 2 aspects:

- we are invoking another library (`FizzBuzz`) from our test class
- we are learning to use the test library

This does not compile. Let's implement the simplest solution:

Replace `FizzBuzz/Library.fs` with
```
module FizzBuzz

let Generate i = "one"
```

Running `dotnet test` should now confirm 1 passing test.

Have fun with F# on Linux!

Get the source code here: git@github.com:draptik/blog-fsharp-fizzbuzz-setup.git


