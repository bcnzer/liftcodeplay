---
author: "Ben Chartrand"
title: "Design Pattern: Blades for Sequential Data Entry (Wizards) in UWP"
date: "2015-11-11"
image: "blocks-1.png"
---

Looking for a design pattern that allows you to enter data in a specific sequence - like a wizard in UWP? Checkout this "blade" based system! I'll be discussing how it works at a high level. But first, a demo!

#### Demo

\[caption id="attachment\_266" align="aligncenter" width="660"\][![Demonstration of a simple blade system for entering ](https://liftcodeplay.files.wordpress.com/2015/11/blade-demo.gif?w=660)](https://liftcodeplay.files.wordpress.com/2015/11/blade-demo.gif) Demonstration of a simple blade system for entering "blocks". Click on the image to view a higher-quality animated GIF\[/caption\]

#### Update 19 Aug 2016

A better version of my control, which is independent of Caliburn Micro, has included in the UWP Community Toolkit.

I would strongly suggest you use that version insted of this.

[https://github.com/Microsoft/UWPCommunityToolkit](https://github.com/Microsoft/UWPCommunityToolkit)

#### Background

When I started designing my UWP I realized I had a need for something akin to a wizard - much like an installation program. Based on how the user would answer a series of questions, I would then ask a series of different questions in the next step. The logic could be complicated as would be the requirements for completing any one step.

I was also after an alternative to the DataGrid, as documented in my post [DataGrid Alternatives in UWP](http://liftcodeplay.com/2015/10/24/datagrid-alternatives-in-uwp/). The last point, where I mentioned the master-detail pattern, really stuck with me and I sought out examples.

Lastly, I wanted something that would work well on a large resolution. Doing it on the phone is relatively easy - just navigate between whole pages. The same could be done on the desktop or larger res but it likely wouldn't look all that flash.

#### My Inspiration - the Azure Portal!

The new Azure Portal uses a system called Blades. Each blade can lead to another blade, based on what you select. The example below has four blades plus a hamburger menu that is minimized on the far left. The workflow is from left to right.

\[caption id="attachment\_270" align="aligncenter" width="660"\][![Samples Azure Portal. Notice the hamburger menu (minimized) on the left as well as the four ](https://liftcodeplay.files.wordpress.com/2015/11/azure-portal.png?w=660)](https://liftcodeplay.files.wordpress.com/2015/11/azure-portal.png) Samples Azure Portal. Notice the hamburger menu (minimized) on the left as well as the four "blades" - the columns\[/caption\]

The concept behind the blades is that you can constantly expand out to the right based on your decisions. Each time it expands out, you can enter specific data. Note the lack of ComboBoxes. Instead, a new blade pops up. Some blades can be expanded / maximized.

Benefits of this model:

- See the overall flow of your decisions
- Can change a decision easily at any point
- Support for a large number of blades. Great for processes with numerous steps / options
- Can be used for data entry
- Where we'd previous use a combo box - we can instead pop out a blade which shows the options in more detail. With combo boxes, you need to click to expand them to see the options. You could also get creative here i.e. if you maximize a blade, perhaps each item can take more space to show the content better?
- Can be mobile friendly

#### How I Implemented This

In this post I want to explain how I implemented this at a high level. In a future post (once my application is further along), I'll share my code.

This blog post assumes an understanding of MVVM. I'm using [Caliburn Micro](http://caliburnmicro.com/). You can likely accomplish this with some other MVVM framework.

#### Step 1 - Shell View

The first thing we need is a Shell View. This is the container that stores all the blades.

In the view (the user interface) I'm using a GridView setup horizontally. Simple stuff.

In my view model I'll be injecting the view model for each blade.

\[code language="xml"\]

<Page x:Class="MyApp.Views.BladeShellView" xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation" xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml" xmlns:d="http://schemas.microsoft.com/expression/blend/2008" xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006" mc:Ignorable="d">

<Grid> <ScrollViewer HorizontalScrollBarVisibility="Auto" VerticalScrollBarVisibility="Auto"> <GridView x:Name="Blades" HorizontalContentAlignment="Left" VerticalContentAlignment="Top" VerticalAlignment="Stretch" HorizontalAlignment="Left" Width="Auto" SelectionMode="None" ItemContainerStyle="{StaticResource GridViewDisablePressAnimationStyle}" >

<GridView.ItemsPanel> <ItemsPanelTemplate> <StackPanel Orientation="Horizontal" /> </ItemsPanelTemplate> </GridView.ItemsPanel>

</GridView> </ScrollViewer> </Grid> </Page> \[/code\]

#### Step 2 - Define each blade

Each view is a XAML Page and has an accompanying View Model. Create whatever you like it.

I was able to have a high level of code use by using ListView/GridView and injecting ViewModels. And by "injecting", I mean creating a BindableCollection of my view models for the horizontal GridView in my shell.

\[caption id="attachment\_281" align="aligncenter" width="660"\][![The three blades in my simple blocks example](https://liftcodeplay.files.wordpress.com/2015/11/blocks-2.png?w=660)](https://liftcodeplay.files.wordpress.com/2015/11/blocks-2.png) The three blades in my simple blocks example. The Expander is actually a ListView with a single item. In my program I have lists of multiple expandable selections\[/caption\]

#### Step 3 - Adding VMs and Inter-blade Communication

The problem we have is that behaviour in a VM (ViewModel) may need to affect another VM or the shell. For example:

- If I press the Close icon on the second blade, I also need it to close every other blade to the right
- If I only had the second blade open and then I clicked the colours expander, I'd want a new blade opened
- If I did select a new colour and pressed Select, I'd want the resulting colour to show up in the expander item of my second blade

I handled inter-VM communication using Caliburn Micro's EventAggregator. I created generic Open and Close event classes. I then told my Shell VM to handle both and for my other blade VMs to handle them as necessary.

\[code language="csharp"\] namespace SpotterPowerlifting.EventAggregator { ///

&amp;amp;lt;summary&amp;amp;gt; /// Event raised when we want to open another blade /// &amp;amp;lt;/summary&amp;amp;gt;

public class BladeOpenEvent { public enum BladeType { &amp;amp;lt;My Blade Types&amp;amp;gt; }

public BladeOpenEvent(BladeType newBlade, BladeType calledFromBladeType) { NewBladeType = newBlade; CalledFromBladeType = calledFromBladeType; }

public BladeType NewBladeType { get; private set; } public BladeType CalledFromBladeType { get; private set; }

#region Optional objects which we may be trying to setup using the blades

public Block CurrentBlock { get; set; }

#endregion } } \[/code\]

The neat thing about EventAggregator is that, once you have sent an event, it gets handled by whomever is setup to listen/handle the event - potentially by multiple VMs. For example: my second blade is setup to handle the Close event, but only to know a selection has been done in the colours expander. The Shell VM is primarily interested opening/closing of blocks.

#### Wrapping Up

By using the GridView, I automatically get transition effects as I add or remove blades. I can customize these but out-of-the-box they're pretty good.

I also have a style applied to my grid to disable the selection of the individual blade, as if it was a single item.

If I wanted to make a mobile friendly version of my app, I would simple display my blades as single pages and navigate back and forth. I'll touch on this in a later blog post.

Overall, I've found this a very powerful pattern and one I'm using extensively in my UWP. Best of all - it's allowed me a high level of code reuse, as I can inject VMs into ListViews/GridViews.

EDIT 18 Jan 2016 - [Click here to view to get the source code](http://liftcodeplay.com/2016/01/18/code-sample-for-the-blade-design-pattern/).
