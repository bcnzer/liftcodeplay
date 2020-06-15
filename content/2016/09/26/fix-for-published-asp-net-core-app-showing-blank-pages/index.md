---
author: "Ben Chartrand"
title: "Fix for published ASP.NET Core app showing blank pages"
date: "2016-09-26"
tags: [
  'ASP.NET Core'
]
---

Have you deployed an ASP.NET Core application to Azure Web Apps (via Visual Studio Publish)... and now you only see blank pages (no HTML generated at all)? Here's the simple fix

The problem could be that your Views folder is not uploaded because, by default in a blank project, the Views folder is not included in the list of publish options.

To fix it:

- Open **project.json**
- Go to publishOptions and add Views, as seen below
    - If publishOptions isn't there, add it
- Re-deploy your project

{{< gist bcnzer 8804574f3677875c73516c58c861e1f4 >}}

This was a newbie problem that I encountered... As with any solution, it's obvious with hindsight!
