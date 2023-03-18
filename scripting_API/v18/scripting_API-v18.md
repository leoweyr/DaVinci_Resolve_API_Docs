In your computer **Davinci Resolve Studio** software data folder, you will find a brief introduction to the Scripting API for **DaVinci Resolve Studio**. That place contains folders containing the basic import modules for scripting access `DaVinciResolve.py` and some representative examples.

From `v16.2.0` onwards, the `nodeIndex` parameters accepted by `SetLUT()` and `SetCDL()` are 1-based instead of 0-based, i.e. 1 <= `nodeIndex` <= total number of nodes.

## Overview

As with ***Blackmagic Design*** **Fusion** scripts, user scripts written in **Lua** and **Python** programming languages are supported. By default, scripts can be invoked from the Console window in the **Fusion** page, or via command line. This permission can be changed in **Resolve** Preferences, to be only from Console, or to be invoked from the local network. Please be aware of the security implications when allowing scripting access from outside of the **Resolve** application.

## Prerequisites

**DaVinci Resolve** scripting requires one of the following to be installed (for all users):

```
Lua 5.1
Python 2.7 64-bit
Python >= 3.6 64-bit
```

## Using A Script

**DaVinci Resolve** needs to be running for a script to be invoked.

For a **Resolve** script to be executed from an external folder, the script needs to know of the API location. 
You may need to set the these environment variables to allow for your **Python** installation to pick up the appropriate dependencies as shown below:

<table>
    <tr>
    	<th>Mac OS X</th>
        <td>RESOLVE_SCRIPT_API = "/Library/Application Support/Blackmagic Design/DaVinci Resolve/Developer/Scripting"<br>
RESOLVE_SCRIPT_LIB = "/Applications/DaVinci Resolve/DaVinci Resolve.app/Contents/Libraries/Fusion/fusionscript.so"<br>PYTHONPATH = "$PYTHONPATH:$RESOLVE_SCRIPT_API/Modules/"</td>
    </tr>
    <tr>
    	<th>Windows</th>
        <td>RESOLVE_SCRIPT_API ="%PROGRAMDATA%\Blackmagic Design\DaVinci Resolve\Support\Developer\Scripting"<br>
RESOLVE_SCRIPT_LIB = "C:\Program Files\Blackmagic Design\DaVinci Resolve\fusionscript.dll"<br>
PYTHONPATH = "%PYTHONPATH%;%RESOLVE_SCRIPT_API%\Modules\"
</td>
    </tr>
    <tr>
        <th>Linux</th>
        <td>RESOLVE_SCRIPT_API = "/opt/resolve/Developer/Scripting"<br>
RESOLVE_SCRIPT_LIB = "/opt/resolve/libs/Fusion/fusionscript.so"<br>
PYTHONPATH = "$PYTHONPATH:$RESOLVE_SCRIPT_API/Modules/"<br>
(Note: For standard ISO Linux installations, the path above may need to be modified to refer to /home/resolve instead of /opt/resolve)</td>
    </tr>
</table>
As with **Fusion** scripts, **Resolve** scripts can also be invoked via the menu and the Console.

On startup, **DaVinci Resolve** scans the subfolders in the directories shown below and enumerates the scripts found in the **Workspace** application menu under **Scripts**. 

Place your script under **Utility** to be listed in all pages, under **Comp** or **Tool** to be available in the **Fusion** page or under folders for individual pages (Edit, Color or Deliver). Scripts under Deliver are additionally listed under render jobs.

Placing your script here and invoking it from the menu is the easiest way to use scripts. 

<table>
	<tr>
    	<th rowspan="2">Mac OS X</th>
        <td>All users</td>
        <td>/Library/Application Support/Blackmagic Design/DaVinci Resolve/Fusion/Scripts</td>
    </tr>
    <tr>
        <td>Specific user</td>
        <td>/Users/(UserName)/Library/Application Support/Blackmagic Design/DaVinci Resolve/Fusion/Scripts</td>
    </tr>
    <tr>
    	<th rowspan="2">Windows</th>
        <td>All users</td>
        <td>%PROGRAMDATA%\Blackmagic Design\DaVinci Resolve\Fusion\Scripts</td>
    </tr>
    <tr>
    	<td>Specific user</td>
        <td>%APPDATA%\Roaming\Blackmagic Design\DaVinci Resolve\Support\Fusion\Scripts</td>
    </tr>
    <tr>
    	<th rowspan="2">Linux</th>
        <td>All users</td>
        <td>/opt/resolve/Fusion/Scripts  (or /home/resolve/Fusion/Scripts/ depending on installation)</td>
    </tr>
    <tr>
    	<td>Specific user</td>
        <td>$HOME/.local/share/DaVinciResolve/Fusion/Scripts</td>
    </tr>
</table>

The interactive Console window allows for an easy way to execute simple scripting commands, to query or modify properties, and to test scripts. The console accepts commands in **Python 2.7**, **Python 3.6** and **Lua** and evaluates and executes them immediately. For more information on how to use the Console, please refer to the **DaVinci Resolve** User Manual.

This example **Python** script creates a simple project:

```python
#!/usr/bin/env python
import DaVinciResolveScript as dvr_script
resolve = dvr_script.scriptapp("Resolve")
fusion = resolve.Fusion()
projectManager = resolve.GetProjectManager()
projectManager.CreateProject("Hello World")
```

The resolve object is the fundamental starting point for scripting via **Resolve**. As a native object, it can be inspected for further scriptable properties - using table iteration and "getmetatable" in **Lua** and dir, help etc in **Python** (among other methods). A notable scriptable object above is fusion - it allows access to all existing **Fusion** scripting functionality.

## Running DaVinci Resolve In Headless Mode

**DaVinci Resolve** can be launched in a headless mode without the user interface using the `-nogui` command line option. When **DaVinci Resolve** is launched using this option, the user interface is disabled. However, the various scripting APIs will continue to work as expected.

## Basic Resolve API

Some commonly used API functions are described below (*). 

> As with the resolve object, each **object** is inspectable for properties and functions.

### Resolve

| Method                                         | Return           | Function                                                     |
| ---------------------------------------------- | ---------------- | ------------------------------------------------------------ |
| Fusion()                                       | Fusion           | Returns the Fusion object. Starting point for Fusion scripts |
| GetMediaStorage()                              | MediaStorage     | Returns the media storage object to query and act on media locations |
| GetProjectManager()                            | ProjectManager   | Returns the project manager object for currently open database |
| OpenPage(pageName)                             | Bool             | Switches to indicated page in DaVinci Resolve. Input can be one of ("media", "cut", "edit", "fusion", "color", "fairlight", "deliver") |
| GetCurrentPage()                               | String           | Returns the page currently displayed in the main window. Returned value can be one of ("media", "cut", "edit", "fusion", "color", "fairlight", "deliver", None) |
| GetProductName()                               | String           | Returns product name("DaVinci Resolve" or "DaVinci Resolve Studio") |
| GetVersion()                                   | [version fields] | Returns list of product version fields in [major, minor, patch, build, suffix] format |
| GetVersionString()                             | String           | Returns product version in "major.minor.patch[suffix].build" format |
| LoadLayoutPreset(presetName)                   | Bool             | Loads UI layout from saved preset named 'presetName'         |
| UpdateLayoutPreset(presetName)                 | Bool             | Overwrites preset named 'presetName' with current UI layout  |
| ExportLayoutPreset(presetName, presetFilePath) | Bool             | Exports preset named 'presetName' to path 'presetFilePath'   |
| DeleteLayoutPreset(presetName)                 | Bool             | Deletes preset named 'presetName'                            |
| SaveLayoutPreset(presetName)                   | Bool             | Saves current UI layout as a preset named 'presetName'       |
| ImportLayoutPreset(presetFilePath, presetName) | Bool             | Imports preset from path 'presetFilePath'. The optional argument 'presetName' specifies how the preset shall be named. If not specified, the preset is named based on the filename |
| Quit()                                         | None             | Quits the Resolve App                                        |