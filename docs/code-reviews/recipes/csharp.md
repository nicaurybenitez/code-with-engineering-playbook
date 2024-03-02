---
layout: default
title: csharp
nav_order: 1
has_children: false
---

# C# Code Reviews

## Style Guide

Developers should follow Microsoft's [C# Coding Conventions](https://learn.microsoft.com/dotnet/csharp/fundamentals/coding-style/coding-conventions) and, where applicable, Microsoft's [Secure Coding Guidelines](https://learn.microsoft.com/dotnet/standard/security/secure-coding-guidelines).

## Code Analysis / Linting

We strongly believe that consistent style increases readability and maintainability of a code base. Hence, we are recommending analyzers / linters to enforce consistency and style rules.

### Project Setup

We recommend using a common setup for your solution that you can refer to in all the projects that are part of the solution. Create a `common.props` file that contains the defaults for all of your projects:

```xml
<Project>
...
    <ItemGroup>
        <PackageReference Include="Microsoft.CodeAnalysis.NetAnalyzers" Version="5.0.3">
          <PrivateAssets>all</PrivateAssets>
          <IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>
        </PackageReference>
        <PackageReference Include="StyleCop.Analyzers" Version="1.1.118">
          <PrivateAssets>all</PrivateAssets>
          <IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>
        </PackageReference>
    </ItemGroup>
    <PropertyGroup>
        <TreatWarningsAsErrors>true</TreatWarningsAsErrors>
    </PropertyGroup>
    <ItemGroup Condition="Exists('$(MSBuildThisFileDirectory)../.editorconfig')" >
        <AdditionalFiles Include="$(MSBuildThisFileDirectory)../.editorconfig" />
    </ItemGroup>
...
</Project>
```

You can then reference the `common.props` in your other project files to ensure a consistent setup.

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
  <Import Project="..