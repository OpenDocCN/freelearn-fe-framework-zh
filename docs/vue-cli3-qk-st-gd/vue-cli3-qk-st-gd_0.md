# 前言

Vue 最初是由一个人 Evan You 发起的项目。令人惊讶的是，它已经发展到今天这个地步：成为最受欢迎的前端框架之一，与由公司支持的 React 和 Angular 竞争。

当然，这些并不是唯一的前端框架，但 Vue、React 和 Angular 这三者似乎是最受欢迎的，互联网上充斥着这些框架的比较和使用经验。比如，很常见的是看到一篇比较 Vue 和 React 的文章，或者一篇关于 Vue 比 Angular 更好的博客文章。无论这些文章是某人的观点，还是标题党，或者是事实陈述，这些说法肯定有一些真实性。

Vue 成功的原因是什么？是奉献、努力工作还是运气？可能都有一点。但 Vue 成功的另一个关键是 Evan 明显地优先考虑了为开发人员简化事情。Vue 不再是由一个人开发，但它仍然非常易于接近。社区一直保持着 Vue 从一开始就具有的要点：一个易于使用的框架，让你自由编码。

Vue CLI 就是这一成功的又一个例子。除了一个与其他现代前端框架相匹敌的命令行界面外，Vue CLI 3 还在前端 JavaScript 框架中树立了新的标准，并配备了图形用户界面（GUI）。这个界面使得设置、扩展、运行和服务一个 Vue 项目变得轻而易举。

当你将这个 GUI 的添加与成功地试图通过提供一个经过深思熟虑的设置过程来减轻工具链疲劳的尝试相结合时，你会得到一个非常强大的组合，开发人员也会因此而感到高兴。

# 这本书是为谁准备的

这本书是为网页开发人员和 JavaScript 开发人员准备的，他们想要更多地了解 Vue CLI 3。读者必须具备 HTML/CSS 和 JavaScript 的基本知识。基本上，读者还应该熟悉基本的操作系统工作流程，比如使用类 UNIX 命令行界面，包括 Git Bash、Windows PowerShell 或任何相关的命令行工具。

这本书深入探讨了 Vue CLI 3 的技术构建模块。这不是一本关于在 Vue 中编写应用程序的书。这更像是一本基础性的书，将帮助您了解 Vue CLI 内部工作原理。如果您从未完全确定 NPM 的工作原理以及如何正确使用它，这本书将通过 Vue CLI 3 的视角来解释。同样，我们将研究 webpack，HMR，使用单文件`.vue`组件，SCSS，ECMAScript，使用 Jest 进行单元测试以及使用 Cypress 进行端到端测试。

# 本书涵盖的内容包括：

第一章《介绍 Vue CLI 3》解释了如何使用 Vue CLI 3 以及为什么应该使用它。它涵盖了最佳实践以及使用 Vue CLI 3 会得到什么。我们将设置 Node 版本管理器和 NPM，安装 Vue CLI 3，并展示如何通过命令行或 GUI 启动新应用程序。

第二章《Vue CLI 3 中的 Webpack》带领读者回顾了过去几年 JavaScript 的发展概况，这导致了 webpack 的出现。它解释了一些背景概念：NPM 和 NPM 脚本，CommonJS，JS 和 Node.js 中的模块，以及模块捆绑器以及它们在浏览器中的使用。此外，我们介绍了 webpack 的工作原理以及如何运行它。最后，我们逐步解释了如何通过 NPM 添加 Vue 项目并使用 webpack。基本上，我们正在手动设置 Vue 工具链，以便我们可以欣赏 Vue CLI 3 自动为我们做了什么。

第三章《Vue CLI 3 中的 Babel》探讨了如何使用 Babel 以及使用它的好处。我们检查了 Vue 核心 Babel 插件的构建模块，包括`Babel 7`，`babel-loader`和`@vue/babel-preset-app`。我们还研究了使用 ES5 和 ES6 运行 webpack 的区别，并更新了我们的 webpack 配置，以便它能理解 Babel。

第四章《Vue CLI 3 中的测试》介绍了使用 Vue 插件，重点介绍了用于测试的插件。我们向 Vue 应用程序添加了 Jest 插件，使用 Jest 运行单元测试，并在 Vue CLI 3 GUI 中展示了一些额外的技术和工作流程，包括从项目任务页面运行任务以及在 GUI 中运行单元测试。我们讨论了**测试驱动开发**（TDD）以及使用断言，并在章节末尾概述了 Cypress。

第五章《Vue CLI 3 和路由》讨论了使用 vue-router 和 vuex 添加 Vue 项目，配置预设选项以及理解 vue-router 主题。这些包括命名路由、动态路由、使用 Vue 实例中的方法导航到路由、使用子路由以及延迟加载路由。

第六章《在 Vue CLI 3 中使用 ESlint 和 Prettier》向我们展示了 ESlint 是什么以及它的用处。我们还看了 Prettier，一个方便的代码格式化程序，可以在每次保存时格式化您的代码。我们讨论了通用的代码检查器以及它们的用途。

第七章《使用 SCSS 改进 CSS》描述了 SCSS 的基础知识，展示了它与 CSS 的不同之处以及可用的附加功能。我们使用了在第五章《Vue CLI 3 和路由》中构建的简单应用程序，并看到如何通过向应用程序添加 boostrap-vue 插件来改进其样式。在 VDOM 库中使用 SCSS 有时会令人困惑，在本章中，我们看到了一种实际的工作流选项。

第八章《在 GitHub Pages 上部署 Vue CLI 3 应用程序》解释了 Git 是什么以及如何设置它。我们讨论了一些基础知识，包括使用 Git 跟踪更改和提交应用程序中的更改。我们继续讨论了三棵树概念、分支和合并分支。我们注册了 GitHub 帐户，使用 GitHub Desktop 添加了 origin/master，并了解了如何发布本地存储库。最后，我们讨论了如何使用*subtree*功能在 GitHub 页面上部署 Vue 应用程序。

# 要充分利用本书

要充分利用本书，您应该对使用 Windows、HTML、CSS、JavaScript 的基础知识以及使用 Git Bash 等命令行工具有一定的了解。熟悉 Node、NPM 和一些基本的命令行实用程序将是有益的，但这并非强制要求。

# 下载示例代码文件

您可以从[www.packt.com](http://www.packt.com)的帐户中下载本书的示例代码文件。如果您在其他地方购买了本书，您可以访问[www.packt.com/support](http://www.packt.com/support)并注册，以便直接通过电子邮件接收文件。

您可以按照以下步骤下载代码文件：

1.  登录或注册[www.packt.com](http://www.packt.com)。

1.  选择“支持”选项卡。

1.  单击“代码下载和勘误”。

1.  在搜索框中输入书名，然后按照屏幕上的说明操作。

文件下载后，请确保使用最新版本解压或提取文件夹：

+   WinRAR/7-Zip for Windows

+   Zipeg/iZip/UnRarX for Mac

+   7-Zip/PeaZip for Linux

该书的代码包也托管在 GitHub 上，网址为[`github.com/PacktPublishing/Vue-CLI-3-Quick-Start-Guide`](https://github.com/PacktPublishing/Vue-CLI-3-Quick-Start-Guide)。如果代码有更新，将在现有的 GitHub 存储库上进行更新。

# 下载彩色图像

我们还提供了一个 PDF 文件，其中包含本书中使用的屏幕截图/图表的彩色图像。您可以在这里下载它：[`www.packtpub.com/sites/default/files/downloads/9781789950342_ColorImages.pdf`](http://www.packtpub.com/sites/default/files/downloads/9781789950342_ColorImages.pdf)。

# 使用的约定

本书中使用了许多文本约定。

`CodeInText`：表示文本中的代码词，数据库表名，文件夹名，文件名，文件扩展名，路径名，虚拟 URL，用户输入和 Twitter 句柄。这是一个例子：“将下载的`WebStorm-10*.dmg`磁盘映像文件挂载为系统中的另一个磁盘。”

代码块设置如下：

```js
{
  "name": "vue-from-npm",
  "version": "1.0.0",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
```

当我们希望引起您对代码块的特定部分的注意时，相关行或项目将以粗体显示：

```js
let CustomArticle = Vue.component('custom-article', {
    template: `
      <article>
        Our own custom article component!
      </article>`
  })
```

任何命令行输入或输出都以以下方式编写：

```js
mkdir new-project-with-webpack && cd new-project-with-webpack
```

**粗体**：表示一个新术语，一个重要的词，或者你在屏幕上看到的词。例如，菜单或对话框中的单词会以这种方式出现在文本中。这是一个例子：“从管理面板中选择系统信息。”

警告或重要说明会以这种方式出现。提示和技巧会以这种方式出现。
