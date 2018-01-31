---
title: VS Code - Debugger for Chrome（文档中文翻译）
tags: [visual studio code, chrome, debugger plugin]
date: 2018-01-31 21:48:47
author: 肖大宝
---

这是VSCode的插件———Debugger for Chrome的中文文档
![demo](https://cdn-images-1.medium.com/max/1206/1*lCs395mV1NHp_NEtNDwV2A.png)

<!-- more -->

# VS Code - Debugger for Chrome（中文文档）

> 从VS Code调试在Google Chrome中运行的JavaScript代码。

一个VSCode代码扩展，用于在Google Chrome浏览器或其他支持[ChromeDev Tools协议](https://chromedevtools.github.io/devtools-protocol/)的目标中调试JavaScript代码。

![demo](https://github.com/Microsoft/vscode-chrome-debug/blob/master/images/demo.gif?raw=true)


### 支持的特性

- 设置断点，包括启用源映射（source map）的源文件

- 单步调试，包括Chrome页面上的按钮

- 本地窗格（The Locals pane）

- 调试eval脚本（eval scripts）、脚本标签（script tags）和动态添加的脚本

- 监听

- 控制台（console）


#### 不支持的方案

- 调试web worker

- 任何不是脚本调试的功能（css、network等） 



## 开始

为了使用Debugger拓展，您首先应该用VSCode打开您的项目。


## 使用Debugger

当启动配置设置完成后，您可以调试您的项目。 在VSCode的调试界面的下拉列表中选择一个启动配置。点击播放按钮或F5以启动。


### 配置Debugger

Debugger有两种运行模式——可以启动一个导航到您的应用程序的Chrome实例（launch模式），或者可以依赖一个正在运行的Chrome实例（attach模式）。 这两种模式都要求您为您的Web应用程序启动一个从Web服务，该服务可以从VSCode任务启动或从命令行启动。使用url参数您就能告诉VSCode哪个url需要在Chrome中打开或启动。


### Launch模式

在`launch.json`文件中配置`"request": "launch"`时，有两种配置范例。你必须指定一个文件或者url来启动Chrome，该Chrome指向一个本地文件或者一个url。如果你使用一个url，将设置webRoot属性为需要提供服务的文件的路径。它既可以是一个绝对路径，也可以是一个使用${workspaceFolder}（VScode锁打开的文件夹）的路径。webRoot是用来解析url（比如“http：//localhost/app.js”）到磁盘上对应文件（比如“/Users/me/project/app.js”）的属性，需要确保他被正确设置。

```json
{
    "version": "0.1.0",
    "configurations": [
        {
            "name": "Launch localhost",
            "type": "chrome",
            "request": "launch",
            "url": "http://localhost/mypage.html",
            "webRoot": "${workspaceFolder}/wwwroot"
        },
        {
            "name": "Launch index.html (disable sourcemaps)",
            "type": "chrome",
            "request": "launch",
            "sourceMaps": false,
            "file": "${workspaceFolder}/index.html"
        },
    ]
}
```

如果您想要使用不同的Chrome浏览器（此处应该指的是使用Chrome的浏览器），则还可以用Chrome浏览器的安装路径设置runtimeExecutable字段。

> Chrome用户配置文件备注：通常情况下，如果您以launch配置模式开始调试时，Chrome已经在运行，那么新的实例将不会以远程调试模式启动。 因此，默认情况下，Debugger会在临时文件夹中使用单独的用户配置文件启动Chrome（除非您使用runtimeExecutable字段）。 使用userDataDir启动配置字段可以覆盖或禁用此效果。


### Attach模式

在配置`"request": "attach"`时，为了能让Debugger可以附着（attach）到Chrome上，您必须打开远程调试模式来启动Chrome。下面是在不同系统下进行配置：

#### windows

- 右击Chrome图标，选择属性

- 在目标输入框中，追加`--remote-debugging-port=9222`

- 或者在命令提示符中，执行`<path to chrome>/chrome.exe --remote-debugging-port=9222`命令

#### macOS

- 在终端中，执行`/Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome --remote-debugging-port=9222`命令

#### Linux

- 在终端中，运行`google-chrome --remote-debugging-port=9222`命令

如果您有一个Chrome实例正在运行并且不想重启它，则可以使用`--user-data-dir`选项在单独的用户配置文件下运行新实例。例如：`--user-data-dir=/tmp/chrome-debug`。 这与在launch模式中使用`userDataDir`选项相同。

运行Chrome并导航到你的页面。

下面是一个在attach模式下的`launch.json`文件示例：

```json
{
    "version": "0.1.0",
    "configurations": [
        {
            "name": "Attach",
            "type": "chrome",
            "request": "attach",
            "port": 9222,
            "url": "<url of the open browser tab to connect to>",
            "webRoot": "${workspaceFolder}"
        },
        {
            "name": "Attach to url with files served from ./out",
            "type": "chrome",
            "request": "attach",
            "port": 9222,
            "url": "<url of the open browser tab to connect to>",
            "webRoot": "${workspaceFolder}"
        }
    ]
}
```


### 其他目标程序

理论上，您也可以在同样支持Chrome调试协议的其他目标程序，例如Electron或Cordova中，使用Debugger。这些并非官方支持，但在基本相同的步骤下应该可以运行。您可以通过launch配置将`"runtimeExecutable"`设置为要启动的程序或脚本，或通过attach配置附着到一个已经运行的进程。 如果VSCode无法找到目标程序，则可以通过在浏览器中导航到`http://localhost:<port>/json`来始终验证其是否可用。如果你得到了有一堆JSON的响应，并且可以在JSON中找到你的目标页面，那么目标应该可用于这个扩展。


### 示例

请阅读我们的wiki页面，那里提供了一些配置过得示例应用：[示例](https://github.com/Microsoft/vscode-chrome-debug/wiki/Examples)


### 其他可选的launch配置字段

- `trace`：如果设置为true，则适配器会将自己的诊断信息记录到文件中。文件路径将打印在调试控制台中。当需要在GitHub上提交issue时，此文件中通常包含有用信息。如果您将其设置为“verbose”，它也会将诊断信息打印到控制台。

- `runtimeExecutable`：运行可执行文件要使用的的工作空间（Workspace）相对或绝对路径。如果未指定，Chrome将从默认安装位置使用。

- `runtimeArgs`：传递给运行可执行文件的可选参数。

- `env`：环境键/值对的可选字典。

- `cwd`：运行可执行文件的可选工作目录。

- `userDataDir`：通常情况下，如果您使用launch配置开始进行调试时，Chrome已经在运行，那么新的实例将不会以远程调试模式启动。因此，默认情况下，Debugger会在临时文件夹中使用单独的用户配置文件启动Chrome。使用此选项来设置想要使用的其他路径，或设置为false以使用默认用户配置文件启动。

- `url`：在launch配置下，将在此url上启动Chrome。

- `urlFilter`：在attach配置或者没有设置url字段的launch配置的情况下，搜索带有这个url的页面并附着于它。它也可以包含通配符，例如，`"localhost:*/app"`将匹配`"http://localhost:123/app"`或`"http://localhost:456/app"`，但不包含`"https://stackoverflow.com"`。

- `sourceMaps`：默认情况下，适配器将尽可能使用源映射（Sourcemaps）和原始源代码。您可以通过将`sourceMaps`设置为false来禁用此功能。

- `pathMapping`：此属性将url路径映射到本地路径，以便在如何将url解析为本地文件方面，为您提供更大的灵活性。`"webRoot": "${workspaceFolder}"`就是`{ "/": "${workspaceFolder}" }`的简写。

- `smartStep`：自动跨越没有映射到源文件的代码。特别适用于使用async/await进行调试。

- `disableNetworkCache`：如果为true，网络缓存将被禁用。

- `showAsyncStacks`：如果为true，跨异步调用（如`setTimeout`，`fetch`，解析Promise等）将显示调用堆栈。


## 跳过 文件/黑箱/忽略文件

您可以使用`skipFiles`属性在调试时忽略/黑盒特定的文件。例如，如果您设置了`"skipFiles": ["jquery.js"]`，那么您将在单步调试代码时跳过任何名为“jquery.js”的文件。您也不会打断从“jquery.js”中抛出的异常。这与Chrome DevTools中的“黑盒子脚本”相同。

支持的格式有：

- 文件名（如`jquery.js`）

- 文件夹名，将跳过其下所有的脚本（如`node_modules`）

- glob路径，跳过所有匹配的脚本（如`node_modules/react/*.min.js`）


## 页面刷新

Debugger还能使您通过在调试界面中点击重启按钮来刷新页面目标。此外，您可以使用以下属性将刷新操作映射到您最喜欢的键盘快捷方式：

```json
{
    "key": "ctrl+r",
    "command": "workbench.action.debug.restart",
    "when": "inDebugMode"
}
```

此处查看更多相关信息[https://github.com/Microsoft/vscode-chrome-debug-core/issues/91#issuecomment-265027348](https://github.com/Microsoft/vscode-chrome-debug-core/issues/91#issuecomment-265027348)


## 源映射Sourcemaps

Debugger使用sourcemaps来让你调试你的源代码，但有时sourcemaps不能正确生成并需要覆盖。在配置中，我们支持`sourceMapPathOverrides`属性。此属性可以设置来自sourcemap的源路径到磁盘上这些源代码路径的映射表。当sourcemap不准确或无法在构建过程中修复时有用。

映射表的左侧可以包含通配符模式，并将针对源映射中的`sourceRoot`+`sources`条目进行测试。如果匹配，则源文件将被解析到映射表右侧的路径，该路径应该是磁盘上源文件的绝对路径。

默认情况下会使用一些映射，这些影射对应于Webpack和Meteor的默认配置：

```json
"sourceMapPathOverrides": {
    "webpack:///./~/*": "${webRoot}/node_modules/*",       // Example: "webpack:///./~/querystring/index.js" -> "/Users/me/project/node_modules/querystring/index.js"
    "webpack:///./*":   "${webRoot}/*",                    // Example: "webpack:///./src/app.js" -> "/users/me/project/src/app.js",
    "webpack:///*":     "*",                               // Example: "webpack:///C:/project/app.ts" -> "C:/project/app.ts"
    "webpack:///src/*": "${webRoot}/*",                    // Example: "webpack:///src/App.js" -> "C:/project/src/App.js"
    "meteor://💻app/*": "${webRoot}/*"                    // Example: "meteor://💻app/main.ts" -> "c:/code/main.ts"
}
```

如果您在启动配置中设置了`sourceMapPathOverrides`，则会覆盖上面这些默认设置。`${workspaceFolder}`和`$ {webRoot}`可以在这里使用。如果您不能确定映射表左侧是什么，您可以使用`.scripts`命令（下面有详述）。您也可以使用`trace`选项来查看sourcemap的内容，或者查看Chrome DevTools中源代码的路径，或者打开`.js.map`文件并手动检查值。


### Ionic/gulp-sourcemaps注意事项

Ionic和gulp-sourcemaps默认输出`"/source/"`的sourceRoot。 如果你不能通过你的构建配置来解决这个问题，我建议如下设置：

```json
"sourceMapPathOverrides": {
    "/source/*": "${workspaceFolder}/*"
}
```


## 故障排除Troubleshooting


### 我的断点不能命中。 怎么了？

如果你的断点没有被触发，这很可能是sourcemapping问题，或者是因为你在立即执行的代码中有断点。例如，如果您在页面加载时运行的渲染函数中有断点，则在代码执行之前，Debugger可能未能附加到Chrome。这意味在VSCode附着到你打的断点后，你必须刷新Chrome中的页面。 我们正在通过[https://github.com/Microsoft/vscode-chrome-debug/issues/445](https://github.com/Microsoft/vscode-chrome-debug/issues/445)中的“中断加载”断点来简化这个过程，这将使这个和时机有关的问题变得明晰。

如果您有sourcemapping问题，请参阅[https://github.com/Microsoft/vscode-chrome-debug#sourcemaps](https://github.com/Microsoft/vscode-chrome-debug#sourcemaps)


### 无法连接到目标：connect CONNREFUSED 127.0.0.1:9222

此消息表示扩展程序无法附着到Chrome，因为Chrome未在调试模式下启动。请做如下尝试：

- 如果使用`attach`类型配置，请确保您使用`--remote-debugging-port=9222`启动了Chrome。如果已经有一个正在运行的实例，请参阅上文的内容。

- 确保`port`属性与Chrome正在侦听的远程调试连接的端口匹配。默认是`9222`端口。确保没有别的应用程序使用这个端口，包括你的网络服务器。如果您的计算机上的其他内容以`http://localhost:9222`响应，请设置其他端口。

- 如果一切都失败了，当你看到此信息时，请尝试在浏览器中浏览`http://localhost:<port>/json`，如果没有响应，则在Debugger的上游出现了错误。如果返回了一个JSON页面，请确保launch配置中的`port`与该url中的端口匹配。

- 如果在显式禁用`userDataDir`选项的情况下使用`launch`类型配置，请关闭其他正在运行的Chrome实例 - 如果Chrome已在运行，则在使用launch模式时，Debugger可能无法附着。 Chrome甚至可以在所有窗口关闭的情况下保持在后台运行，这会造成干扰——检查任务栏并在必要时终止进程。



### 如果你有问题，一般要尝试的事项：

- 如果需要，确保`webRoo`t设置正确

- 仔细看看你的sourcemap配置。sourcemap有一个源文件的路径，Debugger使用该路径来查找磁盘上的原始源文件。检查sourcemap中的`sourceRoot`和`sources`属性，并确保它们可以与启动配置中的`webRoot`属性组合，以构建原始源文件的正确路径。

- Debugger忽略sourcemap中内联源代码模式——您有可能在Chrome Dev Tools中使用此模式，但Debugger不支持，因为路径不正确，但Chrome Dev Tools正在读取内联的源内容。

- 当Debugger无法附着时，有时可以查看Debugger在控制台打印的警告。

- 确保Chrome中的代码与VSCode中的代码相符。Chrome可能会缓存旧版本代码。

- 如果您的断点绑定，但没有命中，请尝试刷新页面。如果您在页面加载时立即运行的代码中设置断点，则在刷新页面之前，该断点不能被命中。

- 在Debugger的[GitHub仓库](https://github.com/Microsoft/vscode-chrome-debug)中提交bug，要包括调试适配器日志文件。通过在launch配置中设置“跟踪”字段并重现问题来创建日志文件。在Debug Console的顶部将打印日志文件的路径。您可以将此文件拖到issue评论中，以将其上传到GitHub。


### `.script`命令

此功能对于了解Debugger如何将工作区中的文件映射到Chrome中运行的文件非常有用。您可以在 Debug Console中输入.scripts，以查看运行时加载的所有脚本的列表，它们的sourcemap信息，以及它们如何映射到磁盘上的文件。格式是这样的：

```
› <The exact URL for a script, reported by Chrome> (<The local path that has been inferred for this script, using webRoot, if applicable>)
    - <The exact source path from the sourcemap> (<The local path inferred for the source, using sourceMapPathOverrides, or webRoot, etc, if applicable>)
```

示例：

```
.scripts
› eval://43
› http://localhost:8080/index.html (/Users/me/project/wwwroot/index.html)
› http://localhost:8080/out/test1.js (/Users/me/project/wwwroot/out/test1.js)
    - /src/test1a.ts (/Users/me/project/wwwroot/src/test1a.ts)
    - /src/test1b.ts (/Users/me/project/wwwroot/src/test1b.ts)
    - /src/test1c.ts (/Users/me/project/wwwroot/src/test1c.ts)
› http://localhost:8080/out/test2.js (/Users/me/project/wwwroot/out/test2.js)
    - /src/test2.ts (/Users/me/project/wwwroot/src/test2.ts)
```

如果你想知道一个脚本是什么，例如那个'eval'脚本，你也可以使用`.scripts`来获取它的内容：`.scripts eval://43`。

===此项目采用了[微软开放源代码行为准则](https://opensource.microsoft.com/codeofconduct/)。 欲了解更多信息，请参阅行为[准则常见问题](https://opensource.microsoft.com/codeofconduct/faq/)或联系[opencode@microsoft.com](https://github.com/Microsoft/vscode-chrome-debug/blob/master/mailto:opencode@microsoft.com)与任何其他问题或意见。