---
author: "Ben Chartrand"
title: "Enabling CORS for Azure Function local development"
date: "2018-09-10"
image: "witcher2.png"
tags: [
  'Azure Functions'
]
---

I had an Azure Function project written in C# (aka Precompiled Azure Function) that worked fine between Visual Studio and Postman. But when I tried to get my local Vue.js project to use it I would get a error, similar to this, in Chrome dev tools:

**Failed to load http://localhost:7071/api/mydata: Response to preflight request doesn't pass access control check: No 'Access-Control-Allow-Origin' header is present on the requested resource. Origin 'http://localhost:8080' is therefore not allowed access. If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.**

Once my app started talking to the API the browser did standard preflight checks and failed because of a lack of access control (CORS).

To add CORS to your local project, open the **local.settings.json** file and add a CORS parameter (see lines 11-13 below). Note that this file is only used for local development, so I don't mind being lazy and using \* as the CORS hostname.

{{< gist bcnzer afabdf0be17fed2174e0aaa2c10789e6 >}}

If you want to make a corresponding/similar change online you need to do so via the UI of the Azure Portal but I would strongly suggest you enter the hostname(s) the API will be accessed from. Don't put in star!
