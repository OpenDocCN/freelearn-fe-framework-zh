# 附录 A. 任务管理应用程序源代码

本书构建的任务管理应用程序的源代码可在 Packt 下载服务器上获取。每个章节最终代码的链接列在本附录中。本附录还提供了如何使用下载的代码以及运行代码所需的步骤说明。此外，它还帮助您解决在使用任务管理应用程序时可能遇到的一些常见问题，并提供了一些解决问题的提示。

# 下载

以下列表提供了本书每个章节的下载链接。下载链接引用可下载的存档文件，需要在您的本地硬盘上解压：

| 章节 | 链接 |
| --- | --- |
| 第二章，*准备，出发!* | [`github.com/PacktPublishing/Mastering-Angular-2-Components/tree/master/angular-2-components-chapter-2`](https://github.com/PacktPublishing/Mastering-Angular-2-Components/tree/master/angular-2-components-chapter-2) |
| 第三章，*使用组件进行创作* | [`github.com/PacktPublishing/Mastering-Angular-2-Components/tree/master/angular-2-components-chapter-3`](https://github.com/PacktPublishing/Mastering-Angular-2-Components/tree/master/angular-2-components-chapter-3) |
| 第四章，*请勿添加注释!* | [`github.com/PacktPublishing/Mastering-Angular-2-Components/tree/master/angular-2-components-chapter-4`](https://github.com/PacktPublishing/Mastering-Angular-2-Components/tree/master/angular-2-components-chapter-4) |
| 第五章，*基于组件的路由* | [`github.com/PacktPublishing/Mastering-Angular-2-Components/tree/master/angular-2-components-chapter-5`](https://github.com/PacktPublishing/Mastering-Angular-2-Components/tree/master/angular-2-components-chapter-5) |
| 第六章，*跟随活动* | [`github.com/PacktPublishing/Mastering-Angular-2-Components/tree/master/angular-2-components-chapter-6`](https://github.com/PacktPublishing/Mastering-Angular-2-Components/tree/master/angular-2-components-chapter-6) |
| 第七章，*用户体验组件* | [`github.com/PacktPublishing/Mastering-Angular-2-Components/tree/master/angular-2-components-chapter-7`](https://github.com/PacktPublishing/Mastering-Angular-2-Components/tree/master/angular-2-components-chapter-7) |
| 第八章，*时间会证明一切* | [`github.com/PacktPublishing/Mastering-Angular-2-Components/tree/master/angular-2-components-chapter-8`](https://github.com/PacktPublishing/Mastering-Angular-2-Components/tree/master/angular-2-components-chapter-8) |
| 第九章，*宇宙飞船仪表盘* | [`github.com/PacktPublishing/Mastering-Angular-2-Components/tree/master/angular-2-components-chapter-9`](https://github.com/PacktPublishing/Mastering-Angular-2-Components/tree/master/angular-2-components-chapter-9) |
| 第十章，*使事物可插拔* | [`github.com/PacktPublishing/Mastering-Angular-2-Components/tree/master/angular-2-components-chapter-10`](https://github.com/PacktPublishing/Mastering-Angular-2-Components/tree/master/angular-2-components-chapter-10) |
| 第十一章，*对事物进行测试* | [`github.com/PacktPublishing/Mastering-Angular-2-Components/tree/master/angular-2-components-chapter-11`](https://github.com/PacktPublishing/Mastering-Angular-2-Components/tree/master/angular-2-components-chapter-11) |

完整的源代码也可在[`www.packtpub.com`](http://www.packtpub.com)找到。

# 先决条件

任务管理应用程序是使用 Node.js 技术构建的；因此，在您能够运行任何代码之前，您需要在您的机器上安装 Node.js。

您可以从网站[`nodejs.org`](http://nodejs.org)下载并安装 Node.js。为了构建 Sass 源文件并启动具有实时重载的服务器，需要两个全局 node 模块：

```js
npm install -g gulp live-server

```

# 使用方法

在下载单个章节的源代码后，您需要安装 NPM 以及 JSPM 依赖项。通过在您的控制台运行以下两行代码，您将确保所有依赖项都已安装。请确保您从下载并解压的代码文件夹内运行这些命令：

```js
npm install
jspm install

```

在您安装了所需的依赖项之后，您可以继续启动应用程序：

```js
npm start

```

NPM start 脚本将调用 gulp 以编译任何 Sass 文件以及启动具有实时重载的静态服务器。请阅读先决条件主题了解如何安装这些全局 Node.js 模块。

# 故障排除

我们已经仔细考虑了任务管理源代码将在其中执行的各种环境。然而，在处理源代码时，您可能会遇到一些问题。本主题将为您提供在处理任务管理应用程序时常见问题的解决方案。

## 清理 IndexDB 以重置数据

任务管理应用程序使用一个持久保存在你浏览器中的数据存储。如果你在多个章节中广泛使用该应用程序，那么你的数据很可能导致一些应用程序不稳定。

如果你想要清理应用程序中使用的本地数据库，你可以在浏览器调试控制台中运行以下代码行。重要的是，你需要在使用任务管理应用程序打开的情况下，在浏览器的调试器中运行代码片段。如果你的浏览器指向不同的源，应用程序数据库将无法删除。删除数据库后，应用程序可以重新加载，并且它会使用初始样本数据重新创建数据库：

```js
indexedDB.deleteDatabase('_pouch_angular-2-components');
```

## 在 Firefox 中启用 web 组件

第六章, *保持活动*, 依赖于浏览器中 Shadow DOM 的存在。Chrome 从 35 版本开始原生支持 Shadow DOM。在 Firefox 中，你可以通过访问`about:config`页面并开启`dom.webcomponents.enabled`标志来启用 Shadow DOM。

IE、Edge 和 Safari 完全不支持这个标准；然而，我们可以通过包含一个名为`webcomponents.js`的 polyfill 来教它们如何处理 Shadow DOM。你可以在[`github.com/webcomponents/webcomponentsjs`](https://github.com/webcomponents/webcomponentsjs)找到更多关于这个 polyfill 的信息。
