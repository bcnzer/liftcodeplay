---
author: "Ben Chartrand"
title: "Incorporating SQLite into a Windows 10 UWP project"
date: "2015-10-04"
---

In the UWP platform, Microsoft is encouraging us to all use SQLite as the local database. This post explains how we can add it (summary: there's a few steps but it's all straightforward).

#### SQLite VSIX

The first step is ensure the SQLite for Universal Windows Platform is installed. It's delivered as a VSIX (Visual Studio Extension Installer). This will provide the core sqlite3.dll. Since it's a VSIX, Visual Studio will remind you when an update is available.

There are two ways to download it.

The first is to go to SQLite.org and download the VSIX. [If you go here](https://www.sqlite.org/download.html), you will find it under the Universal Windows Platform header. Once you've got it, install it.

\[caption id="attachment\_185" align="aligncenter" width="660"\][![The link for the SQLite VSIX. Note the ](https://liftcodeplay.files.wordpress.com/2015/09/sqlite-download.png?w=660)](https://liftcodeplay.files.wordpress.com/2015/09/sqlite-download.png) The link for the SQLite VSIX. Note the "UAP" name (Uniersal App Platform). This name was changed to UWP before Windows 10 went RTM.\[/caption\]

The second method is open Visual Studio 2015 > Tools > Extensions and Updates > Online tab. In the search field on the right, enter **SQLite for Universal App Platform** and you will see it in the list. Once you've found it, install it

\[caption id="attachment\_186" align="aligncenter" width="660"\][![SQLite VSIX available via Visual Studio 2015 Extensions and Updates](https://liftcodeplay.files.wordpress.com/2015/09/sqlite-download-via-vs.png?w=660)](https://liftcodeplay.files.wordpress.com/2015/09/sqlite-download-via-vs.png) SQLite VSIX available via Visual Studio 2015 Extensions and Updates\[/caption\]

As you can infer from the above picture, you can know you have it installed by checking your installed extensions. It will have the green tick as well.

###### Adding SQLite Service Reference to Your Project

In your project, right click References and select Add Reference. In the window that pops up select Universal Windows > Extensions > SQLite for Universal App Platform.

\[caption id="attachment\_188" align="aligncenter" width="660"\][![The reference you need to add to your project](https://liftcodeplay.files.wordpress.com/2015/10/sqlite-reference-e1444017656453.png?w=660)](https://liftcodeplay.files.wordpress.com/2015/10/sqlite-reference-e1444017656453.png) The reference you need to add to your project\[/caption\]

#### SQLite PCL

Almost done!

At this point you've got the main DLL on your system and your project is referring to it. The DLL is written in C. You need a .NET friendly wrapper for it and there are a ton to chose from.

I personally decided to go with **SQLite-Net.PCL** for a few key reasons: it's an active project on GitHub and is a PCL (portable class library).

\[caption id="attachment\_190" align="aligncenter" width="660"\][![The SQLite.NET-PCL NuGet package](https://liftcodeplay.files.wordpress.com/2015/10/sqlite-nuget-package.png?w=660)](https://liftcodeplay.files.wordpress.com/2015/10/sqlite-nuget-package.png) The SQLite.NET-PCL NuGet package\[/caption\]

#### Bringing it all Together

And here's how we can use this library in code. First we'll create a simple table called Team.

\[code language="csharp"\] public class Team { \[PrimaryKey, AutoIncrement\] public int TeamID { get; set; }

public string TeamName { get; set; } } \[/code\]

... and here we are opening a DB, creating the table, writing some data and reading it.

\[code language="csharp"\] var path = Path.Combine(Windows.Storage.ApplicationData.Current.LocalFolder.Path, "db.MyTeams" );

var db = new SQLiteConnection(new SQLitePlatformWinRT(), path); db.CreateTable<Team>();

// Example writing to the DB db.Insert(new Team() {TeamName = "All Blacks"}); db.Insert(new Team() {TeamName = "Wallabies"}); db.Insert(new Team() {TeamName = "Springboks"});

// Reading from the table var query = DbUtils.SpotterDb.Table<Team>();

foreach (var currentTeam in query) { // If you were to step through here, you'd see all the names var currentTeamName = currentTeam.TeamName; } \[/code\]
