# 前言

React 是由 Meta 构建的，旨在为其代码库提供更多结构，并使其能够更好地扩展。React 在 Facebook 上表现得如此出色，以至于他们最终将其开源。如今，React 是构建应用前端的主导技术；它允许我们构建小型、隔离且高度可重用的组件，这些组件可以组合在一起以创建复杂的用户界面。

TypeScript 是由 Microsoft 构建的，旨在帮助开发者更轻松地开发基于 JavaScript 的大型程序。它是 JavaScript 的超集，为它带来了丰富的类型系统。这个类型系统帮助开发者早期捕捉到错误，并允许创建工具来稳健地导航和重构代码。

本书将教会你如何使用这两种技术来创建大型、复杂的用户界面，这些界面易于维护。

# 这本书面向谁

如果你是一名希望使用 React 和 TypeScript 创建大型和复杂前端开发的开发者，这本书适合你。本书不假设你之前有任何 React 或 TypeScript 的知识——然而，对 JavaScript、HTML 和 CSS 的基本了解将有助于你掌握所涵盖的概念。

# 本书涵盖的内容

*第一章*，*介绍 React*，涵盖了构建 React 组件的基本原理。这包括使用 JSX 定义组件输出，使用 props 使组件可配置，以及使用状态使组件交互。

*第二章*，*介绍 TypeScript*，全面讲述了 TypeScript 及其类型系统的基本原理。这包括使用内置类型以及创建新类型。

*第三章*，*设置 React 和 TypeScript*，解释了如何创建用于 React 和 TypeScript 开发的工程。然后章节继续介绍如何创建使用 TypeScript 来使 props 和 states 类型安全的 React 组件。

*第四章*，*使用 React Hooks*，详细介绍了常见的 React Hooks 及其典型用例。章节还涵盖了如何使用 TypeScript 使 Hooks 类型安全。

*第五章*，*React 前端样式化方法*，介绍了如何使用几种不同的方法来样式化 React 组件。每种方法的优点也得到了探讨。

*第六章*，*使用 React Router 进行路由*，介绍了一个流行的库，它为多页应用提供了客户端路由。它涵盖了如何声明页面的路径以及如何在这些页面之间创建链接。它还涵盖了如何为高度动态的页面实现页面参数。

*第七章*，*使用表单*，探讨了如何使用几种不同的方法来实现表单，包括使用一个流行的库。每种方法的优点也包括在内。

*第八章*，*状态管理*，介绍了如何在不同的组件之间共享状态。探讨了多种方法及其优点。

*第九章*，*与 RESTful API 交互*，展示了 React 组件如何与 REST API 交互。章节逐步介绍了一种使用核心 React 的方法，然后是使用一个流行库的替代方法。

*第十章*，*与 GraphQL API 交互*，展示了 React 组件如何与 GraphQL API 交互。章节详细介绍了如何使用两个不同的流行库来实现这一点。

*第十一章*，*可重用组件*，引入了几个使 React 组件高度可重用但仍然类型安全的模式。

*第十二章*，*使用 Jest 和 React Testing Library 进行单元测试*，首先深入探讨了如何使用 Jest 测试函数。然后章节转向如何借助 React Testing Library 测试 React 组件。

# 为了充分利用本书

为了充分利用本书，您需要了解 JavaScript 的基础知识，包括以下内容：

+   理解一些原始的 JavaScript 类型，例如`string`、`number`、`boolean`、`null`和`undefined`

+   理解如何创建变量并引用它们，包括数组和对象

+   理解如何创建函数并调用它们

+   理解如何使用`if`和`else`关键字创建条件语句

您还需要了解 HTML 的基础知识，包括以下内容：

+   理解基本的 HTML 元素，如`div`、`ul`、`a`和`h1`

+   理解如何引用 CSS 类来设置 HTML 元素的样式

理解基本的 CSS 也有帮助，包括以下内容：

+   如何调整元素大小并包括边距和填充

+   如何定位元素

+   如何为元素着色

为了跟随本书的内容，您需要在您的计算机上安装以下技术：

+   一个现代浏览器，如**Google Chrome**，可以从 [`www.google.com/chrome/`](https://www.google.com/chrome/) 安装

+   **Node.js** 和 **npm**：您可以从 [`nodejs.org/en/download/`](https://nodejs.org/en/download/) 安装它们。

+   **Visual Studio Code**：您可以从：[`code.visualstudio.com/`](https://code.visualstudio.com/) 安装它。

| **本书涵盖的软件/硬件** |
| --- |
| React 18.0 或更高版本 |
| TypeScript 4.7 或更高版本 |

**如果您使用的是本书的数字版，我们建议您亲自输入代码或从本书的 GitHub 仓库（下一节中提供链接）获取代码。这样做将有助于避免与代码复制和粘贴相关的任何潜在错误** **。**

# 下载示例代码文件

您可以从 GitHub 下载本书的示例代码文件，网址为[`github.com/PacktPublishing/Learn-React-with-TypeScript-2nd-Edition`](https://github.com/PacktPublishing/Learn-React-with-TypeScript-2nd-Edition)。如果代码有更新，它将在 GitHub 仓库中更新。

我们还有其他来自我们丰富图书和视频目录的代码包，可在[`github.com/PacktPublishing/`](https://github.com/PacktPublishing/)找到。查看它们吧！

# 下载彩色图像

我们还提供包含本书中使用的截图和图表的彩色图像的 PDF 文件。您可以从这里下载：[`packt.link/5CvU5`](https://packt.link/5CvU5)。

# 约定使用

本书使用了多种文本约定。

`文本中的代码`：表示文本中的代码单词、数据库表名、文件夹名、文件名、文件扩展名、路径名、虚拟 URL、用户输入和 Twitter 昵称。以下是一个示例：“在这里，`null`被传递，因为没有属性。”

代码块设置如下：

```js
<div className=”title”>
  <span>Oh no!</span>
</div>
```

当我们希望您注意代码块中的特定部分时，相关的行或项目将以粗体显示：

```js
React.createElement(
  'span',
  null,
  title ? title : 'Something important'
);
```

任何命令行输入或输出都按以下方式编写：

```js
$ mkdir css
$ cd css
```

**粗体**：表示新术语、重要单词或您在屏幕上看到的单词。例如，菜单或对话框中的单词以**粗体**显示。以下是一个示例：“从**管理**面板中选择**系统信息**。”

小贴士或重要提示

看起来是这样的。

# 联系我们

我们欢迎读者的反馈。

**一般反馈**：如果您对本书的任何方面有疑问，请通过电子邮件发送至 customercare@packtpub.com，并在邮件主题中提及书名。

**勘误**：尽管我们已经尽一切努力确保内容的准确性，但错误仍然可能发生。如果您在本书中发现错误，我们将不胜感激，如果您能向我们报告此错误。请访问[www.packtpub.com/support/errata](http://www.packtpub.com/support/errata)并填写表格。

**盗版**：如果您在互联网上遇到我们作品的任何形式的非法副本，如果您能提供位置地址或网站名称，我们将不胜感激。请通过 copyright@packt.com 与我们联系，并提供材料的链接。

**如果您有兴趣成为作者**：如果您在某个主题上具有专业知识，并且您有兴趣撰写或为书籍做出贡献，请访问[authors.packtpub.com](http://authors.packtpub.com)。

# 分享您的想法

读完*Learn React with TypeScript (Second Edition)*后，我们很乐意听听您的想法！请[点击此处直接进入此书的亚马逊评论页面](https://packt.link/r/1-804-61420-3)并分享您的反馈。

您的评论对我们和科技社区都很重要，并将帮助我们确保我们提供高质量的内容。

# 下载本书的免费 PDF 副本

感谢您购买本书！

您喜欢在路上阅读，但无法携带您的印刷书籍到处走吗？ 您的电子书购买是否与您选择的设备不兼容？

不要担心，现在，每购买一本 Packt 书籍，您都可以免费获得该书的 DRM 免费 PDF 版本。

在任何地方、任何设备上阅读。直接从您最喜欢的技术书籍中搜索、复制和粘贴代码到您的应用程序中。

优惠远不止于此，您将获得独家折扣、时事通讯和每日免费内容的每日邮箱访问权限

按照以下简单步骤获取优惠：

1.  扫描二维码或访问以下链接

![二维码](img/B19051_QR_Free_PDF.jpg)

https://packt.link/free-ebook/9781804614204

1.  提交您的购买证明

1.  就这些！我们将直接将您的免费 PDF 和其他优惠发送到您的邮箱

# 第一部分：简介

本部分将帮助您开始学习 React 和 TypeScript，分别了解这两种技术的 fundamentals。然后我们将开始一起使用这些技术，以便我们能够创建强大的类型安全组件。我们还将详细了解 React 的常用 hooks 以及它们在应用程序中的使用情况。

本部分包括以下章节：

+   *第一章*，*介绍 React*

+   *第二章*，*介绍 TypeScript*

+   *第三章*，*设置 React 和 TypeScript*

+   *第四章*，*使用 React Hooks*
