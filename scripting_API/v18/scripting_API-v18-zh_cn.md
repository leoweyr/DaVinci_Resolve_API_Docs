在您的电脑 **Davinci Resolve Studio** 软件数据文件夹中，您会找到**DaVinci Resolve Studio** 脚本接口的简要介绍。该位置的其它文件夹，其中包含用于脚本访问`DaVinciResolve.py`的基本导入模块和一些代码示例。

从 `版本16.2.0` 开始，`SetLUT()` 和 `SetCDL()` 接受的 `nodeIndex` 参数是从 1 开始的，而不是从 0 开始的，即 1 <= `nodeIndex` <= 节点总数。

## 概述

与 ***Blackmagic Design*** **Fusion** 脚本一样，支持使用 **Lua** 和 **Python** 编程语言编写的用户脚本。默认情况下，可以从 **Fusion** 页面的控制台窗口或通过命令行调用脚本。此权限可以在 **Resolve** 偏好设置中更改，仅来自控制台，或从本地网络调用。当允许从 **Resolve** 应用程序外部访问脚本时，请注意安全隐患。

## 先决条件

**DaVinci Resolve** 脚本需要安装以下其中一项（适用于所有用户）：

```
Lua 5.1
Python 2.7 64-bit
Python >= 3.6 64-bit
```

## 调用脚本

**DaVinci Resolve** 需要运行才能调用脚本。

对于要从外部文件夹执行的 **Resolve** 脚本，脚本需要知道 API 位置。

您可能需要设置这些环境变量以允许您的 **Python** 安装选择适当的依赖项，如下所示：

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

与 **Fusion** 脚本一样，**Resolve** 脚本也可以通过菜单和控制台调用。

启动时，**DaVinci Resolve** 扫描如下所示目录中的子文件夹，并将发现的可用脚本枚举在**工作区**的**脚本**菜单中。

将您的脚本放在**公共父级文件夹**下以在所有页面中列出，在 **Comp** 或**工具**下以在 **Fusion** 页面中可用或放置在单个页面的文件夹下（编辑、颜色或交付）。交付页面的渲染配置里可设置脚本的启用时机。

将您的脚本放在下列位置并从菜单中调用它是使用脚本最简单的方法。

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

交互式控制台窗口提供了一种简单的方法来执行简单的脚本命令、查询或修改属性以及测试脚本。控制台接受 **Python 2.7**、**Python 3.6** 和 **Lua** 中的命令，并立即评估和执行它们。有关如何使用控制台的更多信息，请参阅 **DaVinci Resolve** 用户手册。

此示例为 **Python** 脚本创建一个简单的项目：

```python
#!/usr/bin/env python
import DaVinciResolveScript as dvr_script
resolve = dvr_script.scriptapp("Resolve")
fusion = resolve.Fusion()
projectManager = resolve.GetProjectManager()
projectManager.CreateProject("Hello World")
```

实例化 `resolve` 对象是通过 **Resolve** 编写脚本的基本起点。作为原生环境的对象，可以检查它是否有更多可编写脚本的属性，例如使用 **Lua** 中的表迭代或获取元表以及 **Python** 中的目录、帮助等（以及其他方法）。上面一个值得注意的可编写脚本的对象是在 **Fusion**中，即它允许访问所有现有的 **Fusion** 脚本功能。

## 在无界面模式下运行 DaVinci Resolve

**DaVinci Resolve** 可以使用`-nogui`命令行选项在没有用户界面的无界面模式下启动。使用此选项启动 **DaVinci Resolve** 时，用户界面将被禁用。但是，各种脚本 API 将继续按预期工作。

## 基础API

下面介绍了一些常用的 API 函数。

> 与`resolve`对象一样，每一种对象都具有属性和方法。

### Resolve

| 方法                                           | 返回值           | 作用                                                         |
| ---------------------------------------------- | ---------------- | ------------------------------------------------------------ |
| Fusion()                                       | Fusion           | 返回 Fusion 对象。开始调用`Fusion`对象 API 的起点            |
| GetMediaStorage()                              | MediaStorage     | 返回 MediaStorage 对象以查询和操作媒体位置                   |
| GetProjectManager()                            | ProjectManager   | 返回当前打开的数据库的 ProjectManager 对象                   |
| OpenPage(pageName)                             | Bool             | 切换到 DaVinci Resolve 中的指定页面。 输入可以是 ("media", "cut", "edit", "fusion", "color", "fairlight", "deliver") 之一 |
| GetCurrentPage()                               | String           | 返回当前显示在主窗口中的页面。 返回值可以是 ("media", "cut", "edit", "fusion", "color", "fairlight", "deliver", None) 之一 |
| GetProductName()                               | String           | 返回产品名称（免费版返回“Davinci Resolve”，付费版返回“Davinci Resolve Studio”） |
| GetVersion()                                   | [version fields] | 返回 [major, minor, patch, build, suffix] 格式的产品版本字段列表 |
| GetVersionString()                             | String           | 以“major.minor.patch[suffix].build”格式返回产品版本          |
| LoadLayoutPreset(presetName)                   | Bool             | 从名为“presetName”的已保存预设加载 UI 布局                   |
| UpdateLayoutPreset(presetName)                 | Bool             | 使用当前 UI 布局覆盖名为“presetName”的预设                   |
| ExportLayoutPreset(presetName, presetFilePath) | Bool             | 将名为“presetName”的预设导出到路径“presetFilePath”           |
| DeleteLayoutPreset(presetName)                 | Bool             | 删除名为“presetName”的预设                                   |
| SaveLayoutPreset(presetName)                   | Bool             | 将当前 UI 布局保存为名为“presetName”的预设                   |
| ImportLayoutPreset(presetFilePath, presetName) | Bool             | 从路径“presetFilePath”导入预设。 可选参数“presetName”指定预设的命名方式。 如果未指定，则根据文件名命名预设 |
| Quit()                                         | None             | 关闭软件                                                     |

### ProjectManager

| 方法                                                         | 返回值             | 作用                                                         |
| :----------------------------------------------------------- | ------------------ | ------------------------------------------------------------ |
| ArchiveProject(projectName,<br/>                 filePath,<br/>                 isArchiveSrcMedia=True,<br/>                 isArchiveRenderCache=True,<br/>isArchiveProxyMedia=False) | Bool               | 使用可选参数提供的配置将项目归档到指定文件路径               |
| CreateProject(projectName)                                   | Project            | 如果 projectName (string) 是唯一的，则创建并返回一个项目，如果不是，则创建并返回 None |
| DeleteProject(projectName)                                   | Bool               | 如果当前未加载，则删除当前文件夹中的项目                     |
| LoadProject(projectName)                                     | Project            | 如果找到匹配项，则加载并返回名称为 projectName (string) 的项目，如果没有匹配的项目，则加载并返回 None |
| GetCurrentProject()                                          | Project            | 返回当前加载的 Resolve 项目                                  |
| SaveProject()                                                | Bool               | 使用自己的名称保存当前加载的项目。成功则返回真               |
| CloseProject(project)                                        | Bool               | 关闭指定项目并不保存                                         |
| CreateFolder(folderName)                                     | Bool               | 如果 folderName (string) 是唯一的，则创建一个文件夹          |
| DeleteFolder(folderName)                                     | Bool               | 删除指定的文件夹（如果存在）。成功则返回 True                |
| GetProjectListInCurrentFolder()                              | [project names...] | 返回当前文件夹中的项目名称列表                               |
| GetFolderListInCurrentFolder()                               | [folder names...]  | 返回当前文件夹中的文件夹名称列表                             |
| GotoRootFolder()                                             | Bool               | 打开数据库中的根文件夹                                       |
| GotoParentFolder()                                           | Bool               | 如果当前文件夹有父文件夹，则打开数据库中当前文件夹的父文件夹 |
| GetCurrentFolder()                                           | string             | 返回当前文件夹名称                                           |
| OpenFolder(folderName)                                       | Bool               | 打开指定名称的文件夹                                         |
| ImportProject(filePath, projectName=None)                    | Bool               | 从指定项目名称提供的文件路径导入项目（如果有）。成功则返回真 |
| ExportProject(projectName, filePath, withStillsAndLUTs=True) | Bool               | 如果 withStillsAndLUTs 为 True（默认启用），则将项目导出到提供的文件路径，包括静止图像和 LUT。成功则返回 True |
| RestoreProject(filePath, projectName=None)                   | Bool               | 从指定项目名称提供的文件路径恢复项目（如果有）。成功则返回真 |
| GetCurrentDatabase()                                         | {dbInfo}           | 返回与当前数据库连接相对应的字典（键为“DbType”、“DbName”和可选的“IpAddress”） |
| GetDatabaseList()                                            | [{dbInfo}]         | 返回与添加到 Resolve 的所有数据库相对应的字典项列表（带有键“DbType”、“DbName”和可选的“IpAddress”） |
| SetCurrentDatabase({dbInfo})                                 | Bool               | 将当前数据库连接切换到由以下键指定的数据库，并关闭任何打开的项目。<br/>'DbType'：'Disk'或'PostgreSQL'（字符串）<br/>'DbName'：数据库名称（字符串） <br/> 'IpAddress'：PostgreSQL 服务器的 IP 地址（字符串，可选键 - 默认为 '127.0.0.1'） |

### Project

| 方法                                                         | 返回值              | 作用                                                         |
| ------------------------------------------------------------ | ------------------- | ------------------------------------------------------------ |
| GetMediaPool()                                               | MediaPool           | 返回 MediaPool 对象                                          |
| GetTimelineCount()                                           | Int                 | 返回项目中当前存在的时间线数量                               |
| GetTimelineByIndex(idx)                                      | Timeline            | 返回给定索引处的时间线，1 <= idx <= project.GetTimelineCount() |
| GetCurrentTimeline()                                         | Timeline            | 返回当前加载的 Timeline 对象                                 |
| SetCurrentTimeline(timeline)                                 | Bool                | 将给定时间线设置为项目的当前时间线。成功则返回真             |
| GetGallery()                                                 | Gallery             | 返回 Gallery 对象                                            |
| GetName()                                                    | String              | 返回项目名称                                                 |
| SetName(projectName)                                         | Bool                | 如果 projectName (string) 是唯一的，则设置项目名称           |
| GetPresetList()                                              | [presets...]        | 返回预设列表及其信息                                         |
| SetPreset(presetName)                                        | Bool                | 将预设设置到项目中并命名为 presetName (string)               |
| AddRenderJob()                                               | String              | 将基于当前渲染设置的渲染作业添加到渲染队列。返回新渲染作业的唯一作业 id (string) |
| DeleteRenderJob(jobId)                                       | Bool                | 删除输入 jobid (string) 的渲染作业                           |
| DeleteAllRenderJobs()                                        | Bool                | 删除队列中的所有渲染作业                                     |
| GetRenderJobList()                                           | [render jobs...]    | 返回渲染作业列表及其信息                                     |
| GetRenderPresetList()                                        | [presets...]        | 返回渲染预设列表及其信息                                     |
| StartRendering(jobId1, jobId2, ...)                          | Bool                | 开始渲染由输入 jobId 指示的作业                              |
| StartRendering([jobIds...], isInteractiveMode=False)         | Bool                | 启动由输入 jobId 指示的渲染作业<br />可选的“isInteractiveMode”设置后，在渲染期间启用 UI 中的错误反馈 |
| StartRendering(isInteractiveMode=False)                      | Bool                | 开始渲染所有排队的渲染作业。 <br/>可选的“isInteractiveMode”设置后，会在渲染期间在 UI 中启用错误反馈 |
| StopRendering()                                              | None                | 停止任何当前渲染进程                                         |
| IsRenderingInProgress()                                      | Bool                | 如果渲染正在进行则返回 True                                  |
| LoadRenderPreset(presetName)                                 | Bool                | 如果存在 presetName (string)，则将预设设置为当前渲染预设     |
| SaveAsNewRenderPreset(presetName)                            | Bool                | 如果 presetName (string) 是唯一的，则按给定名称创建新的渲染预设 |
| SetRenderSettings({settings})                                | Bool                | 设置给定的渲染设置。 Settings 是一个字典，支持键：有关支持设置的信息，请参阅“查找渲染设置”部分 |
| GetRenderJobStatus(jobId)                                    | {status info}       | 通过给定的 jobId (string) 返回一个包含作业状态和作业完成百分比的字典 |
| GetSetting(settingName)                                      | String              | 返回项目设置的值（由 settingName，字符串表示）。查看以下部分以获取更多信息 |
| SetSetting(settingName, settingValue)                        | Bool                | 将项目设置（由 settingName，string 表示）设置为值（settingValue，string）。查看以下部分以获取更多信息 |
| GetRenderFormats()                                           | {render formats...} | 返回可用渲染格式的字典（格式 -> 文件扩展名）                 |
| GetRenderCodecs(renderFormat)                                | {render codecs...}  | 返回给定 renderFormat (string) 的可用编解码器的字典（编解码器描述 -> 编解码器名称） |
| GetCurrentRenderFormatAndCodec()                             | {format, codec}     | 返回当前选择的格式 'format' 和渲染编解码器 'codec' 的字典    |
| SetCurrentRenderFormatAndCodec(format, codec)                | Bool                | 将给定的format (string) 和 codec (string) 设置为渲染选项     |
| GetCurrentRenderMode()                                       | Int                 | 返回渲染模式：0 - 多个剪辑片段，1 - 单个剪辑片段             |
| SetCurrentRenderMode(renderMode)                             | Bool                | 设置渲染模式：renderMode = 0 为多个剪辑片段, 1 为单个剪辑片段 |
| GetRenderResolutions(format, codec)                          | [{Resolution}]      | 返回适用于给定 format (string) 和 codec (string) 的分辨率列表。 如果未提供参数，则返回完整的决议列表。列表中的每个元素都是一个带有 2 个键“宽度”和“高度”的字典 |
| RefreshLUTList()                                             | Bool                | 刷新 LUT 列表                                                |
| GetUniqueId()                                                | String              | 返回项目项的唯一 ID                                          |
| InsertAudioToCurrentTrackAtPlayhead(mediaPath, startOffsetInSamples, durationInSamples) | Bool                | 在 Fairlight 页面上选定轨道的播放头处插入由 mediaPath (string) 指定的媒体以及 startOffsetInSamples (int) 和 durationInSamples (int)。成功则返回 True，否则返回 False |

### Timeline

| Method                                                      | Return          | Function                                                     |
| ----------------------------------------------------------- | --------------- | ------------------------------------------------------------ |
| GetName()                                                   | String          | 返回时间线名称                                               |
| SetName(timelineName)                                       | Bool            | 如果 timelineName (string) 是唯一的，则设置时间线名称。 成功则返回真 |
| GetStartFrame()                                             | Int             | 返回时间线开始的帧号                                         |
| GetEndFrame()                                               | Int             | 返回时间线末尾的帧号                                         |
| SetStartTimecode(timecode)                                  | Bool            | 将时间线的开始时间码设置为字符串“timecode”。 更改成功时返回 true，否则返回 false |
| GetStartTimecode()                                          | String          | 返回时间线的开始时间码                                       |
| GetTrackCount(trackType)                                    | Int             | 返回给定轨道类型的轨道数 ("audio", "video" or "subtitle")    |
| GetItemListInTrack(trackType, index)                        | [items...]      | 返回该轨道上的时间线项目列表（基于轨道类型和索引）。1 <= index <= GetTrackCount(trackType) |
| AddMarker(frameId, color, name, note, duration, customData) | Bool            | 在给定的 frameId 位置和给定的标记信息创建一个新标记。'customData' 是可选的，有助于将用户特定数据附加到标记 |
| GetMarkers()                                                | {markers...}    | 返回所有标记和字典及其信息的字典（frameId -> {information}）<br />示例：值 {96.0: {'color': 'Green', 'duration': 1.0, 'note': '', 'name': 'Marker 1', 'customData': ''}, ...} 表示时间轴偏移 96 处的单个绿色标记 |
| GetMarkerByCustomData(customData)                           | {marker...}     | 返回具有指定 customData 的第一个匹配标记的标记 {information} |
| UpdateMarkerCustomData(frameId, customData)                 | Bool            | 为给定 frameId 位置的标记更新 customData (string)。CustomData 不通过 UI 公开，对于脚本开发人员将任何用户特定数据附加到标记非常有用 |
| GetMarkerCustomData(frameId)                                | String          | 返回给定 frameId 位置标记的 customData 字符串                |
| DeleteMarkersByColor(color)                                 | Bool            | 删除指定颜色的所有时间轴标记。支持“All"参数并删除所有时间轴标记 |
| DeleteMarkerAtFrame(frameNum)                               | Bool            | 删除给定帧号处的时间线标记                                   |
| DeleteMarkerByCustomData(customData)                        | Bool            | 删除具有指定 customData 的第一个匹配标记                     |
| ApplyGradeFromDRX(path, gradeMode, item1, item2, ...)       | Bool            | 从给定的 path (string) 加载静止图像并使用 gradeMode (int) 将等级应用于时间轴项目：0 - “无关键帧”，1 - “源时间码对齐”，2 - “起始帧对齐” |
| ApplyGradeFromDRX(path, gradeMode, [items])                 | Bool            | 从给定的 path (string) 加载静止图像并使用 gradeMode (int) 将等级应用于时间轴项目：0 - “无关键帧”，1 - “源时间码对齐”，2 - “起始帧对齐” |
| GetCurrentTimecode()                                        | String          | 返回当前播放头位置的字符串时间码表示，同时在剪切、编辑、颜色、Fairlight 和交付页面上 |
| SetCurrentTimecode(timecode)                                | Bool            | 根据剪切、编辑、颜色、Fairlight 和交付页面的输入时间码设置当前播放头位置 |
| GetCurrentVideoItem()                                       | Item            | 返回当前视频时间线 item                                      |
| GetCurrentClipThumbnailImage()                              | {thumbnailData} | 返回一个字典（键为“width”、“height”、“format”和“data”），其中数据包含彩色页面中当前媒体的原始缩略图图像数据（以 base64 格式编码的 RGB 8 位图像数据）<br / >示例文件夹中的 6_get_current_media_thumbnail.py 中提供了如何检索和解释缩略图的示例 |
| GetTrackName(trackType, trackIndex)                         | String          | 返回由 trackType（“audio”、“video”或“subtitle”）和索引指示的轨道的轨道名称。1 <= trackIndex <= GetTrackCount(trackType) |
| SetTrackName(trackType, trackIndex, name)                   | Bool            | 为由 trackType（“audio”、“video”或“subtitle”）和索引指示的轨道设置轨道名称（字符串）。1 <= trackIndex <= GetTrackCount(trackType) |
| DuplicateTimeline(timelineName)                             | timeline        | 复制时间线并返回创建的时间线，成功时带有（可选）timelineName |
| CreateCompoundClip([timelineItems], {clipInfo})             | timelineItem    | 使用可选的 clipInfo 映射创建输入时间线项目的复合剪辑：{"startTimecode" : "00:00:00:00", "name" : "Compound Clip 1"}。它返回创建的 timelineItem 对象 |
| CreateFusionClip([timelineItems])                           | timelineItem    | 创建输入时间线项目的 Fusion 剪辑。它返回创建的时间线项目     |
| ImportIntoTimeline(filePath, {importOptions})               | Bool            | 从 AAF 文件和可选的 importOptions dict 导入时间线项目，支持键：<br/>“autoImportSourceClipsIntoMediaPool”：Bool，指定源剪辑是否应导入媒体池，默认为 True<br/>“ ignoreFileExtensionsWhenMatching": Bool, 指定匹配时是否忽略文件扩展名，默认为False<br/>"linkToSourceCameraFiles": Bool, 指定是否应启用到源相机文件的链接，默认为False<br/>"useSizingInfo": Bool，指定是否应使用大小信息，默认为 False<br/>“importMultiChannelAudioTracksAsLinkedGroups”：Bool，指定是否应将多声道音轨导入为链接组，默认为 False<br/>“insertAdditionalTracks”：Bool， 指定是否应插入其他曲目，默认为 True<br/>“insertWithOffset”：字符串，指定以时间码格式插入偏移值 - 默认为“00:00:00:00”，如果“insertAdditionalTracks”为 False< br/>“搜 rceClipsPath"：字符串，如果媒体在其原始路径中不可访问且"ignoreFileExtensionsWhenMatching"为真，则指定用于搜索源剪辑的文件系统路径<br/>"sourceClipsFolders"：字符串，用于搜索源的媒体池文件夹对象列表 如果媒体不存在于当前文件夹中则剪辑 |
| Export(fileName, exportType, exportSubtype)                 | Bool            | 根据输入的 exportType 和 exportSubtype 格式将时间线导出到“文件名”<br />有关参数的信息，请参阅“查找时间线导出属性”部分 |
| GetSetting(settingName)                                     | String          | 返回时间线设置的值（由 settingName (string) 表示）。查看以下部分以获取更多信息 |
| SetSetting(settingName, settingValue)                       | Bool            | 将时间线设置（由 settingName (string) 表示）设置为值 (settingValue : string)。查看以下部分以获取更多信息 |
| InsertGeneratorIntoTimeline(generatorName)                  | TimelineItem    | 将生成器（由 generatorName (string) 表示）插入到时间线中     |
| InsertFusionGeneratorIntoTimeline(generatorName)            | TimelineItem    | 将 Fusion 生成器（由 generatorName (string) 表示）插入时间线 |
| InsertFusionCompositionIntoTimeline()                       | TimelineItem    | 将 Fusion 合成插入时间线                                     |
| InsertOFXGeneratorIntoTimeline(generatorName)               | TimelineItem    | 将 OFX 生成器（由 generatorName (string) 表示）插入时间线    |
| InsertTitleIntoTimeline(titleName)                          | TimelineItem    | 将标题（由 titleName (string) 表示）插入时间线               |
| InsertFusionTitleIntoTimeline(titleName)                    | TimelineItem    | 将 Fusion 标题（由 titleName (string) 表示）插入时间线       |
| GrabStill()                                                 | galleryStill    | 从当前视频剪辑中抓取静止图像。返回一个 GalleryStill 对象     |
| GrabAllStills(stillFrameSource)                             | [galleryStill]  | 从“stillFrameSource”（1 - 第一帧，2 - 中间帧）的时间线的所有剪辑中抓取静止图像。返回 GalleryStill 对象列表 |
| GetUniqueId()                                               | String          | 返回 Timeline 的唯一 ID                                      |

### TimelineItem

| 方法                                                        | 返回值            | 作用                                                         |
| ----------------------------------------------------------- | ----------------- | ------------------------------------------------------------ |
| GetName()                                                   | string            | 返回 TimelineItem 名称                                       |
| GetDuration()                                               | Int               | 返回 TimelineItem 持续时间                                   |
| GetEnd()                                                    | Int               | 返回时间线上的结束帧位置                                     |
| GetFusionCompCount()                                        | Int               | 返回与时间线 TimelineItem 关联的 Fusion 作品数               |
| GetFusionCompByIndex(compIndex)                             | fusionComp        | 返回基于给定索引的 fusionComp 对象。1 <= compIndex <= timelineItem.GetFusionCompCount() |
| GetFusionCompNameList()                                     | [names...]        | 返回与时间线项目关联的 Fusion 合成名称列表                   |
| GetFusionCompByName(compName)                               | fusionComp        | 返回基于给定名称的 FusionComp对象                            |
| GetLeftOffset()                                             | Int               | 返回剪辑从左侧按帧的最大扩展                                 |
| GetRightOffset()                                            | Int               | 从右侧返回剪辑的最大帧扩展                                   |
| GetStart()                                                  | Int               | 返回时间线上的起始帧位置                                     |
| SetProperty(propertyKey, propertyValue)                     | Bool              | 将属性“propertyKey”的值设置为值“propertyValue”<br />有关详细信息，请参阅“查找时间线项目属性” |
| GetProperty(propertyKey)                                    | Int / [key:value] | 返回指定键的值<br />如果没有指定键，该方法返回所有支持键的字典（python）或表（lua） |
| AddMarker(frameId, color, name, note, duration, customData) | Bool              | 在给定的 frameId 位置和给定的标记信息创建一个新标记。'customData' 是可选的，有助于将用户特定数据附加到标记 |
| GetMarkers()                                                | {markers...}      | 返回所有标记和字典及其信息的字典（frameId -> {information}）<br />示例：值 {96.0: {'color': 'Green', 'duration': 1.0, 'note': '', 'name': 'Marker 1', 'customData': ''}, ...} 表示剪辑偏移 96 处的单个绿色标记 |
| GetMarkerByCustomData(customData)                           | {markers...}      | 返回具有指定 customData 的第一个匹配标记的标记 {information} |
| UpdateMarkerCustomData(frameId, customData)                 | Bool              | 为给定 frameId 位置的标记更新 customData (string) 。CustomData 不通过 UI 公开，对于脚本开发人员将任何用户特定数据附加到标记非常有用 |
| GetMarkerCustomData(frameId)                                | String            | 返回给定 frameId 位置标记的 customData 字符串                |
| DeleteMarkersByColor(color)                                 | Bool              | 从时间线项目中删除所有指定颜色的标记。“All"作为参数删除所有颜色标记 |
| DeleteMarkerAtFrame(frameNum)                               | Bool              | 从 TimelineItem 中删除帧编号处的标记                         |
| DeleteMarkerByCustomData(customData)                        | Bool              | 删除具有指定 customData 的第一个匹配标记                     |
| AddFlag(color)                                              | Bool              | 添加具有给定 color (string) 的标志                           |
| GetFlagList()                                               | [colors...]       | 返回分配给 TimelineItem 的标志颜色列表                       |
| ClearFlags(color)                                           | Bool              | 清除指定颜色的标志。支持“all”参数以清除所有标志              |
| GetClipColor()                                              | String            | 以字符串形式返回 TimelineItem 颜色                           |
| SetClipColor(colorName)                                     | Bool              | 根据 colorName (string) 设置 TimelineItem 颜色               |
| ClearClipColor()                                            | Bool              | 清除 TimelineItem 颜色                                       |
| AddFusionComp()                                             | fusionComp        | 添加与 TimelineItem 关联的新 Fusion 合成                     |
| ImportFusionComp(path)                                      | fusionComp        | 通过为项目创建和添加新组合，从给定文件路径导入 Fusion 组合   |
| ExportFusionComp(path, compIndex)                           | Bool              | 将基于给定 compIndex 的 Fusion 组合导出到提供的 path         |
| DeleteFusionCompByName(compName)                            | Bool              | 删除命名为 compName 的 Fusion 合成                           |
| LoadFusionCompByName(compName)                              | fusionComp        | 将命名为 fusionComp 的 Fusion 合成加载为活动合成             |
| RenameFusionCompByName(oldName, newName)                    | Bool              | 重命名由 oldName 标识的 Fusion 合成                          |
| AddVersion(versionName, versionType)                        | Bool              | 根据 versionType（0 - 本地，1 - 远程）为视频剪辑片段添加新的颜色版本 |
| GetCurrentVersion()                                         | {versionName...}  | 返回视频剪辑片段的当前版本。 返回值将包含键 versionName 和 versionType（0 - 本地，1 - 远程） |
| DeleteVersionByName(versionName, versionType)               | Bool              | 按 versionName 和 versionType (0 - 本地，1 - 远程) 删除颜色版本 |
| LoadVersionByName(versionName, versionType)                 | Bool              | 加载命名为 versionName 的颜色版本作为活动版本。versionType: 0 - 本地，1 - 远程 |
| RenameVersionByName(oldName, newName, versionType)          | Bool              | 重命名由 oldName 和 versionType (0 - 本地，1 - 远程) 标识的颜色版本 |
| GetVersionNameList(versionType)                             | [names...]        | 返回给定 versionType (0 - 本地，1 - 远程) 的所有颜色版本的列表 |
| GetMediaPoolItem()                                          | MediaPoolItem     | 返回与时间线项目对应的 MediaPoolItem 对象（如果存在）        |
| GetStereoConvergenceValues()                                | {keyframes...}    | 返回关键帧偏移量和相应收敛值的字典 (offset -> value)         |
| GetStereoLeftFloatingWindowParams()                         | {keyframes...}    | 对于左眼 - >返回密钥帧偏移和相应浮动窗口参数的dict（offset - > dict）。 特定偏移的值包括左，右，顶部和底部浮动窗口值 |
| GetStereoRightFloatingWindowParams()                        | {keyframes...}    | 对于右眼 - >返回密钥帧偏移和相应浮动窗口参数的dict（offset - > dict）。 特定偏移的值包括左，右，顶部和底部浮动窗口值 |
| GetNumNodes()                                               | Int               | 返回时间线当前图中的节点数量                                 |
| SetLUT(nodeIndex, lutPath)                                  | Bool              | 在节点映射上提供的节点索引，1 <= nodeIndex <=节点总数<br/>可以是绝对路径，或者是相对路径（基于自定义LUT路径或主LUT路径）<br/>对于解决方案已经发现的有效LUT路径成功了（请参阅Project.refreshlutlist） |
| GetLUT(nodeIndex)                                           | String            | 根据提供的节点索引获取相对LUT路径，1 <= NodeIndex <=节点总数 |
| SetCDL([CDL map])                                           | Bool              | 映射的键是：“ nodeIndex”，“ slope”，“ offset”，“ power”，“Saturation”，其中1 <= nodeIndex <= 节点的总数。<br/>示例 python 代码 - SetCDL({"NodeIndex" : "1", "Slope" : "0.5 0.4 0.2", "Offset" : "0.4 0.3 0.2", "Power" : "0.6 0.7 0.8", "Saturation" : "0.65"}) |
| AddTake(mediaPoolItem, startFrame, endFrame)                | Bool              | 将 mediaPoolIitem 添加为新 take。 如果需要，将时间表项初始化一个时间轴项。 默认情况下，添加了完整的剪辑扩展。startFrame (int) 和endFrame (int) 是用于指定范围的可选参数 |
| GetSelectedTakeIndex()                                      | Int               | 返回当前选择 take 的索引，或者如果剪辑不是 take 选择器，则返回0 |
| GetTakesCount()                                             | Int               | 返回 take 选择器的数量，或者如果剪辑不是 take 选择器，则返回0 |
| GetTakeByIndex(idx)                                         | {takeInfo...}     | 返回还有指定 take 索引信息的字典 (keys "startFrame", "endFrame" and "mediaPoolItem") |
| DeleteTakeByIndex(idx)                                      | Bool              | 通过索引删除 take，1 <= idx <= take 的数量                   |
| SelectTakeByIndex(idx)                                      | Bool              | 通过索引选择一个 take，1 <= idx <= take 的数量               |
| FinalizeTake()                                              | Bool              | 最终确定 take                                                |
| CopyGrades([tgtTimelineItems])                              | Bool              | 将当前等级复制到 tgtTimelineItems 列表中的所有项目。 在成功时返回 True，如果发生任何错误返回 False |
| UpdateSidecar()                                             | Bool              | 更新用于 R3D 剪辑片段的 BRAW 剪辑片段或 RMD 文件的 sidecar 文件 |
| GetUniqueId()                                               | String            | 返回 TimelineItem 的唯一ID                                   |
