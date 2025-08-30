# 前言

Vuex 是构建客户端 Web 应用的集中式状态管理架构。它利用 Vue.js 的反应性系统，完美地集成到 Vue.js 应用中。

在这本书中，你将了解为什么集中式状态管理很重要，它是如何工作的，以及如何使用 Vuex 赋予你的 Vue.js 应用更多功能。最后，你将了解 Vuex 插件系统，并学习如何使用它来丰富你的 Vuex 应用。

# 本书面向对象

本书针对希望理解和在应用中使用 Vuex 集中式状态管理架构的 Vue.js 开发者。

# 本书涵盖内容

第一章，*用 Flux、Vue 和 Vuex 重新思考用户界面*，介绍了 Flux 架构的概念以及 Vuex 实现中的细微差别。

第二章，*使用 Vuex 实现 Flux 架构*，讲解了 Vuex 的核心概念，并通过一些小型可执行示例，我们学习了如何使用它们。

第三章，*设置开发和测试环境*，展示了如何使用 webpack 和 npm 为开发 Vue/Vuex 应用准备我们的环境。

第四章，*使用 Vuex 状态管理编写 EveryNote 应用*，解释了如何开发一个笔记应用，使用我们刚刚学到的 Vuex 的所有概念。此外，我们还使用 karma 测试应用的所有组件。

第五章，*调试 Vuex 应用*，指出即使测试了每个组件，有时调试也是必要的。我们将了解如何使用 Chrome 开发者工具和 vue-devtools 来调试我们的应用。

第六章，*使用 Vuex 插件系统*，通过一些有用的 Vuex 插件丰富了 EveryNote 应用，包括从头开始开发的两个自定义插件。

# 为了充分利用本书

在这本书中，我们假设读者对 Vue.js 框架有良好的了解，对 JavaScript 有良好的了解，并对 EcmaScript 6 有基本了解。

此外，为了运行示例，读者需要安装 Node.js，并对**node 包管理器**（**npm**）有非常基本的了解。

最后，为了使用这本书的 Git 仓库，用户需要安装 Git，并对 Git 命令有基本了解。

# 下载示例代码文件

你可以从你的账户在 [www.packtpub.com](http://www.packtpub.com) 下载这本书的示例代码文件。如果你在其他地方购买了这本书，你可以访问 [www.packtpub.com/support](http://www.packtpub.com/support) 并注册，以便将文件直接通过电子邮件发送给你。

你可以通过以下步骤下载代码文件：

1.  在 [www.packtpub.com](http://www.packtpub.com/support) 登录或注册。

1.  选择“支持”标签。

1.  点击“代码下载与勘误”。

1.  在搜索框中输入书籍名称，并按照屏幕上的说明操作。

文件下载完成后，请确保使用最新版本解压缩或提取文件夹：

+   适用于 Windows 的 WinRAR/7-Zip

+   适用于 Mac 的 Zipeg/iZip/UnRarX

+   适用于 Linux 的 7-Zip/PeaZip

本书代码包也托管在 GitHub 上，网址为 [`github.com/PacktPublishing/Vuex-Quick-Start-Guide`](https://github.com/PacktPublishing/Vuex-Quick-Start-Guide)。我们还有其他来自我们丰富图书和视频目录的代码包可供下载，网址为 **[`github.com/PacktPublishing/`](https://github.com/PacktPublishing/)**。请查看它们！

# 下载彩色图像

我们还提供了一份包含本书中使用的截图/图表彩色图像的 PDF 文件。您可以从这里下载：[`www.packtpub.com/sites/default/files/downloads/VuexQuickStartGuide`](http://www.packtpub.com/sites/default/files/downloads/VuexQuickStartGuide)。

# 代码实战

访问以下链接查看代码运行的视频：

[`goo.gl/8WLxhs`](https://goo.gl/8WLxhs)

# 使用的约定

本书使用了多种文本约定。

`CodeInText`：表示文本中的代码单词、数据库表名、文件夹名、文件名、文件扩展名、路径名、虚拟 URL、用户输入和 Twitter 昵称。以下是一个示例：“将下载的 `WebStorm-10*.dmg` 磁盘映像文件作为系统中的另一个磁盘挂载。”

代码块设置如下：

```js
html, body, #map {
 height: 100%; 
 margin: 0;
 padding: 0
}
```

当我们希望您注意代码块中的特定部分时，相关的行或项目将以粗体显示：

```js
[default]
exten => s,1,Dial(Zap/1|30)
exten => s,2,Voicemail(u100)
exten => s,102,Voicemail(b100)
exten => i,1,Voicemail(s0)
```

任何命令行输入或输出都如下所示：

```js
$ mkdir css
$ cd css
```

**粗体**：表示新术语、重要单词或您在屏幕上看到的单词。例如，菜单或对话框中的单词在文本中显示如下。以下是一个示例：“从管理面板中选择系统信息。”

警告或重要提示如下所示。

技巧和窍门如下所示。

# 联系我们

我们始终欢迎读者的反馈。

**总体反馈**：请将邮件发送至 `feedback@packtpub.com`，并在邮件主题中提及书籍标题。如果您对本书的任何方面有疑问，请通过 `questions@packtpub.com` 发送邮件给我们。

**勘误**：尽管我们已经尽最大努力确保内容的准确性，但错误仍然可能发生。如果您在这本书中发现了错误，我们将非常感激您能向我们报告。请访问 [www.packtpub.com/submit-errata](http://www.packtpub.com/submit-errata)，选择您的书籍，点击勘误提交表单链接，并输入详细信息。

**盗版**：如果您在互联网上以任何形式发现我们作品的非法副本，我们将非常感激您能提供位置地址或网站名称。请通过 `copyright@packtpub.com` 联系我们，并提供材料的链接。

**如果您有兴趣成为作者**：如果您在某个领域有专业知识，并且对撰写或参与一本书籍感兴趣，请访问[authors.packtpub.com](http://authors.packtpub.com/).

# 评论

请留下评论。一旦您阅读并使用了这本书，为何不在购买它的网站上留下评论呢？潜在读者可以查看并使用您的客观意见来做出购买决定，我们 Packt 可以了解您对我们产品的看法，我们的作者也可以看到他们对书籍的反馈。谢谢！

如需了解 Packt 的更多信息，请访问[packtpub.com](https://www.packtpub.com/).
