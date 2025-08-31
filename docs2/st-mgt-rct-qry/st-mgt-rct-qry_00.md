# 前言

状态管理是 React 生态系统中最热门的话题之一。在 React 中处理状态有许多库和工具，每个都带来了不同的配方和观点。有一点是明确的——处理客户端状态的状态管理解决方案并不擅长处理服务器状态。React Query 就是为了解决这个问题而创建的，以帮助管理你的服务器状态！

在*使用 React Query 进行状态管理*中，你将获得一本指南，它将带你从零开始，到本书结束时成为 React Query 的专家。

你将从学习 React 世界中状态的历史背景以及是什么导致了从全局状态到客户端和服务器状态的转变开始。有了这些知识，你将理解 React Query 的需求。随着你通过章节的深入，你将学习 React Query 如何帮助你处理常见的服务器状态挑战，例如获取数据、缓存数据、更新数据以及将数据与服务器同步。

但这还不是全部——一旦你掌握了 React Query，你将学习如何将这一知识应用到服务器端渲染中。

最后，你将通过利用 Testing Library 和 Mock Service Worker 来学习一些测试代码的模式。

在本书结束时，你将获得对状态的新视角，并能够利用 React Query 解决应用程序中服务器状态的所有挑战。

# 本书面向对象

*本书面向希望提高状态管理技能并开始处理服务器状态挑战，同时提升开发和* *用户体验*的 JavaScript 和 React 开发者。*

*对 Web 开发、JavaScript 和 React 的基本了解将有助于理解本书中涵盖的一些关键概念。*

# 本书涵盖内容

*第一章*，*什么是状态以及我们如何管理它？*，涵盖了状态的基本定义，并给出了我们如何管理状态的历史概述。

*第二章*，*服务器状态与客户端状态对比*，将状态概念分开，帮助我们理解为什么独立于客户端状态管理服务器状态如此重要。

*第三章*，*React Query – 介绍、安装和配置*，介绍了 React Query 并提供将其添加到应用程序的方法。

*第四章*，*使用 React Query 获取数据*，涵盖了如何利用`useQuery`自定义钩子获取你的服务器状态。

*第五章*，*更多数据获取挑战*，扩展了前一章中介绍的概念，并涵盖了如何利用`useQuery`处理其他数据获取挑战。

*第六章*，*使用 React Query 执行数据突变*，涵盖了如何利用`useMutation`自定义钩子对服务器状态进行更改。

*第七章*，*使用 Next.js 或 Remix 进行服务器端渲染*，介绍了如何利用 React Query 与 Next.js 或 Remix 等服务器端框架。

*第八章*，*测试 React Query 钩子和组件*，为你提供了可以将应用到你的应用程序中以测试你的组件和利用 React Query 的自定义钩子的实践和食谱。

*第九章*，*React Query v5 的变化是什么？*，是一个附加章节，介绍了 TanStack Query 的 v5 版本对 React Query 的引入以及你需要更新应用程序的内容。

# 为了充分利用这本书

建议具备基本的 RESTful API 和 HTTP 方法知识。如果你想利用使用它的示例，则需要具备基本的 GraphQL 知识。

你需要了解一些关于 HTML 的基本概念。你还需要理解 JavaScript 以及其一些概念，特别是承诺。

最后，鉴于我们正在使用 React Hooks，了解它们的工作原理以及如何在你的 React 应用程序中使用它们非常重要。

| **本书涵盖的软件/硬件** | **操作系统要求** |
| --- | --- |
| Yarn | Windows, macOS, 或 Linux |
| pnpm | Windows, macOS, 或 Linux |
| npm | Windows, macOS, 或 Linux |
| JavaScript | Windows, macOS, 或 Linux |
| React 16.8 | Windows, macOS, 或 Linux |
| Remix | Windows, macOS, 或 Linux |
| Next.js | Windows, macOS, 或 Linux |
| React Testing Library | Windows, macOS, 或 Linux |
| Mock Service Worker | Windows, macOS, 或 Linux |
| TanStack Query | Windows, macOS, 或 Linux |

**如果你使用的是本书的数字版，我们建议你亲自输入代码或从本书的 GitHub 仓库（下一节中有一个链接）访问代码。这样做将帮助你避免与代码的复制和粘贴相关的任何潜在错误。**

本书为你提供了实践和工具，以全面理解和掌握 TanStack Query React 适配器——React Query。到本书结束时，你将具备充分利用它的必要理解，并准备好决定是否将其添加到你的项目中。

# 下载示例代码文件

你可以从 GitHub 下载这本书的示例代码文件：https://github.com/PacktPublishing/State-management-with-React-Query。如果代码有更新，它将在 GitHub 仓库中更新。

我们还有其他来自我们丰富的图书和视频目录的代码包，可在 https://github.com/PacktPublishing/ 上找到。查看它们吧！

# 下载彩色图像

我们还提供了一份包含本书中使用的截图和图表的彩色图像 PDF 文件。你可以从这里下载：`packt.link/Wt1n6`。

# 使用的约定

本书使用了多种文本约定。

`文本中的代码`：表示文本中的代码单词、数据库表名、文件夹名、文件名、文件扩展名、路径名、虚拟 URL、用户输入和 Twitter 昵称。以下是一个示例：“React 原生地为我们提供了两种在应用程序中保持状态的方法 - `useState`和`useReducer`。”

代码块设置如下：

```js
const NotState = ({aList = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]}) => {
  const value = "a constant value";
  const filteredList = aList.filter((item) => item % 2 === 0);
  return filteredList.map((item) => <div key={item}>{item}</div>);
};
```

当我们希望将您的注意力引到代码块中的特定部分时，相关的行或项目将以粗体显示：

```js
const App = () => {
  ...
  return (
    <div className="App">
      <div>Counter: {count}</div>
      <div>
        <button onClick={increment}>+1</button>
        <button onClick={decrement}>-1</button>
        <button onClick={reset}>Reset</button>
      </div>
    </div>
```

任何命令行输入或输出都按以下方式编写：

```js
npm i @tanstack/react-query
```

**粗体**：表示新术语、重要单词或屏幕上看到的单词。例如，菜单或对话框中的单词以**粗体**显示。以下是一个示例：“作为一个用户，我希望在点击**无效****查询**按钮时重新获取我的查询。”

小贴士或重要注意事项

它看起来像这样。

# 联系我们

我们始终欢迎读者的反馈。

请将邮件主题中提及书籍标题，并发送至`customercare@packtpub.com`。

**勘误表**：尽管我们已经尽一切努力确保内容的准确性，但错误仍然可能发生。如果您在此书中发现错误，我们将非常感激您向我们报告。请访问 www.packtpub.com/support/errata 并填写表格。

请通过`copyright@packt.com`发送带有材料链接的邮件。

**如果您有兴趣成为作者**：如果您在某个领域有专业知识，并且您有兴趣撰写或为书籍做出贡献，请访问 authors.packtpub.com。

# 分享您的想法

一旦您阅读了《使用 React Query 进行状态管理》，我们非常乐意听到您的想法！扫描下面的二维码直接进入此书的亚马逊评论页面并分享您的反馈。

![二维码图片](img/B18501_QR.jpg)

[`packt.link/r/1-803-23134-3`](http://authors.packtpub.com/)

您的评论对我们和科技社区非常重要，并将帮助我们确保我们提供高质量的内容。

# 下载此书的免费 PDF 副本

感谢您购买此书！

您喜欢在路上阅读，但无法随身携带您的印刷书籍吗？

您的电子书购买是否与您选择的设备不兼容？

请放心，现在，每购买一本 Packt 书籍，您都可以免费获得该书的 DRM 免费 PDF 版本。

在任何地方、任何设备上阅读。直接从您最喜欢的技术书籍中搜索、复制和粘贴代码到您的应用程序中。

优惠不会就此停止，您还可以获得独家折扣、时事通讯和每日免费内容的每日邮箱访问权限

按照以下简单步骤获取福利：

1.  扫描下面的二维码或访问以下链接

![二维码图片](img/B18501_QR_Free_PDF.jpg)

[`packt.link/free-ebook/9781803231341`](https://packt.link/free-ebook/9781803231341)

1.  提交您的购买证明

1.  就这样！我们将直接将您的免费 PDF 和其他福利发送到您的邮箱

# 第一部分：理解状态和了解 React Query

状态是使您的应用程序运行的关键。我们很多人往往没有意识到的是，存在不同类型的状态。这些不同类型的状态在管理给定状态时会导致不同的挑战。在本部分，我们将更深入地了解状态以及我们如何管理它。在这个过程中，我们将了解到服务器和客户端状态具有非常不同的挑战，需要分别处理，并使用不同的工具。为了处理服务器状态，我们将学习更多关于 TanStack Query React 适配器 React Query 的知识，并了解如何将其添加到我们的应用程序中。

本部分包括以下章节：

+   *第一章*，*什么是状态以及我们如何管理它？*

+   *第二章*，*服务器状态与客户端状态*

+   *第三章*，*React Query – 介绍、安装和配置*
