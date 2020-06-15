---
author: "Ben Chartrand"
title: "Using ASP.NET Core and JQuery to call methods via AJAX"
date: "2017-02-14"
image: "asp-core-jquery.png"
tags: [
  'ASP.NET Core'
]
---

Would like to use AJAX to call a method in your ASP.NET Core app / web service? In this demo we’ll walk through all the details – including how to make use of ValidateAntiForgeryToken.

## Update: 25 Nov 2017

User **tewux** in the comments, below, pointed out there's a simpler way of accessing the anti-forgery token. Checkout the document from Microsoft:

[https://docs.microsoft.com/en-us/aspnet/core/security/anti-request-forgery#javascript-ajax-and-spas](https://docs.microsoft.com/en-us/aspnet/core/security/anti-request-forgery#javascript-ajax-and-spas)

Thank you tewux!

## Prerequisite knowledge

I'm assuming you have a basic understanding of:

- [ASP.NET Core](https://docs.microsoft.com/en-us/aspnet/core/)
- [HTTP GET and POST](http://www.w3schools.com/Tags/ref_httpmethods.asp)
- [AJAX](http://www.w3schools.com/xml/ajax_intro.asp)
- [\[ValidateAntiForgeryToken\] attribute in ASP](http://www.c-sharpcorner.com/article/purpose-of-validateantiforgerytoken-in-mvc-application/)

## Problem we're trying to solve

We are writing a form to collect book details, for a library management system. We have a drop down list called **Storage Location**. The options available depend on **Hardcover** (yes/no).

When a user enters/changes the hardcover option we want to get the storage locations using an AJAX call.

### Storage location logic

For the purpose of this simple example, here’s how I am storing the different combinations or hardcover and storage locations (in code).

https://gist.github.com/bcnzer/617fd48e0de118a75bbfc86e69703a2c

Example: if it’s a hardcover book the options would be: General Circulation, Backroom and Offsite storage.

## ASP.NET Core method

We need a URL for our AJAX call. I’m choosing to add a method to class **BookDetailsController**.

https://gist.github.com/bcnzer/6631631ac9281ac455d89f92e867a16b

The code uses LINQ to get the data from my static list of locations. Perhaps in real-life this statement would be a query using Entity Framework.

I chose to use a POST request. I could have used GET, which would have made it easier to test the URL as I could pass a URL such as this: **https://localhost:44382/bookdetails/storagelocations?ishardcover=true**

Lastly, I am using the **ValidateAntiForgeryToken** attribute. We will need to cater for that in our AJAX call. Within that form is **@Html.AntiForgeryToken()**. This is what will generate the token. Looks something like this…

https://gist.github.com/bcnzer/aa7726a02a58687f7a389b995208f8d7

Note that the @Html.AntiForgeryToken() method produces HTML that looks something like this. Note the name **\_\_RequestVerificationToken**

> <input name="\_\_RequestVerificationToken" type="hidden" value="CfDJ8EtPyYIqlaFOgtiW4jAOXuSK5rEvXYi5CM-OFWETtxeW34juJ-ugaxrbR-xb2o5NaqcgjmST\_lzBevHVxUQ\_2g6ILNkg\_5J0mLcQIJMJZxPFK8vBCH3grLwnLjC8UhWB\_hAP21me-\_1wxdbyFS28\_\_d-8ox4aSIh\_CLXMAWT5ejXUqsit6gDyRM8AOsx\_eJI2Q">

## JQuery AJAX call

The actual AJAX call will be handled by JQuery, which comes by default in the sample ASP Core project. Here’s what the code looks like:

https://gist.github.com/bcnzer/b6ff241080d34db9eff03a601d7a1876

Breaking down this code, the first thing we need to do is get the parameters. We need to send:

- isHardcover – true or false
- \_\_RequestVerificationToken from our form. We need to pass this because of the \[ValidateAntiForgeryToken\] attribute

That’s what lines 4-6 do.

Afterwards we setup the AJAX call. Note the success function starting in line 12.

Lastly, we want this code to fire whenever the selection in the Hardcover method is changed. Hence the line of code at line 24.

Hope this helps.
