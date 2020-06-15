---
author: "Ben Chartrand"
title: "Adding Design-Time data for UWP and Caliburn Micro"
date: "2015-10-30"
---

I'll demonstrate how you can do this in UWP and Caliburn Micro. It's easy to do and makes life so much easier when trying to design content in something like a ListView,

This blog post assumes you understand the basics of Caliburn Micro.

#### View Model

Let's assume we have a View Model like this. The key thing to point out is the statement with if(Execute.InDesignMode). Within here we are creating some sample data.

\[code language="csharp"\] public class TeamSetupViewModel : Screen { public TeamSetupViewModel() { if(Execute.InDesignMode) { Teams = new BindableCollection<Team>() { new Team() {TeamName= "All Blacks", Country= "New Zealand"}, new Team() {TeamName= "Springboks", Country= "South Africa"}, new Team() {TeamName= "Wallabies", Country= "Australia"} }; } }

private BindableCollection<Team> \_teams; public BindableCollection<Team> Teams { get { return \_teams; } set { \_teams = value; NotifyOfPropertyChange(() => Teams); } } } \[/code\]

#### View

In the View we will have a ListView called Teams. Since we're using Caliburn Micro this will automatically set the ItemSource for me.

The only changes we're making here is in the page definition.

- We define the data context, in the design instance, as being our View Model
- Tell Caliburn Micro to bind at design time

\[code language="xml"\] <Page x:Class="MyApp.Views.TeamSetupView" xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation" xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml" xmlns:d="http://schemas.microsoft.com/expression/blend/2008" xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006" xmlns:micro="using:Caliburn.Micro" xmlns:vm="using:MyApp.ViewModels" mc:Ignorable="d" d:DataContext="{d:DesignInstance Type=vm:BlockSetupViewModel, IsDesignTimeCreatable=True}" micro:Bind.AtDesignTime="True">

<ListView x:Name="Teams"> <DataTemplate> <ListView.ItemTemplate> <StackPanel Orientation="Horizontal"> <TextBlock Text={Binding TeamName} /> <TextBlock Text={Binding Country} /> </StackPanel> </ListView> </DataTemplate> </ListView> </Page> \[/code\]

#### Conclusion

Do this and your design time data will spring to life in Visual Studio!
