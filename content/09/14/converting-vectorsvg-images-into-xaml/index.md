---
author: "Ben Chartrand"
title: "Converting Vector/SVG images into XAML"
date: "2015-09-14"
---

A [vector image](https://en.wikipedia.org/wiki/Vector_graphics) is a geometric representation of an image. SVG is a common file format for vectors. Here's how you can convert the contents of your SVG into XAML.

#### Why convert to XAML?

You can't use an SVG file directly within your XAML. The image tags don't allow you to load it. There are third party packages that can allow you to use it but personally I've never bothered.

Odds are you are here because you either have an SVG you want to convert or you want to create one.

What you ultimately want to get is XAML something like this - a path with data. You can then stick it into a canvas, viewbox, etc.

\[code language="xml"\] <ResourceDictionary xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml" xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"> <Canvas x:Key="black\_appbar\_globe" Width="76" Height="76" Clip="F1 M 0,0L 76,0L 76,76L 0,76L 0,0"> <Path Width="44.3333" Height="44.3333" Canvas.Left="15.8333" Canvas.Top="15.8333" Stretch="Fill" Fill="Black" Data="F1 M 38,15.8333C 50.2423,15.8333 60.1667,25.7577 60.1667,38C 60.1667,50.2423 50.2423,60.1667 38,60.1667C 25.7577,60.1667 15.8333,50.2423 15.8333,38C 15.8333,25.7577 25.7577,15.8333 38,15.8333 Z M 38,19C 37.0389,19 36.0944,19.0714 35.1716,19.2091C 34.9741,19.6392 34.8333,20.1113 34.8333,20.5833C 34.8333,22.1667 33.25,19 31.6667,22.1667C 30.0833,25.3333 31.6667,22.1667 31.6667,25.3333C 31.6667,26.9167 29.3752,25.771 30.0833,26.125C 31.6667,26.9167 31.6667,26.9167 30.0833,28.5C 30.0833,28.5 26.9167,26.9167 26.9167,28.5C 26.9167,30.0833 25.3333,30.0833 23.75,30.0833C 22.1667,30.0833 23.75,33.25 22.1667,33.25C 20.9493,33.25 21.6039,31.3779 20.5322,30.5126C 20.1248,31.4618 19.7925,32.4508 19.5428,33.4722C 20.6099,34.4283 20.7886,38.2053 22.1667,39.5834C 23.75,38 23.75,39.5834 25.3333,39.5834C 26.9167,39.5834 26.9167,39.5834 27.7083,41.1667C 29.2917,41.1667 30.0833,42.75 31.6667,44.3333C 33.25,45.9167 36.4166,45.9167 36.4166,47.5C 36.4166,49.0833 34.8333,47.5 34.8333,50.6667C 34.8333,52.25 34.8333,52.25 33.25,52.25C 32.2531,52.25 31.2561,54.1331 30.6544,55.528C 32.9142,56.4761 35.3959,57 38,57C 43.3179,57 48.1255,54.8153 51.5742,51.2944L 50.6666,49.4792C 50.6666,49.4792 52.6458,46.3125 51.0625,44.7292C 49.4791,43.1459 49.4791,41.5625 49.4791,41.5625C 49.4791,41.5625 46.3125,44.7292 44.7291,43.1458C 43.1458,41.5625 43.1458,43.1458 41.5625,39.9792C 39.9791,36.8125 43.1458,35.2292 43.1458,35.2292C 43.1458,35.2292 43.1458,32.0625 44.7291,32.0625C 46.3125,32.0625 47.8958,28.8959 51.0625,32.0625C 51.0625,32.0625 52.8924,30.8426 55.4814,30.5444C 54.6693,28.6428 53.5561,26.9006 52.2016,25.3777C 51.9172,25.5822 51.545,25.7292 51.0625,25.7292C 49.4791,25.7292 52.6458,28.8959 51.0625,28.8959C 49.4791,28.8959 49.4791,27.3125 47.8958,27.3125C 46.3125,27.3125 46.3125,28.8959 44.7291,30.4792C 43.1458,32.0625 44.7291,30.4792 43.1458,28.8959C 41.5625,27.3125 46.3125,28.8959 44.7291,27.3125C 43.1458,25.7292 46.3125,25.7292 46.3125,24.1459C 46.3125,22.904 48.2605,22.6362 49.1008,22.5784C 48.187,21.9195 47.2124,21.3398 46.3125,20.9792C 47.8958,22.5625 44.7291,24.1459 43.1458,24.1459C 41.6585,24.1459 42.9653,21.3518 43.1294,19.7005C 41.4977,19.2441 39.7773,19 38,19 Z M 19,38C 19,43.5885 21.4127,48.6134 25.2533,52.09L 23.75,49.0833C 22.1667,49.0833 21.375,45.5209 21.375,43.9375C 21.375,42.6669 20.8651,41.6512 21.4821,40.4812C 19.2482,38.2376 20.5833,39.454 20.5833,38C 20.5833,37.2463 19.8657,36.4925 19.1137,35.9096C 19.0385,36.5961 19,37.2935 19,38 Z "/> </Canvas> </ResourceDictionary> \[/code\]

#### Creating an SVG

If you want to create an SVG, there are a bunch of tools available. A nice free option is [Inkscape](https://inkscape.org/). You can easily create an image or load one, then save it as an SVG. You can also export to XAML (more on that in a moment).

\[caption id="attachment\_170" align="aligncenter" width="300"\][![Sample image in Inkscape](https://liftcodeplay.files.wordpress.com/2015/09/uwp-inkscape1.png?w=300)](https://liftcodeplay.files.wordpress.com/2015/09/uwp-inkscape1.png) Sample image in Inkscape\[/caption\]

[You can download the above SVG file here](http://1drv.ms/1ipmUid).

Note that the above image consists of only three lines. It's really easy / simple. That won't be the case for all SVGs!

There are several methods available. None of them are perfect.

#### METHOD 1 - opening the SVG File

An SVG is an XML file format. You can open it in your favourite text editor. If you were to open the file and scroll down, checkout the content below.

\[code language="xml"\] <g transform="translate(0,0)" id="layer1"> <path id="path3388" d="m 15.6,5 c 0,40 0,40 0,40" style="fill:none;fill-rule:evenodd;stroke:#000000;stroke-width:1;stroke-linecap:butt;stroke-linejoin:miter;stroke-opacity:1;stroke-miterlimit:4;stroke-dasharray:none;stroke-dashoffset:0" /> <path id="path3390" d="m 35,5 c 0,40 0,40 0,40" style="fill:none;fill-rule:evenodd;stroke:#000000;stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;stroke-opacity:1" /> <path id="path3392" d="m 2,15 c 46,0 46,0 46,0" style="fill:none;fill-rule:evenodd;stroke:#000000;stroke-width:1px;stroke-linecap:butt;stroke-linejoin:miter;stroke-opacity:1" /> </g> \[/code\]

The data you want is in the d properties of the path. The simplest thing to do is copy and paste it out. Note that I had to keep the F1 in front of my paths

\[code language="xml"\] <Path Data="F1 m 15.6,5 c 0,40 0,40 0,40" Stroke="Black" StrokeThickness="1" /> <Path Data="F1 m 35,5 c 0,40 0,40 0,40" Stroke="Black" StrokeThickness="1" /> <Path Data="F1 m 2,15 c 46,0 46,0 46,0" Stroke="Black" StrokeThickness="1" /> \[/code\]

#### Method 2 - Using Inkscape

The steps are: 1) Open your SVG in Inkscape 2) File -> Save As... 3) In the file types, select Microsoft XAML (near the bottom) 4) In the window you have the option of Silverlight compatible. I'd select as it generates cleaner XML

Note that you probably still have some cleanup to do. I'd suggest removing unnecessary tags and properties - especially the xmlns:x property in each element. \[code language="xml"\] <?xml version="1.0" encoding="UTF-8"?> <!--This file is compatible with Silverlight--> <Canvas xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation" Name="svg3350" Width="50.000001" Height="50.000001"> <Canvas.RenderTransform> <TranslateTransform X="0" Y="0"/> </Canvas.RenderTransform> <Canvas.Resources/> <!--Unknown tag: sodipodi:namedview--> <!--Unknown tag: metadata--> <Canvas Name="layer1"> <Canvas.RenderTransform> <TranslateTransform X="0" Y="0"/> </Canvas.RenderTransform> <Path xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml" Name="path3388" StrokeThickness="1" Stroke="#FF000000" StrokeMiterLimit="4" StrokeLineJoin="Miter" StrokeStartLineCap="Flat" StrokeEndLineCap="Flat" Data="m 15.6 5 c 0 40 0 40 0 40"/> <Path xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml" Name="path3390" StrokeThickness="1" Stroke="#FF000000" StrokeLineJoin="Miter" StrokeStartLineCap="Flat" StrokeEndLineCap="Flat" Data="m 35 5 c 0 40 0 40 0 40"/> <Path xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml" Name="path3392" StrokeThickness="1" Stroke="#FF000000" StrokeLineJoin="Miter" StrokeStartLineCap="Flat" StrokeEndLineCap="Flat" Data="m 2 15 c 46 0 46 0 46 0"/> </Canvas> </Canvas> \[/code\]

#### Method 3 - XPS format

Bit of a roundabout method but an interesting one which generates cleaner code.

XPS format is (or was?) an alternative to PDF. You will often see it available as a virtual printer.

An XPS is a zip file containing a bunch of files. The data is very similar to XAML.

Steps: 1) Print your SVG to the XPS printer 2) The XPS printer will ask you for a file name. Save it somewhere 3) Rename the file extension to .ZIP 4) Extract all the files in the zips. It will create a bunch of files and folders 5) Hunt down the file. It's likely under **Documents > 1 > Pages > 1.fpage**

Once you find the code you will be rewarded with some nice, clean code. You could take those Path items out and past them right in and it would work in XAML.

\[code language="xml"\] <FixedPage Width="53.28" Height="53.28" xmlns="http://schemas.openxps.org/oxps/v1.0" xml:lang="und"> <!-- Microsoft XPS Document Converter (MXDC) Generated! Version: 0.3.10240.16384 --> <Path Data="F1 M 16.64,5.28 L 16.64,48" Stroke="#ff000000" StrokeThickness="0.96" StrokeLineJoin="Miter" StrokeMiterLimit="4" /> <Path Data="F1 M 37.28,5.28 L 37.28,48" Stroke="#ff000000" StrokeThickness="0.96" StrokeLineJoin="Miter" StrokeMiterLimit="4" /> <Path Data="F1 M 2.08,16 L 51.2,16" Stroke="#ff000000" StrokeThickness="0.96" StrokeLineJoin="Miter" StrokeMiterLimit="4" Clip="M 0,13.44 L 53.28,13.44 53.28,18.72 0,18.72 z" /> </FixedPage> \[/code\]

#### Conclusion

There we go - an SVG converted into Paths we can use in our XAML!
