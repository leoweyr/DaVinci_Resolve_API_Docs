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
