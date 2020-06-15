---
author: "Ben Chartrand"
title: "ThemeResource – Dark and Light themes in Windows 10"
date: "2015-08-24"
---

In my application I wanted support for a dark and light theme that users could change at runtime via a setting. Turns out there is already support for a dark and light theme via the ThemeResource and I could easily change it programmatically. It was just a matter of defining my styles / look and feel to match!

#### Background

If you're a Windows Phone user, you know you can also go into the settings and define if you want the phone to run in a light or dark mode. In Windows 10, the functionality is there as well but only available by adding two keys to the registry. What we can infer from the keys below is that Windows 10 runs in Light mode by default.

\[code language="text"\] \[HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Themes\\Personalize\] "AppsUseLightTheme"=dword:00000000

\[HKEY\_CURRENT\_USER\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Themes\\Personalize\] "AppsUseLightTheme"=dword:00000000 \[/code\]

If you want to see what Windows 10 looks like in Dark mode and for more info on the current state of the dark theme [checkout this howtogeek article](http://www.howtogeek.com/222614/how-to-enable-windows-10%E2%80%99s-hidden-dark-theme/).

#### RequestedTheme Property

In App.xaml you have the RequestedTheme tag where you can specify either Light or Dark. It's there by default if you start a new project and is set to Light. If you remove it, the value assumes the Default state. On that note, there are actually four states (more on this in a minute):

- Default
- Light
- Dark
- High Contrast

#### ThemeResources

These are like StaticResources except they are somewhat dynamic. If you change the theme at runtime, they change as well.

Like StaticResources, you can define them in a ResourceDictionary but the key differences is that you encapsulate them within a ResourcesDictionary.ThemeDictionaries tag and you need to specify the dark, light and ideally the high contrast version using a key. Here's a simple example:

\[code language="xml"\] <ResourceDictionary xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation" xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml" xmlns:local="using:MySampleProject.ResourcesDictionaries"> <ResourceDictionary.ThemeDictionaries> <ResourceDictionary x:Key="Dark"> <Brush x:Key="MyButtonBrush">Red</Brush> </ResourceDictionary> <ResourceDictionary x:Key="Light"> <Brush x:Key="MyButtonBrush">Green</Brush> </ResourceDictionary> <ResourceDictionary x:Key="HighContrast"> <Brush x:Key="MyButtonBrush">Blue</Brush> </ResourceDictionary> </ResourceDictionary.ThemeDictionaries> </ResourceDictionary> \[/code\]

And here is my example where I use it. Note the use or ThemeResource.

\[code language="xml"\] <Button Content="Hello World" Click="HelloWorld\_Click" Background="{ThemeResource MyButtonBrush}" /> \[/code\]

The key thing to note in the above resource dictionary is the use of the light, dark and high contrast keys. You could enter a value for Default but Microsoft does not recommend it. More information from on ThemeResource [available here](https://msdn.microsoft.com/en-us/library/windows/apps/mt187274.aspx) from Microsoft.

#### RequestedTheme property

Within each FrameworkElement (the base type for XAML elements), Microsoft added the RequestedTheme property. That means you could change it programmatically or via XAML so you could do something interesting, such as having some content on the light theme and other on the dark, such as this:

\[code language="xml"\] <Button Content="Hello World Dark" Background="{ThemeResource MyButtonBrush}" RequestedTheme="Dark" /> <Button Content="Hello World Light" Background="{ThemeResource MyButtonBrush}" RequestedTheme="Light" /> \[/code\]

#### Switching between Light and Dark

The RequestedTheme property we saw earlier, in the app.xaml, is a global setting. If you try to change it at runtime you will get an exception. But you can set it at start up but that means the user would have to shutdown your application and open it back up.

If you want to change between themes in code, the most sensible thing to do is to change the RequestedTheme property at the root frame of your application. That way the theme applies to all the pages.

I'm using Caliburn.Micro. At one point I have to register my navigation service, as seen below, in my app.xaml.cs.

\[code language="csharp"\] protected override void PrepareViewFirst(Frame rootFrame) { \_container.RegisterNavigationService(rootFrame); } \[/code\]

I'm not 100% sure how I'll incorporate this as part of my application so, for now, I created a public static value in my app.xaml.cs, which I can then access later in my program.

\[code language="csharp"\] public static Frame MyRootFrame;

protected override void PrepareViewFirst(Frame rootFrame) { MyRootFrame = rootFrame; \_container.RegisterNavigationService(rootFrame); } \[/code\]

... and later in my view model, I have a view with a single button. Here's the implementation in the view model where I simply toggle between the different states.

\[code language="csharp"\] public void ToggleThemes() { App.MyRootFrame.RequestedTheme = App.MyRootFrame.RequestedTheme == ElementTheme.Dark ? ElementTheme.Light : ElementTheme.Dark; } \[/code\]

Here's another implementation using dependency injection, from [Olivier Matis](http://www.guruumeditation.net/en/changing-app-theme-on-the-fly-with-requestedtheme/).

#### Summary and Further Reading

The good news is that you can create a dark or light theme. You can also toggle between the two programmatically.

For more information, check the [ResourceDictionary and XAML resource references](https://msdn.microsoft.com/en-us/library/windows/apps/mt187273.aspx) and [XAML Theme Resources](https://msdn.microsoft.com/en-us/library/windows/apps/mt187274.aspx) articles from Microsoft.
