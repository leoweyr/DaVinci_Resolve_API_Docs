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

### ProjectManager

| Method                                                       | Return             | Function                                                     |
| :----------------------------------------------------------- | ------------------ | ------------------------------------------------------------ |
| ArchiveProject(projectName,<br/>                 filePath,<br/>                 isArchiveSrcMedia=True,<br/>                 isArchiveRenderCache=True,<br/>isArchiveProxyMedia=False) | Bool               | Archives project to provided file path with the configuration as provided by the optional arguments |
| CreateProject(projectName)                                   | Project            | Creates and returns a project if projectName (string) is unique, and None if it is not |
| DeleteProject(projectName)                                   | Bool               | Delete project in the current folder if not currently loaded |
| LoadProject(projectName)                                     | Project            | Loads and returns the project with name = projectName (string) if there is a match found, and None if there is no matching Project |
| GetCurrentProject()                                          | Project            | Returns the currently loaded Resolve project                 |
| SaveProject()                                                | Bool               | Saves the currently loaded project with its own name. Returns True if successful |
| CloseProject(project)                                        | Bool               | Closes the specified project without saving                  |
| CreateFolder(folderName)                                     | Bool               | Creates a folder if folderName (string) is unique            |
| DeleteFolder(folderName)                                     | Bool               | Deletes the specified folder if it exists. Returns True in case of success |
| GetProjectListInCurrentFolder()                              | [project names...] | Returns a list of project names in current folder            |
| GetFolderListInCurrentFolder()                               | [folder names...]  | Returns a list of folder names in current folder             |
| GotoRootFolder()                                             | Bool               | Opens root folder in database                                |
| GotoParentFolder()                                           | Bool               | Opens parent folder of current folder in database if current folder has parent |
| GetCurrentFolder()                                           | string             | Returns the current folder name                              |
| OpenFolder(folderName)                                       | Bool               | Opens folder under given name                                |
| ImportProject(filePath, projectName=None)                    | Bool               | Imports a project from the file path provided with given project name, if any. Returns True if successful |
| ExportProject(projectName, filePath, withStillsAndLUTs=True) | Bool               | Exports project to provided file path, including stills and LUTs if withStillsAndLUTs is True (enabled by default). Returns True in case of success |
| RestoreProject(filePath, projectName=None)                   | Bool               | Restores a project from the file path provided with given project name, if any. Returns True if successful |
| GetCurrentDatabase()                                         | {dbInfo}           | Returns a dictionary (with keys 'DbType', 'DbName' and optional 'IpAddress') corresponding to the current database connection |
| GetDatabaseList()                                            | [{dbInfo}]         | Returns a list of dictionary items (with keys 'DbType', 'DbName' and optional 'IpAddress') corresponding to all the databases added to Resolve |
| SetCurrentDatabase({dbInfo})                                 | Bool               | Switches current database connection to the database specified by the keys below, and closes any open project.<br/>'DbType': 'Disk' or 'PostgreSQL' (string)<br/>'DbName': database name (string)<br/> 'IpAddress': IP address of the PostgreSQL server (string, optional key - defaults to '127.0.0.1') |

### Project

| Method                                                       | Return              | Function                                                     |
| ------------------------------------------------------------ | ------------------- | ------------------------------------------------------------ |
| GetMediaPool()                                               | MediaPool           | Returns the Media Pool object                                |
| GetTimelineCount()                                           | Int                 | Returns the number of timelines currently present in the project |
| GetTimelineByIndex(idx)                                      | Timeline            | Returns timeline at the given index, 1 <= idx <= project.GetTimelineCount() |
| GetCurrentTimeline()                                         | Timeline            | Returns the currently loaded timeline                        |
| SetCurrentTimeline(timeline)                                 | Bool                | Sets given timeline as current timeline for the project. Returns True if successful |
| GetGallery()                                                 | Gallery             | Returns the Gallery object                                   |
| GetName()                                                    | String              | Returns project name                                         |
| SetName(projectName)                                         | Bool                | Sets project name if given projectName (string) is unique    |
| GetPresetList()                                              | [presets...]        | Returns a list of presets and their information              |
| SetPreset(presetName)                                        | Bool                | Sets preset by given presetName (string) into project        |
| AddRenderJob()                                               | String              | Adds a render job based on current render settings to the render queue. Returns a unique job id (string) for the new render job |
| DeleteRenderJob(jobId)                                       | Bool                | Deletes render job for input job id (string)                 |
| DeleteAllRenderJobs()                                        | Bool                | Deletes all render jobs in the queue                         |
| GetRenderJobList()                                           | [render jobs...]    | Returns a list of render jobs and their information          |
| GetRenderPresetList()                                        | [presets...]        | Returns a list of render presets and their information       |
| StartRendering(jobId1, jobId2, ...)                          | Bool                | Starts rendering jobs indicated by the input job ids         |
| StartRendering([jobIds...], isInteractiveMode=False)         | Bool                | Starts rendering jobs indicated by the input job ids<br /> The optional "isInteractiveMode", when set, enables error feedback in the UI during rendering |
| StartRendering(isInteractiveMode=False)                      | Bool                | Starts rendering all queued render jobs. <br/>The optional "isInteractiveMode", when set, enables error feedback in the UI during rendering |
| StopRendering()                                              | None                | Stops any current render processes                           |
| IsRenderingInProgress()                                      | Bool                | Returns True if rendering is in progress                     |
| LoadRenderPreset(presetName)                                 | Bool                | Sets a preset as current preset for rendering if presetName (string) exists |
| SaveAsNewRenderPreset(presetName)                            | Bool                | Creates new render preset by given name if presetName(string) is unique |
| SetRenderSettings({settings})                                | Bool                | Sets given settings for rendering. Settings is a dict, with support for the keys: Refer to "Looking up render settings" section for information for supported settings |
| GetRenderJobStatus(jobId)                                    | {status info}       | Returns a dict with job status and completion percentage of the job by given jobId (string) |
| GetSetting(settingName)                                      | String              | Returns value of project setting (indicated by settingName, string). Check the section below for more information |
| SetSetting(settingName, settingValue)                        | Bool                | Sets the project setting (indicated by settingName, string) to the value (settingValue, string). Check the section below for more information |
| GetRenderFormats()                                           | {render formats...} | Returns a dict (format -> file extension) of available render formats |
| GetRenderCodecs(renderFormat)                                | {render codecs...}  | Returns a dict (codec description -> codec name) of available codecs for given render format (string) |
| GetCurrentRenderFormatAndCodec()                             | {format, codec}     | Returns a dict with currently selected format 'format' and render codec 'codec' |
| SetCurrentRenderFormatAndCodec(format, codec)                | Bool                | Sets given render format (string) and render codec (string) as options for rendering |
| GetCurrentRenderMode()                                       | Int                 | Returns the render mode: 0 - Individual clips, 1 - Single clip |
| SetCurrentRenderMode(renderMode)                             | Bool                | Sets the render mode. Specify renderMode = 0 for Individual clips, 1 for Single clip |
| GetRenderResolutions(format, codec)                          | [{Resolution}]      | Returns list of resolutions applicable for the given render format (string) and render codec (string). Returns full list of resolutions if no argument is provided. Each element in the list is a dictionary with 2 keys "Width" and "Height" |
| RefreshLUTList()                                             | Bool                | Refreshes LUT List                                           |
| GetUniqueId()                                                | String              | Returns a unique ID for the project item                     |
| InsertAudioToCurrentTrackAtPlayhead(mediaPath, startOffsetInSamples, durationInSamples) | Bool                | Inserts the media specified by mediaPath (string) with startOffsetInSamples (int) and durationInSamples (int) at the playhead on a selected track on the Fairlight page. Returns True if successful, otherwise False |

### Timeline

| Method                                                      | Return          | Function                                                     |
| ----------------------------------------------------------- | --------------- | ------------------------------------------------------------ |
| GetName()                                                   | String          | Returns the timeline name                                    |
| SetName(timelineName)                                       | Bool            | Sets the timeline name if timelineName (string) is unique. Returns True if successful |
| GetStartFrame()                                             | Int             | Returns the frame number at the start of timeline            |
| GetEndFrame()                                               | Int             | Returns the frame number at the end of timeline              |
| SetStartTimecode(timecode)                                  | Bool            | Set the start timecode of the timeline to the string 'timecode'. Returns true when the change is successful, false otherwise |
| GetStartTimecode()                                          | String          | Returns the start timecode for the timeline                  |
| GetTrackCount(trackType)                                    | Int             | Returns the number of tracks for the given track type ("audio", "video" or "subtitle") |
| GetItemListInTrack(trackType, index)                        | [items...]      | Returns a list of timeline items on that track (based on trackType and index). 1 <= index <= GetTrackCount(trackType) |
| AddMarker(frameId, color, name, note, duration, customData) | Bool            | Creates a new marker at given frameId position and with given marker information. 'customData' is optional and helps to attach user specific data to the marker |
| GetMarkers()                                                | {markers...}    | Returns a dict (frameId -> {information}) of all markers and dicts with their information<br />Example: a value of {96.0: {'color': 'Green', 'duration': 1.0, 'note': '', 'name': 'Marker 1', 'customData': ''}, ...} indicates a single green marker at timeline offset 96 |
| GetMarkerByCustomData(customData)                           | {marker...}     | Returns marker {information} for the first matching marker with specified customData |
| UpdateMarkerCustomData(frameId, customData)                 | Bool            | Updates customData (string) for the marker at given frameId position. CustomData is not exposed via UI and is useful for scripting developer to attach any user specific data to markers |
| GetMarkerCustomData(frameId)                                | String          | Returns customData string for the marker at given frameId position |
| DeleteMarkersByColor(color)                                 | Bool            | Deletes all timeline markers of the specified color. An "All" argument is supported and deletes all timeline markers |
| DeleteMarkerAtFrame(frameNum)                               | Bool            | Deletes the timeline marker at the given frame number        |
| DeleteMarkerByCustomData(customData)                        | Bool            | Delete first matching marker with specified customData       |
| ApplyGradeFromDRX(path, gradeMode, item1, item2, ...)       | Bool            | Loads a still from given file path (string) and applies grade to Timeline Items with gradeMode (int): 0 - "No keyframes", 1 - "Source Timecode aligned", 2 - "Start Frames aligned" |
| ApplyGradeFromDRX(path, gradeMode, [items])                 | Bool            | Loads a still from given file path (string) and applies grade to Timeline Items with gradeMode (int): 0 - "No keyframes", 1 - "Source Timecode aligned", 2 - "Start Frames aligned" |
| GetCurrentTimecode()                                        | String          | Returns a string timecode representation for the current playhead position, while on Cut, Edit, Color, Fairlight and Deliver pages |
| SetCurrentTimecode(timecode)                                | Bool            | Sets current playhead position from input timecode for Cut, Edit, Color, Fairlight and Deliver pages |
| GetCurrentVideoItem()                                       | Item            | Returns the current video timeline item                      |
| GetCurrentClipThumbnailImage()                              | {thumbnailData} | Returns a dict (keys "width", "height", "format" and "data") with data containing raw thumbnail image data (RGB 8-bit image data encoded in base64 format) for current media in the Color Page<br />An example of how to retrieve and interpret thumbnails is provided in 6_get_current_media_thumbnail.py in the Examples folder |
| GetTrackName(trackType, trackIndex)                         | String          | Returns the track name for track indicated by trackType ("audio", "video" or "subtitle") and index. 1 <= trackIndex <= GetTrackCount(trackType) |
| SetTrackName(trackType, trackIndex, name)                   | Bool            | Sets the track name (string) for track indicated by trackType ("audio", "video" or "subtitle") and index. 1 <= trackIndex <= GetTrackCount(trackType) |
| DuplicateTimeline(timelineName)                             | timeline        | Duplicates the timeline and returns the created timeline, with the (optional) timelineName, on success |
| CreateCompoundClip([timelineItems], {clipInfo})             | timelineItem    | Creates a compound clip of input timeline items with an optional clipInfo map: {"startTimecode" : "00:00:00:00", "name" : "Compound Clip 1"}. It returns the created timeline item |
| CreateFusionClip([timelineItems])                           | timelineItem    | Creates a Fusion clip of input timeline items. It returns the created timeline item |
| ImportIntoTimeline(filePath, {importOptions})               | Bool            | Imports timeline items from an AAF file and optional importOptions dict into the timeline, with support for the keys:<br/>"autoImportSourceClipsIntoMediaPool": Bool, specifies if source clips should be imported into media pool, True by default<br/>"ignoreFileExtensionsWhenMatching": Bool, specifies if file extensions should be ignored when matching, False by default<br/>"linkToSourceCameraFiles": Bool, specifies if link to source camera files should be enabled, False by default<br/>"useSizingInfo": Bool, specifies if sizing information should be used, False by default<br/>"importMultiChannelAudioTracksAsLinkedGroups": Bool, specifies if multi-channel audio tracks should be imported as linked groups, False by default<br/>"insertAdditionalTracks": Bool, specifies if additional tracks should be inserted, True by default<br/>"insertWithOffset": string, specifies insert with offset value in timecode format - defaults to "00:00:00:00", applicable if "insertAdditionalTracks" is False<br/>"sourceClipsPath": string, specifies a filesystem path to search for source clips if the media is inaccessible in their original path and if "ignoreFileExtensionsWhenMatching" is True<br/>"sourceClipsFolders": string, list of Media Pool folder objects to search for source clips if the media is not present in current folder |
| Export(fileName, exportType, exportSubtype)                 | Bool            | Exports timeline to 'fileName' as per input exportType & exportSubtype format<br />Refer to section "Looking up timeline exports properties" for information on the parameters |
| GetSetting(settingName)                                     | String          | Returns value of timeline setting (indicated by settingName : string). Check the section below for more information |
| SetSetting(settingName, settingValue)                       | Bool            | Sets timeline setting (indicated by settingName : string) to the value (settingValue : string). Check the section below for more information |
| InsertGeneratorIntoTimeline(generatorName)                  | TimelineItem    | Inserts a generator (indicated by generatorName : string) into the timeline |
| InsertFusionGeneratorIntoTimeline(generatorName)            | TimelineItem    | Inserts a Fusion generator (indicated by generatorName : string) into the timeline |
| InsertFusionCompositionIntoTimeline()                       | TimelineItem    | Inserts a Fusion composition into the timeline               |
| InsertOFXGeneratorIntoTimeline(generatorName)               | TimelineItem    | Inserts an OFX generator (indicated by generatorName : string) into the timeline |
| InsertTitleIntoTimeline(titleName)                          | TimelineItem    | Inserts a title (indicated by titleName : string) into the timeline |
| InsertFusionTitleIntoTimeline(titleName)                    | TimelineItem    | Inserts a Fusion title (indicated by titleName : string) into the timeline |
| GrabStill()                                                 | galleryStill    | Grabs still from the current video clip. Returns a GalleryStill object |
| GrabAllStills(stillFrameSource)                             | [galleryStill]  | Grabs stills from all the clips of the timeline at 'stillFrameSource' (1 - First frame, 2 - Middle frame). Returns the list of GalleryStill objects |
| GetUniqueId()                                               | String          | Returns a unique ID for the timeline                         |
