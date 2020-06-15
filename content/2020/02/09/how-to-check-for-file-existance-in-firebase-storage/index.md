---
author: "Ben Chartrand"
title: "How to check for File Existance in Firebase Storage"
date: "2020-02-09"
image: "header-6.png"
tags: [
  'Firebase'
]
---

I wanted to check to see if a file exists in storage... but there's no method that allows me to do this. Let me show you some work-arounds.

I'm going to assume we're programming for the web but the methods should be similar in other target environments, like Android.

## Method 1: getDownloadURL

In the documentation, under the [Download Files](https://firebase.google.com/docs/storage/web/download-files) section, there's information about a `getDownloadURL()`. [Checkout here specifically in the docs](https://firebase.google.com/docs/storage/web/download-files#download_data_via_url).

Assuming you specify a path in code, you can call `getDownloadURL` to get you the full, HTTP path.

What you can do is:

1. Enter the filename you're after
2. Check for an error/exception. An error means it doesn't exist, otherwise it does

The downside to this approach is that, if the file doesn't exist, there'll be a 404 error in your console / network tab. Not really a problem... but it does irk me.

Not that `getMetadata` also works and has the same problem.

https://gist.github.com/bcnzer/e36462c8afad11e95af6dff1502faba9

## Method 2: list() method

You can run a [list or listAll](https://firebase.google.com/docs/storage/web/list-files) command.

Unfortunately `listAll` returns everything and is only suitable for small directories and `list` is paginated. You can't filter with either.

I'd only use this option if you knew there was only a few files. Wait a second...

## Method 3: use list() and use folders

### Some quick background

I'm storing a list of screenshots.

There can be one screenshot per document in my Cloud Firestore database (i.e. document `Te7xDpv5f9l7A2mC9s0F` might have a corresponding `Te7xDpv5f9l7A2mC9s0F.png`).

I wanted to confirm the file exists so, instead of trying to load it, I can display a placeholder image instead.

### Solution

I was initially storing them all the screenshots in a `screenshots` folder.

I instead switched to creating a sub-folder for each, like you see in these pictures. Note that I am using the GCP user interface as I prefer it. You can use the UI provided by Firebase.

[![](https://liftcodeplay.files.wordpress.com/2020/02/img1.png?w=794)](https://liftcodeplay.files.wordpress.com/2020/02/img1.png)

[![](https://liftcodeplay.files.wordpress.com/2020/02/img2.png?w=799)](https://liftcodeplay.files.wordpress.com/2020/02/img2.png)

The result was simpler code, I don't risk pulling lots of data, paginating, etc. Also made more sense to put them in separate folders as I might, in the future, have `n` associated to each document.

https://gist.github.com/bcnzer/9f38b2f9f1ed8182eaa843a337ef8535

[![](https://liftcodeplay.files.wordpress.com/2020/02/console.png?w=1024)](https://liftcodeplay.files.wordpress.com/2020/02/console.png)

Console output showing you the details. Click on it to see a bigger version
