---
title: Background Task Helper
author: nmetulev
description: The Background Task Helper helps users interacting with background tasks in an easier manner. 
keywords: windows 10, uwp, uwp community toolkit, uwp toolkit, Background Task Helper
---

# Background Task Helper

The [Background Task Helper](https://docs.microsoft.com/dotnet/api/microsoft.toolkit.uwp.helpers.backgroundtaskhelper) helps users interacting with background tasks in an easier manner. 

## Syntax

```csharp
using Microsoft.Toolkit.Uwp;

BackgroundTaskRegistration registered = BackgroundTaskHelper.Register(typeof(BackgroundTaskClass), new TimeTrigger(15, true));
BackgroundTaskRegistration registered = BackgroundTaskHelper.Register("TaskName", "TaskEntryPoint", new TimeTrigger(15, true));
```

## Methods

| Methods | Return Type | Description |
| -- | -- | -- |
| GetBackgroundTask(String) | [IBackgroundTaskRegistration](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.IBackgroundTaskRegistration) | Get the registered background task of the given type |
| GetBackgroundTask(Type) | IBackgroundTaskRegistration | Get the registered background task of the given type |
| IsBackgroundTaskRegistered(String) | bool | Check if a background task is registered |
| IsBackgroundTaskRegistered(Type) | bool | Check if a background task is registered |
| Register(String, IBackgroundTrigger, Boolean, Boolean, IBackgroundCondition[]) | [BackgroundTaskRegistration](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.BackgroundTaskRegistration) | Registers under the Single Process Model |
| Register(Type, IBackgroundTrigger, Boolean, Boolean, IBackgroundCondition[]) | BackgroundTaskRegistration | Register a background task with conditions |
| Register(String, String, IBackgroundTrigger, Boolean, Boolean, IBackgroundCondition[]) | BackgroundTaskRegistration | Register a background task with conditions |
| Unregister(String, Boolean) | void | Unregister a background task |
| Unregister(Type, Boolean) | void | Unregister a background task |
| Unregister(IBackgroundTaskRegistration, Boolean) | void | Unregister a background task |

## Example

### Using Multi-Process Model

Using MPM (Multi-Process Model) is the classic way of using Background Task.

To make it work, you will need :

* To create Background Tasks in a Windows Runtime Component
* To register the Background Tasks in the package manifest (appxmanifest file)

Once it is done, you can register your Background Tasks.

```csharp
// Be sure to include the using at the top of the file:
using Microsoft.Toolkit.Uwp;
using Windows.ApplicationModel.Background;

// Register a normal, seperate process, background task
BackgroundTaskRegistration registered = BackgroundTaskHelper.Register("TaskName", "TaskEntryPoint", new TimeTrigger(15, true));

// This can also be written using the overload of Register with Type parameter.
BackgroundTaskRegistration registered = BackgroundTaskHelper.Register(typeof(BackgroundTaskClass), new TimeTrigger(15, true));

// With condition
BackgroundTaskRegistration registered = 
    BackgroundTaskHelper.Register(typeof(BackgroundTaskClass), 
                                    new TimeTrigger(15, true), 
                                    false, true, 
                                    new SystemCondition(SystemConditionType.InternetAvailable));

// 2 or more conditions
BackgroundTaskRegistration registered = 
    BackgroundTaskHelper.Register(typeof(BackgroundTaskClass), 
                                    new TimeTrigger(15, true), 
                                    false, true, 
                                    new SystemCondition(SystemConditionType.InternetAvailable), 
                                    new SystemCondition(SystemConditionType.UserPresent));
```

### Using Single-Process Model

Using SPM (Single-Process Model) is the new and simple way of using Background Task.
It is required to target Anniversary Update (SDK 14393) or later.

Using SPM, you can declare your Background Tasks inside your own project, no need to create a Windows Runtime Component.
Moreover, it is no longer required to register the Background Tasks in the package manifest (appxmanifest file).

Once you have created the Background Task, you can register it by calling the `Register` method.

```csharp
// Be sure to include the using at the top of the file:
using Microsoft.Toolkit.Uwp;
using Windows.ApplicationModel.Background;

// Register a single process background task (Anniversary Update and later ONLY)
BackgroundTaskRegistration registered = BackgroundTaskHelper.Register("Name of the Background Task", new TimeTrigger(15, true));
```

The other difference between SPM and MPM is that in SPM, you have to handle your Background Tasks inside the `OnBackgroundActivated` event of `App.xaml.cs` class.
Here is an example of how to handle Background Tasks in SPM.

```csharp
// Event fired when a Background Task is activated (in Single Process Model)
protected override void OnBackgroundActivated(BackgroundActivatedEventArgs args)
{
    base.OnBackgroundActivated(args);

    var deferral = args.TaskInstance.GetDeferral();

    switch (args.TaskInstance.Task.Name)
    {
        case "Name of the Background Task":
            new TestBackgroundTask().Run(args.TaskInstance);
            break;
    }

    deferral.Complete();
}
```

## Sample Code

[Background Task Helper](https://github.com/Microsoft/UWPCommunityToolkit/tree/master/Microsoft.Toolkit.Uwp.SampleApp/SamplePages/BackgroundTaskHelper). You can see this in action in [UWP Community Toolkit Sample App](https://www.microsoft.com/store/apps/9NBLGGH4TLCQ).

## Requirements

| Device family | Universal, 10.0.14393.0 or higher |
| --- | --- |
| Namespace | Microsoft.Toolkit.Uwp |
| NuGet package | [Microsoft.Toolkit.Uwp](https://www.nuget.org/packages/Microsoft.Toolkit.Uwp/) |

## API

* [Background Task source code](https://github.com/Microsoft/UWPCommunityToolkit/blob/master/Microsoft.Toolkit.Uwp/Helpers/BackgroundTaskHelper.cs)
