---
author: "Ben Chartrand"
title: "Alternating Row Color in ListView"
date: "2016-02-03"
tags: [
  'Windows 10 UWP'
]
---

Alternating row colours are not there, by default, with the ListView. Here's an easy way to to do it in UWP. I originally found [this page on MSDN](https://msdn.microsoft.com/en-us/library/ms750769(v=vs.85).aspx) but it was old - for .NET 3.0 - and didn't work in UWP. I later found [this article](http://www.bendewey.com/index.php/523/alternating-row-color-in-windows-store-listview) by Ben Dewey which worked. My post is a brief version of Ben's post.

The easiest way to achieve alternating rows is by creating your own ListView class and adding dependency properties to specify the row colours. The dependency properties allow you to define the colours in XAML (i.e. you can set your ThemeResource). You could do without the dependency properties and just hard-code some colours but I wouldn't suggest it.

\[code lang="csharp"\] public class AlternatingRowListView : ListView { public static readonly DependencyProperty OddRowBackgroundProperty = DependencyProperty.Register(&amp;quot;OddRowBackground&amp;quot;, typeof(Brush), typeof(AlternatingRowListView), null); public Brush OddRowBackground { get { return (Brush)GetValue(OddRowBackgroundProperty); } set { SetValue(OddRowBackgroundProperty, (Brush)value); } }

public static readonly DependencyProperty EvenRowBackgroundProperty = DependencyProperty.Register(&amp;quot;EvenRowBackground&amp;quot;, typeof(Brush), typeof(AlternatingRowListView), null); public Brush EvenRowBackground { get { return (Brush)GetValue(EvenRowBackgroundProperty); } set { SetValue(EvenRowBackgroundProperty, (Brush)value); } }

protected override void PrepareContainerForItemOverride(DependencyObject element, object item) { base.PrepareContainerForItemOverride(element, item); var listViewItem = element as ListViewItem; if (listViewItem != null) { var index = IndexFromContainer(element);

if ((index + 1) % 2 == 1) { listViewItem.Background = OddRowBackground; } else { listViewItem.Background = EvenRowBackground; } }

} } \[/code\]

The above code is relatively simple. Based on the index of the row it sets the colour of the row.

To implement this in your XAML, you would do something like this

\[code lang="xml"\] <Page x:Class="MyApp.Views.SimpleView" xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation" xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml" xmlns:d="http://schemas.microsoft.com/expression/blend/2008" xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006" xmlns:controls="using:MyApp.Controls" mc:Ignorable="d">

<Grid> <controls:AlternatingRowListView x:Name="Comps" EvenRowBackground="{ThemeResource MyEvenRowBrush}" OddRowBackground="{ThemeResource MyOddRowBrush}">

<controls:AlternatingRowListView.ItemTemplate> <DataTemplate> ... </DataTemplate> </controls:AlternatingRowListView.ItemTemplate> </controls:AlternatingRowListView> </Grid> </Page> \[/code\]
