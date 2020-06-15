---
author: "Ben Chartrand"
title: "ASP.NET Core: Partials versus Sections"
date: "2017-07-01"
image: "asppartialvssectionsheader.png"
tags: [
  'ASP.NET Core'
]
---

Not immediately clear what the difference is between a Partial view and a Section is in ASP.NET Core? Let me try to explain.

The official documentation on [Partials](https://docs.microsoft.com/en-us/aspnet/core/mvc/views/partial) and [Sections](https://docs.microsoft.com/en-us/aspnet/core/mvc/views/layout) is pretty good. It had been a few weeks since I worked in ASP.NET Core and I found myself in a piece of code, late at night, that left me mildly confused.

I'm partially writing this purely for my benefit - in the hope the info will stick in my brain.

## Example

Let's say you are confronted with the following code. See lines 42 and 43. What is it doing? Why? How do use it / what do I need to do in other views?

{{< gist bcnzer 4a0a92cf9449b4ed4dee5e5d1b4af0f9 >}}

## Partials

As the [ASP.NET Core documentation](https://docs.microsoft.com/en-us/aspnet/core/mvc/views/partial) says:

> A partial view is a view that is rendered within another view.

What I've done is intentionally chosen to breakup all the code in my \_Layout.cshtml into a series of smaller views. I'm doing this for two reasons:

1. In the hope of future code-reuse
2. To make it easier on myself. My real-life \_Layout.cshtml is pretty big so, by having less code, I can get a better sense of the 'big picture' layout

To use a partial I simply call @Html.Partial with the name to my file.

### So Line 42 means...

This line means I'm adding the \_Scripts.cshtml view to the layout page. Everything in the file gets automatically placed into the \_Layout.cshtml, which then makes up a standard page.

And if you're curious: I'm using \_Scripts.cshtml to references the standard Javascript files used by all pages.

## Sections

The simplest way to describe a section is:

- you define **where** content with that section name should go
- it's **up to you**, on each page, to define precisely what goes into the section
- sections can be optional or mandatory. You can also choose to ignore them

Firstly, checkout the [ASP.NET Core documentation on Layout](https://docs.microsoft.com/en-us/aspnet/core/mvc/views/layout). If it all makes sense, cool. But I think I've got a more succient example to explain it.

### Example use case for a Section

In the example below each page has a header and then a content breadcrumb, to tell users where they are.

I want this to always be at the top of my content page but, in some cases (i.e. login and registration page), I want to explicitly ignore that section.

[![SectionExample.png](images/sectionexample.png)](https://liftcodeplay.files.wordpress.com/2017/07/sectionexample.png)

The code, in \_Layout.cshtml, would look something like this. Note that I called my section **MyContentHeader**.

{{< gist bcnzer 93d0e25b02e6218dac68626acf73014d >}}

What this means is that, in the next page I create, ASP.NET Core is expecting a section called MyContentHeader. If it doesn't find it an exception will be thrown.

In my Dashboard cshtml file I'd have something like this:

{{< gist bcnzer 5c82f53d8df099d4eabb89bf3b19419c >}}

If I wanted to ignore that section for the Login / Registration pages I can call @**IgnoreSection("MyContentHeader")**

### So what about Line 43?

It's saying that "scripts" should be at the bottom, below my standard JS libraries. It's also saying it's optional so no biggie if I don't use it.

This makes sense because, on some pages, I will want to have my Javascript code for that page and maybe reference additional libraries only used on that page. I can put it all in the **@section Scripts { ... }** area.

Going back to the definition of Sections: it originally defined where it will go in the layout so, in the file I'm working on (i.e. dashboard.cshtml) I'm not stressed about where to put **@section Scripts { ... }** . It could be anywhere in dashboard.cshtml!

## Thanks for reading

And it looks like the process of writing this blog post has really helped it stick in my brain.

I'm hoping this was of help to someone else. If so, add a comment!
