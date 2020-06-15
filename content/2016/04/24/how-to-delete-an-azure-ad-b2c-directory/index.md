---
author: "Ben Chartrand"
title: "How to Delete an Azure AD B2C Directory"
date: "2016-04-24"
tags: [
  'Azure'
]
---

Do you get a message saying "Directory has one or more applications that were added by a user or administrator."? Here's how to fix it

There's an application associated automatically by Azure AD B2C. Here's what you need to do to get rid of it:

- Log into the Classic Azure Management portal
- Find the directory that you want to delete
- Click the **Applications** tab
- In the drop down select **Applications my company owns** and press search
- You will notice there is an item there called B2c extensions. Delete it
- You can now delete the AD directory!
