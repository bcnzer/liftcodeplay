---
author: "Ben Chartrand"
title: "Referencing Frameworks for NuGet Packages in .NET Core"
date: "2016-11-18"
tags: [
  'ASP.NET Core'
]
---

Do you have a .NET Core project (i.e. ASP .NET Core 1.0 or 1.1) where, the moment you add a NuGet package such as Windows.Storage or Microsoft.AspNetCore.AzureAppServicesIntegration, you get a bunch of NuGet restore errors? Here's how you can fix them - by referencing the frameworks they require

Package Microsoft.Data.OData 5.6.4 is not compatible with netcoreapp1.1 (.NETCoreApp,Version=v1.1). Package Microsoft.Data.OData 5.6.4 supports:
      - net40 (.NETFramework,Version=v4.0)
      - portable-net40+sl5+win8+wp8+wpa81 (.NETPortable,Version=v0.0,Profile=Profile328)
      - portable-net45+win8+wp8+wpa81 (.NETPortable,Version=v0.0,Profile=Profile259)
      - sl4 (Silverlight,Version=v4.0)
    Package Microsoft.Data.Services.Client 5.6.4 is not compatible with netcoreapp1.1 (.NETCoreApp,Version=v1.1). Package Microsoft.Data.Services.Client 5.6.4 supports:
      - net40 (.NETFramework,Version=v4.0)
      - portable-net45+win8+wp8+wpa81 (.NETPortable,Version=v0.0,Profile=Profile259)
      - sl4 (Silverlight,Version=v4.0)
    Package System.Spatial 5.6.4 is not compatible with netcoreapp1.1 (.NETCoreApp,Version=v1.1). Package System.Spatial 5.6.4 supports:
      - net40 (.NETFramework,Version=v4.0)
      - portable-net40+sl5+win8+wp8+wpa81 (.NETPortable,Version=v0.0,Profile=Profile328)
      - portable-net45+win8+wp8+wpa81 (.NETPortable,Version=v0.0,Profile=Profile259)
      - sl4 (Silverlight,Version=v4.0)
    Package Microsoft.Data.Edm 5.6.4 is not compatible with netcoreapp1.1 (.NETCoreApp,Version=v1.1). Package Microsoft.Data.Edm 5.6.4 supports:
      - net40 (.NETFramework,Version=v4.0)
      - portable-net40+sl5+win8+wp8+wpa81 (.NETPortable,Version=v0.0,Profile=Profile328)
      - portable-net45+win8+wp8+wpa81 (.NETPortable,Version=v0.0,Profile=Profile259)
      - sl4 (Silverlight,Version=v4.0)
    One or more packages are incompatible with .NETCoreApp,Version=v1.1

In .NET Core, in the project.json, there's this section called **frameworks.** The most important reference is here is to the version of .NET Core you are using i.e. 1.1.0.

Also here is the **Imports** section. This is where you can reference other frameworks.

The reason packages like Windows.Storage fail is because they depend on packages on other packages, such as Microsoft.Data.OData, that target different frameworks. You just need to add a reference to them and it should burst into life.

Here's the portion of my project.json file, which I used to get Windows.Storage and Microsoft.AspNetCore.AzureAppServicesIntegration working.

    "frameworks": {
        "netcoreapp1.1": {
            "dependencies": {
                "Microsoft.NETCore.App": {
                    "type": "platform",
                    "version": "1.1.0"
                }
            },
            "imports": \[
                **"dotnet5.6",**
                "dnxcore50",
                **"portable-net45+win8"**
            \]
        }
    },
