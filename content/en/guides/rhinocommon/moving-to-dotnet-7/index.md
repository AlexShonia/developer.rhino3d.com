+++
authors = ["curtis"]
categories = []
description = "This guide walks you through making the transition to .NET 7"
keywords = [ ".NET", "RhinoCommon", "Plugin" ]
languages = [ "C#" ]
sdk = [ "RhinoCommon" ]
title = "Moving to .NET 7"
type = "guides"
weight = 4

[admin]
TODO = ""
origin = ""
picky_sisters = ""
state = ""

[included_in]
platforms = [ "Windows", "Mac" ]
since = 8

[page_options]
byline = true
toc = true
toc_type = "single"
block_webcrawlers = false
+++

Rhino 8 now uses the open source [.NET Core Runtime](https://github.com/dotnet/runtime) for running .NET code on both Windows and Mac.
This brings some performance improvements and aligns the .NET runtimes used across platforms. Previously, Rhino 7 and earlier used the [mono runtime](https://www.mono-project.com) on Mac, and .NET Framework exclusively on Windows.

On Windows, you can still optionally run using the .NET Framework runtime in the case of compatibility issues or running inside other software that requires it (e.g. Rhino.Inside Revit).

Most plugins are already compatible when running in .NET Core without any recompilation, but in the case of any incompatibilities you may need to update your plugin.

## Choosing the .NET Runtime on Windows

There are two ways to select the runtime that Rhino uses:

1. Use the `SetDotNetRuntime` command, then restart Rhino.
1. Pass either `/netcore` or `/netfx` as an argument when launching `Rhino.exe`. This overrides the `SetDotNetRuntime` setting.

## Checking if your plugin is compatible

Rhino 8 will automatically scan plugins for any known API breakages when running in .NET Core, and will provide a report of the specific assemblies and APIs that are not comatible.

To check manually, you can use the `compat.exe` tool on each of your plugin assemblies:

```
"C:\Program Files\Rhino 8 WIP\System\netcore\compat.exe" -q --check-system-assemblies MyPlugin.rhp
```

You can also use Microsoft's [upgrade assistant](https://learn.microsoft.com/en-us/dotnet/core/porting/upgrade-assistant-overview) to analyze your project for compatibility issues.

## Migrating your plugin

It is recommended to keep your plugin(s) targetted at .NET 4.8 so that it can run in either runtime on Windows. Most plugins won't need any changes to run in Rhino 8.

For Mac-specific plugins you can target .NET 7 as that is the only runtime available in Rhino 8. If you want the plugin to be compatible with Rhino 7, keep the target at .NET 4.8.

If your plugin uses any unavailable or non-working APIs when running in .NET Core, some code changes may be necessary. The compat report will show you which APIs you need to avoid.

If you [multi-target your project](https://learn.microsoft.com/en-us/nuget/create-packages/multiple-target-frameworks-project-file) to both .NET 4.8 and .NET 7.0, you can find compatibility issues during compilation. Keep in mind you should only distribute the .NET 4.8 version on Windows.

## Debugging .NET Core on Windows

To debug in .NET Core on Windows you will need to use Visual Studio 2022 or Visual Studio Code.

Visual Studio determines the debugging runtime by the project's target framework, so either you need to [multi-target your project](https://learn.microsoft.com/en-us/nuget/create-packages/multiple-target-frameworks-project-file), or create a separate launcher project used for debugging purposes only.

With Visual Studio Code, use the `coreclr` debugger type from the C# extension. You do not need to multi-target or create a launcher project when using Visual Studio Code.

### How to add a .NET Core launcher project in Visual Studio

This is only required when you want to debug in .NET Core using Visual Studio 2022 on Windows.

1. Right-click on your solution node and select *Add > New Project...*
1. Select the C# **Console App** then click *Next*.
1. Enter a name e.g. `Rhino8Launcher` then click *Next*.
1. Select **.NET 7.0** for the Framework then click *Create*.
1. Right-click the launcher project and select *Properties*.
1. Go to the **Application > General** panel and change the **Output Type** drop down to *Windows Application*.
1. Go to the **Debug > General** panel and click *Open debug launch profiles UI*.
1. Delete the default profile by clicking the *Delete selected profile* button.
1. Create a new **Executable** profile, and enter the path to Rhino.exe. E.g. `C:\Program Files\Rhino 8 WIP\System\Rhino.exe`

You may also want to add your plugin project as a dependency of the launcher project so it compiles before launching.

## Debugging on Mac

On Mac, get the [latest RhinoCommon Visual Studio extension](https://github.com/mcneel/RhinoVisualStudioExtensions) for Visual Studio for Mac. The extension automatically selects which version to debug based on the referenced version of RhinoCommon.

To override this, you can go to the **Project Properties > Rhino** panel and select the launcher version. This adds a `<RhinoMacLauncher>8</RhinoMacLauncher>` property to your project file.

You can also use Visual Studio Code with the `coreclr` debugger type from the C# extension. Set the program to `/Applications/RhinoWIP.app/Contents/MacOS/Rhinoceros` and set either the `RHINO_PLUGIN_PATH` or `GRASSHOPPER_PLUGINS` to the path of your built plugin/component.