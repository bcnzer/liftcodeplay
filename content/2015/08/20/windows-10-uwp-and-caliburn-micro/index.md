---
author: "Ben Chartrand"
title: "Windows 10 UWP and Caliburn Micro"
date: "2015-08-20"
---

Lets walk through the setup of a blank UWP (Universal Windows Platform) project with [Caliburn Micro](http://caliburnmicro.com/) - an MVVM framework. I'm going to cover the minimal steps you in order to get up and running. I'm assuming a basic knowledge of MVVM and CM.

If you prefer a pre-existing template to work with, check out [this post by Bogdan Bujdea](http://www.codeproject.com/Articles/1001387/Caliburn-Micro-template-for-Windows-apps) explaining the template he wrote. What we will produce will be very similar but simpler.

We will be using the [WinRT" functionality in Caliburn.Micro](http://caliburnmicro.com/documentation/windows-runtime). The WinRT tag threw me off when I was searching for information. That being said, what we're doing is near identical to what you would need to do in Windows 8/8.1. [See this series by Terry Marshall](http://www.terrymarshall.com.au/Blog/tabid/162/EntryId/157/WinRT-and-Caliburn-Micro-Part-1-Introduction.aspx), which explains everything in more detail than I will.

#### PREREQUISITES

- PC running Windows 10 PC
- Visual Studio 2015 (community edition is fine)
- Windows 10 SDK
- Developer mode enabled in Settings > Update & Security > For developers

[See this article for more information](https://msdn.microsoft.com/library/windows/apps/dn726766.aspx).

#### Getting Started

Open Visual Studio 2015. Select **New Project...** and then Visual C# > Windows > Universal > **Blank App (Universal Windows)**. You have the option to "Show Telemetry in the Windows 10 dev centre" and "Enable richer analytics with Application Insights". You're welcome to select one/both.

\[caption id="attachment\_28" align="aligncenter" width="300"\][![UWP Caliburn - New Project](https://liftcodeplay.files.wordpress.com/2015/08/uwp-caliburn-new-project.png?w=300)](https://liftcodeplay.files.wordpress.com/2015/08/uwp-caliburn-new-project.png) Select a blank project\[/caption\]

#### Add the caliburn.micro nuget package

Open the NuGet Package Manager (i.e. right-click the project and select Manage NuGet Packages). Search for **Caliburn.Micro** and install it. If you prefer the command line, enter this into the package manager console **install-package Caliburn.Micro**

\[caption id="attachment\_30" align="aligncenter" width="300"\][![UWP Caliburn - Find package](https://liftcodeplay.files.wordpress.com/2015/08/uwp-caliburn-find-package.png?w=300)](https://liftcodeplay.files.wordpress.com/2015/08/uwp-caliburn-find-package.png) Add the caliburn.micro nuget package\[/caption\]

#### Folders

Create a folder in the root of your project called **Views** and another called **ViewModels**.

Within the ViewModels folder, we need a class called ViewModelBase. This inherits from _Screen._ Going forward, your view models will inherit from ViewModelBase.

\[code language="csharp"\] using Caliburn.Micro;

namespace UWPCaliburnMicro.ViewModels { public class ViewModelBase : Screen { protected readonly INavigationService PageNavigationService;

protected ViewModelBase(INavigationService pageNavigationService) { PageNavigationService = pageNavigationService; }

public bool CanGoBack { get { return PageNavigationService.CanGoBack; } }

protected void NavigateTo<T>() { PageNavigationService.Navigate<T>(); }

public void GoBack() { PageNavigationService.GoBack(); } } } \[/code\]

In this example, I'm going to have a single view called Hello. As such, I need to create a blank XAML page under the Views folder called HelloView.xaml and a class in ViewModels called HelloViewModel.cs. We're following the [Caliburn.Micro naming convention](http://caliburnmicro.com/documentation/naming-conventions).

#### Create a view and viewmodel

In my example, I am going to create a very simple view called HelloWorld, which will be my main page. It consists purely of a textbox and button. Note that I didn't touch the code behind on the view and deleted MainPage.xaml.

\[code language="xml"\] <Page x:Class="UWPCaliburnMicro.Views.HelloWorldView" xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation" xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml" xmlns:local="using:UWPCaliburnMicro.Views" xmlns:d="http://schemas.microsoft.com/expression/blend/2008" xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006" mc:Ignorable="d">

<Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}"> <StackPanel Orientation="Vertical" Margin="20"> <TextBox x:Name="MyMessage" /> <Button x:Name="SayHello" Content="Say hello" Margin="0,10" /> </StackPanel> </Grid> </Page> \[/code\]

Here's the ViewModel code. Simple stuff - if you press the button, I populate the textbox.

\[code language="csharp"\] using Caliburn.Micro;

namespace UWPCaliburnMicro.ViewModels { public class HelloWorldViewModel : ViewModelBase { private INavigationService \_pageNavigationService;

public HelloWorldViewModel(INavigationService pageNavigationService) : base(pageNavigationService) { \_pageNavigationService = pageNavigationService; }

private string \_myMessage; public string MyMessage { get { return \_myMessage; } set { \_myMessage = value; NotifyOfPropertyChange(() => MyMessage); } }

public void SayHello() { MyMessage = "Hello World!"; } } } \[/code\]

#### App.xaml

We need to make bit a fair of changes here. Let's start in App.xaml. It's currently based on Application. We need to change that to CaliburnApplication. If you're new to XAML, what we've done is add the line xmlns:cm="using:Caliburn.Micro" (I'm defining an XML namespace to Caliburn.Micro) and then using "cm", which I defined, to reference the CaliburnApplication.

\[code language="xml"\] <cm:CaliburnApplication x:Class="UWPCaliburnMicro.App" xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation" xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml" xmlns:local="using:UWPCaliburnMicro" xmlns:cm="using:Caliburn.Micro" RequestedTheme="Light">

</cm:CaliburnApplication> \[/code\]

#### App.xaml.cs

Nothing too serious here. A few things to note:

- In my example I've still got the telemetry client (Application Insights)
- I'm generally trying to keep it similar to the blank template in that we're supporting OnLaunching, OnResuming, OnSuspending. This is important as they are all events you should be handling
- We overwrite several functions from CaliburnApplication including Configure (where we setup our container and add all our view models) as well as GetAllInstances, BuildUp, GetInstance

In writing a UWP, make sure you build the appropriate functionality in OnResuming and OnSuspending.

\[code language="csharp"\] using System; using System.Collections.Generic; using System.Diagnostics; using Windows.ApplicationModel; using Windows.ApplicationModel.Activation; using Windows.UI.Xaml.Controls; using Caliburn.Micro; using Microsoft.ApplicationInsights; using UWPCaliburnMicro.ViewModels; using UWPCaliburnMicro.Views;

namespace UWPCaliburnMicro { /// <summary> /// Provides application-specific behavior to supplement the default Application class. /// </summary>

sealed partial class App { private WinRTContainer \_container;

/// <summary> /// Initializes the singleton application object. This is the first line of authored code /// executed, and as such is the logical equivalent of main() or WinMain(). /// </summary>

public App() { WindowsAppInitializer.InitializeAsync(); this.InitializeComponent(); this.Suspending += OnSuspending; }

protected override void Configure() { \_container = new WinRTContainer();

\_container.RegisterWinRTServices();

// Make sure to register your containers here \_container.PerRequest<HelloWorldViewModel>(); }

/// <summary> /// Invoked when the application is launched normally by the end user. Other entry points /// will be used such as when the application is launched to open a specific file. /// </summary>

/// <param name="e">Details about the launch request and process.</param> protected override void OnLaunched(LaunchActivatedEventArgs e) { #if DEBUG if (Debugger.IsAttached) { this.DebugSettings.EnableFrameRateCounter = true; } #endif // I am launching my main view here DisplayRootView<HelloWorldView>(); }

/// <summary> /// Invoked when application execution is being suspended. Application state is saved /// without knowing whether the application will be terminated or resumed with the contents /// of memory still intact. /// </summary>

/// <param name="sender">The source of the suspend request.</param> /// <param name="e">Details about the suspend request.</param> protected override void OnSuspending(object sender, SuspendingEventArgs e) { var deferral = e.SuspendingOperation.GetDeferral(); //TODO: Save application state and stop any background activity deferral.Complete(); }

protected override void PrepareViewFirst(Frame rootFrame) { \_container.RegisterNavigationService(rootFrame); }

protected override object GetInstance(Type service, string key) { return \_container.GetInstance(service, key); }

protected override IEnumerable<object> GetAllInstances(Type service) { return \_container.GetAllInstances(service); }

protected override void BuildUp(object instance) { \_container.BuildUp(instance); }

} } \[/code\]

#### Behaviours sdk reference

In your project, right-click **References** and select **Add Reference...** Under **Universal Windows** > **Extensions** select **Behaviours SDK (XAML)**

\[caption id="attachment\_45" align="aligncenter" width="300"\][![Select Behaviours SDK (XAML)](https://liftcodeplay.files.wordpress.com/2015/08/uwp-caliburn-behaviours-sdk1.png?w=300)](https://liftcodeplay.files.wordpress.com/2015/08/uwp-caliburn-behaviours-sdk1.png) Select Behaviours SDK (XAML)\[/caption\]

#### DONE

We've now created a simple UWP using Caliburn Micro!

\[caption id="attachment\_58" align="aligncenter" width="300"\][![The final product. A simple Hello World application](https://liftcodeplay.files.wordpress.com/2015/08/uwp-caliburn-final-product.png?w=300)](https://liftcodeplay.files.wordpress.com/2015/08/uwp-caliburn-final-product.png) The final product. A simple Hello World application\[/caption\]
