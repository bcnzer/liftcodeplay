---
author: "Ben Chartrand"
title: "XAML Line Element"
date: "2015-08-29"
---

A quick article explaining how to use the <Line> XAML element. It's something that bugged me and took longer than it should have.

I wanted to draw a simple horizontal line. It's only when I read [the Microsoft documentation](https://msdn.microsoft.com/en-us/library/windows/apps/xaml/hh465055.aspx#line) that I realised a few key points.

- You must specify a StrokeThickness.
- You must specify a Stroke (i.e. White). Don't bother with Fill
- Width and Height specify the space your line is in
- The coordinates (X1, X2, Y1, Y2) define the detail of your line within the space

If you want a simple horizontal line in red, you can do this:

\[code lang="xml"\] <Line Stroke="Red" StrokeThickness="3" X2="200" /> \[/code\]

And here's an example of a more complicated line, more akin to the slash / character:

\[code lang="xml"\] <Line Width="100" Height="100" X1="10" X2="90" Y1="90" StrokeThickness="3" Stroke="Red" /> \[/code\]
