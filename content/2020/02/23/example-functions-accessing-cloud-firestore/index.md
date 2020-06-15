---
author: "Ben Chartrand"
title: "Example Functions accessing Cloud Firestore"
date: "2020-02-23"
image: "marc-olivier-jodoin-tquerqguz8-unsplash.jpg"
tags: [
  'Firebase'
]
---

Let me show you and briefly explain how to read / write / update documents in Cloud Firestore from a Function.

Photo by [Marc-Olivier Jodoin](https://unsplash.com/@marcojodoin?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/reflection?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

I wanted to write a function to read some data and then update some other data. The docs aren't clear on how to do it from a function and I struggled to get it working just right.

Below are some examples of read (get), add and update. Delete is more of the same. Also included is an example of how to check the onWrite method to determine what happened to a document.

The key takeaways:

- Lines 1 & 2 - require _firebase-admin_ and _initializeApp_
- When you use class from the require statement (in my case, _admin_), use admin.firestore() and then the syntax from there is the same as in my front end code

{{< gist bcnzer 269969c38443a89be831c07adbe934c4 >}}
