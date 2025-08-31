# 前言

ReactJS 是一个开源的 JavaScript 库，旨在将响应式编程的方面引入 Web 应用程序和网站。它旨在解决开发单页应用程序时遇到的挑战。React 的核心原则是声明式代码、效率、灵活性和改进的开发者体验。

在工作过程中深入研究新技术，还有什么更好的学习方法呢？这本书将通过不同的项目引导你，每个项目都专注于你在掌握 React 过程中的特定功能。我们将涵盖从 JSX、插件、性能到 Redux 的所有内容。

让我们的旅程开始吧！

# 有什么比在工作过程中深入研究新技术更好的学习方法呢？

第一章, *React 入门*, 通过构建一个使用静态数据的简单应用程序来介绍 ReactJS 的基础知识。我们将研究 React 的顶级 API 及其基本构建块。

第二章, *深入 JSX*, 深入探讨 JSX 及其与 React 的用法。我们还将探讨在处理 JSX 时需要考虑的一些陷阱。

第三章, *数据流和生命周期事件*, 专注于 React 组件之间的数据流以及组件的完整生命周期。

第四章, *复合动态组件和表单*, 展示了如何使用 React 构建复合动态组件，在构建表单向导应用程序时更加关注表单。

第五章, *混入和 DOM*, 涵盖了混入、refs 以及 React 如何与 DOM 交互。

第六章, *服务器端的 React*, 使用 React 在服务器端渲染 HTML，并通过基于 Open Library Books API 的搜索应用程序来了解服务器端渲染带来的好处。

第七章, *React 插件*, 继续使用搜索应用程序，并通过 React 提供的各种插件来增强它。我们将研究这些插件的用例。

第八章, *React 应用的性能*, 通过深入研究 React 如何渲染内容，讨论了 React 应用的性能的各个方面，并帮助我们使应用程序更快。

第九章, *React Router 和数据模型*, 帮助构建 Pinterest 风格的程序，并讨论使用 react-router 的路由。我们还将讨论如何使用 React，包括 Backbone 模型，与各种数据模型一起使用。

第十章, *动画*, 专注于通过动画使我们的 Pinterest 应用程序更加互动，以及如何有效地使用 React 来使用它们。

第十一章，*React 工具*，回顾并讨论了我们在与 React 一起工作时将使用的各种工具。我们将研究 Babel、ESLint、React 开发者工具和 Webpack 等工具。

第十二章，*Flux*，解释了在使用 Flux 架构的同时如何构建社交媒体追踪应用。我们将讨论 Flux 架构的需求以及它带来的好处。

第十三章，*Redux 和 React*，介绍了如何使用 Redux——一个流行的状态管理库——来进一步增强社交媒体追踪应用，以便使用基于 Redux 的状态管理。

# 您需要为本书准备什么

您需要拥有一个现代的网页浏览器，例如 Chrome 或 Firefox，才能运行本书中的示例。您还需要安装 Node.js——[`nodejs.org/en/`](https://nodejs.org/en/)——并设置 npm 包管理器。额外的设置说明可以在[`github.com/bigbinary/reactjs-by-example`](https://github.com/bigbinary/reactjs-by-example)找到。

# 本书面向谁

如果您是一位希望从头开始学习 ReactJS 的网页开发者，那么这本书是为您量身定制的。本书预期您对 JavaScript、HTML 和 CSS 有良好的理解。

# 惯例

在本书中，您将找到许多文本样式，用于区分不同类型的信息。以下是一些这些样式的示例及其含义的解释。

文本中的代码单词、数据库表名、文件夹名、文件名、文件扩展名、路径名、虚拟 URL、用户输入和 Twitter 昵称如下所示："注意，我们是如何使用`this.props.headings`来访问传递的头信息。"

代码块设置如下：

```js
return <div>
         <h1>Recent Changes</h1>
           <table>
          ….
          </table>
         </div>
…
```

当我们希望您注意代码块中的特定部分时，相关的行或项目将以粗体显示：

```js
    TestUtils.Simulate.click(submitButton);
    expect(app.state.searching).toEqual(true);
    expect(app.state.searchCompleted).toEqual(false);
 let spinner = TestUtils.findRenderedComponentWithType(app, Spinner);
 expect(spinner).toBeTruthy();

```

任何命令行输入或输出都如下所示：

```js
Listening at localhost:9000
Hash: 8ec0d12965567260413b
Version: webpack 1.9.11
Time: 1639ms

```

**新术语**和**重要词汇**将以粗体显示。屏幕上显示的单词，例如在菜单或对话框中，在文本中如下所示："你注意到**自动运行 JS**选项了吗？"

### 注意

警告或重要注意事项如下所示：

### 小贴士

小技巧和技巧看起来像这样。

# 读者反馈

我们始终欢迎读者的反馈。告诉我们您对这本书的看法——您喜欢或不喜欢什么。读者反馈对我们来说很重要，因为它帮助我们开发出您真正能从中受益的标题。

要向我们发送一般反馈，只需发送电子邮件至`<feedback@packtpub.com>`，并在邮件主题中提及本书的标题。

如果您在某个领域有专业知识，并且有兴趣撰写或为本书做出贡献，请参阅我们的作者指南[www.packtpub.com/authors](http://www.packtpub.com/authors)。

# 客户支持

现在您已经是 Packt 书籍的骄傲拥有者，我们有多种方式可以帮助您从您的购买中获得最大收益。

## 下载示例代码

您可以从[`www.packtpub.com`](http://www.packtpub.com)的账户下载本书的示例代码文件。如果您在其他地方购买了这本书，您可以访问[`www.packtpub.com/support`](http://www.packtpub.com/support)并注册，以便将文件直接通过电子邮件发送给您。

您可以通过以下步骤下载代码文件：

1.  使用您的电子邮件地址和密码登录或注册我们的网站。

1.  将鼠标指针悬停在顶部的**支持**标签上。

1.  点击**代码下载与错误清单**。

1.  在**搜索**框中输入书籍名称。

1.  选择您想要下载代码文件的书籍。

1.  从下拉菜单中选择您购买此书的来源。

1.  点击**代码下载**。

文件下载完成后，请确保使用最新版本的以下软件解压缩或提取文件夹：

+   WinRAR / 7-Zip for Windows

+   Zipeg / iZip / UnRarX for Mac

+   7-Zip / PeaZip for Linux

## 错误清单

尽管我们已经尽最大努力确保内容的准确性，但错误仍然可能发生。如果您在我们的书中发现错误——可能是文本或代码中的错误——如果您能向我们报告这一点，我们将不胜感激。通过这样做，您可以避免其他读者的挫败感，并帮助我们改进本书的后续版本。如果您发现任何错误清单，请通过访问[`www.packtpub.com/submit-errata`](http://www.packtpub.com/submit-errata)，选择您的书籍，点击**错误提交表单**链接，并输入您的错误详情。一旦您的错误清单得到验证，您的提交将被接受，错误清单将被上传到我们的网站或添加到该标题的错误清单部分。

要查看之前提交的错误清单，请访问[`www.packtpub.com/books/content/support`](https://www.packtpub.com/books/content/support)，并在搜索字段中输入书籍名称。所需信息将出现在**错误清单**部分。

## 盗版

盗版互联网上的版权材料是一个跨所有媒体的持续问题。在 Packt，我们非常重视我们版权和许可证的保护。如果您在互联网上发现我们作品的任何非法副本，请立即提供位置地址或网站名称，以便我们可以寻求补救措施。

请通过`<copyright@packtpub.com>`与我们联系，并提供疑似盗版材料的链接。

我们感谢您在保护我们作者和我们为您提供有价值内容的能力方面的帮助。

## 问题

如果您在这本书的任何方面遇到问题，您可以通过`<questions@packtpub.com>`联系我们，我们将尽力解决问题。
