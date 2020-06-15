---
author: "Ben Chartrand"
title: "Setting up Moq with your .NET Core test project"
date: "2017-01-02"
image: "moq.png"
tags: [
  'ASP.NET Core'
]
---

Keen to use Moq in your .NET Core project (i.e. ASP.NET Core and XUnit)? Here's how to get setup.

## Background

Moq is a mocking framework for .NET. If you'd like to learn more about Moq, check out their [GitHub page](https://github.com/moq/moq4).

## Adding Moq in your Test Project

I'm assuming you have a test project using your favourite unit test framework such as XUnit, NUnit, MSTest, etc. I'm using XUnit.

### Step 1: Add the Moq NuGet package

As of this writing, only the pre-release version supports .NET Core and the current version is 4.6.38-alpha

Install-Package Moq -prerelease

### Step 2: Add TraceSource package

This is an important step - one that caused me a lot of pain. It's required but not set as a dependency. Without it you will get IO File exceptions when you try to use a mocked object.

Install-Package System.Diagnostics.TraceSource -version 4.0.0

## Complete project.json File

The following is a complete example of the project.json.

{{< gist bcnzer 7063ef232a3823d9468734ba1b5f3b19 >}}

Note that this project.json belongs to a project running .NET Core 1.1, hence the netcoreapp1.1 entry.

{{< gist bcnzer 7063ef232a3823d9468734ba1b5f3b19 >}}
