---
author: "Ben Chartrand"
title: "Exporting and Importing data to/from Firestore using Google Sheets"
date: "2020-02-07"
image: "header-2.png"
tags: [
  'Firebase'
]
---

Let me show you how I created a Google Sheets script to easily export and import data to/from Firestore.

[Click here to jump straight to the repo with code](https://github.com/bcnzer/firebase-googlesheets-importexport).

## Background

I was watching this excellent video on [Firebase Console Tips & Tricks](https://www.youtube.com/watch?v=E0xofSMYc6Y&list=PLl-K7zZEsYLkkCFs6T9mlqG8v6NCs38pA) by [David East](https://twitter.com/_davideast) and [Sumit Chandel](https://twitter.com/s_chande).

I had no idea you could do this from Sheets! I was really keen to use this approach and extend it so that:

- It grabbed the actual number or rows (the video just grabbed 100)
- It handled updates and deletes (the video only handled creates)
- Also handled imports (video only covered exports)

https://www.youtube.com/watch?v=E0xofSMYc6Y&list=PLl-K7zZEsYLkkCFs6T9mlqG8v6NCs38pA

I had an ideal use for this in my app: something I called **publiclessons**. These are lesson plans any one of my users can access.

I'll be managing this collection and I was already wondering to myself: how will I manage this? I don't really want to write a tool / UI just for this, especially as the data is relatively simple, I'll be the only one using it and the data will change infrequently.

Using a Google Sheet is perfect!

## Summary of what I did

- Opened a Sheet
- Clicked **Tools** \> **Script Editor**
- Typed up that code. In your case, just paste it!
- Followed the quick start mentioned here, to install the library, get my secrets and add the secrets to my script [https://github.com/grahamearley/FirestoreGoogleAppsScript](https://github.com/grahamearley/FirestoreGoogleAppsScript)
    - You'll see several comments with **TODO** that you need to fill out
- Window popped up occasionally asking for permission to connect to my GCP / Firestore instance. I gave it permission
- Made sure to clicked **Save**

## How it works

There's two parts to my script - exports and imports.

### Exports

This is largely based on the content of the video but I took it a bit further. Key things I added were:

- **Column A** is the ID of the doc.
    - If it's blank the document will be added and that field will show the ID from Firestore
    - If it's not blank it will update the document in Firestore
- **Column H** (the far right) I called **toDelete**. If that field is non-empty it will delete the row in Firestore, then the sheet
- The video just grabbed 100 rows. My sheet grabs the actual data in the sheet

### Imports

I added another menu item and added functionality to:

- Import records that don't exist in the sheet to the sheet
- Update existing records in the sheet. The import will match up the properties to the correct column
- Note that it does not handle the situation where the record is not in Firestore but is in the sheet

### Result

This is what it looks like. Nice and easy to use.

[![](https://liftcodeplay.files.wordpress.com/2020/02/annotation-2020-02-08-111011.png?w=1024)](https://liftcodeplay.files.wordpress.com/2020/02/annotation-2020-02-08-111011.png)

Data ready to be exported

[![](https://liftcodeplay.files.wordpress.com/2020/02/after-import.png?w=1024)](https://liftcodeplay.files.wordpress.com/2020/02/after-import.png)

After data was exported to Firestore

## Code

All the code can be found here, in this [repo](https://github.com/bcnzer/firebase-googlesheets-importexport).

Also included is a sample csv field and a [sample sheet of the same data](https://docs.google.com/spreadsheets/d/1XgK3XVURmHPvq0jzkmaNos4Pm_wfN17KfRXBXm7IoQM/edit?usp=sharing).
