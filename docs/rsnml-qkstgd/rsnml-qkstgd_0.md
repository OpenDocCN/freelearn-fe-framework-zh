# 前言

ReactJS 已经改变了我们所知的 frontend 开发世界。它的创造者 Jordan Walke 还创建了 ReasonML 和 ReasonReact 作为 React 的未来。React 对 DOM 的抽象允许使用强大的编程范式，有助于解决 JavaScript 的可维护性问题，本书将深入探讨 Reason 如何帮助你构建更简单、更可维护的 React 应用程序。本书是使用 ReasonML 构建 React 应用程序的实战指南。

# 本书面向的对象

本书的目标读者是熟悉 ReactJS 的 JavaScript 开发者。不需要有静态类型语言的前期经验。

# 本书涵盖的内容

第一章，*ReasonML 简介*，讨论了当前 Web 开发的状态以及为什么我们会考虑 ReasonML 用于前端开发（以及更多）。

第二章，*设置开发环境*，帮助我们开始运行。

第三章，*创建 ReasonReact 组件*，演示了如何使用 ReasonML 和 ReasonReact 创建 React 组件。在这里，我们开始构建一个应用外壳，并在本书的其余部分中添加到它。

第四章，*BuckleScript、Belt 和互操作性*，让我们对 Reason 的生态系统和标准库有一个全面的理解。

第五章，*有效的机器学习*，通过商业案例深入探讨了 Reason 的类型系统的更多高级特性。

第六章，*Reason 中的 CSS-in-JS*，展示了 CSS-in-JS 在 Reason 中的工作方式以及类型系统如何提供帮助。

第七章，*Reason 中的 JSON*，演示了如何将 JSON 转换为 Reason 中的数据结构，并说明了 GraphQL 如何提供帮助。

第八章，*使用 Jest 进行单元测试*，深入探讨了使用流行的 Jest 测试库进行测试。

# 为了充分利用本书

你应该熟悉以下内容：

+   命令行界面

+   GitHub 和 Git

+   一个文本编辑器，例如 Visual Studio Code

# 下载示例代码文件

你可以从[www.packt.com](http://www.packt.com)的账户下载本书的示例代码文件。如果你在其他地方购买了本书，你可以访问[www.packt.com/support](http://www.packt.com/support)并注册，以便将文件直接通过电子邮件发送给你。

你可以通过以下步骤下载代码文件：

1.  在[www.packt.com](http://www.packt.com)登录或注册。

1.  选择 SUPPORT 标签页。

1.  点击代码下载与勘误。

1.  在搜索框中输入书名，并遵循屏幕上的说明。

下载文件后，请确保使用最新版本的以下软件解压缩或提取文件夹：

+   Windows 下的 WinRAR/7-Zip

+   Mac 下的 Zipeg/iZip/UnRarX

+   Linux 下的 7-Zip/PeaZip

书籍的代码包也托管在 GitHub 上，地址为 **[`github.com/PacktPublishing/ReasonML-Quick-Start-Guide`](https://github.com/PacktPublishing/ReasonML-Quick-Start-Guide)**。如果代码有更新，它将在现有的 GitHub 仓库中更新。

我们还有其他来自我们丰富图书和视频目录的代码包，可在 [`github.com/PacktPublishing/`](https://github.com/PacktPublishing/) 上找到。查看它们吧！

# 使用的约定

在这本书中使用了多种文本约定。

`CodeInText`: 表示文本中的代码单词、文件夹名称、文件名、文件扩展名、路径名和变量名。以下是一个示例："运行 `npm run build` 编译 `Demo.re` 到 JavaScript。"

代码块设置如下：

```js
"warnings": {
  "error": "A"
},
```

当我们希望将您的注意力引向代码块中的特定部分时，相关的行或项目将以粗体显示：

```js
/* bsconfig.json */
...
"sources": {
  "dir": "src",
  "subdirs": true
},
...
```

任何命令行输入或输出都按以下方式编写：

```js
bsb -init my-reason-react-app -theme react
cd my-reason-react-app
```

**粗体**: 表示新术语、重要单词或屏幕上看到的单词。例如，菜单或对话框中的单词在文本中显示如下。以下是一个示例："padLeft 的类型是 (string, some_variant) => string，其中 some_variant 使用一个高级类型系统特性，称为 **多态变体**，它使用 [@bs.unwrap] 转换为 JavaScript 可以理解的内容。"

警告或重要提示看起来像这样。

技巧和窍门看起来像这样。

# 联系我们

我们始终欢迎读者的反馈。

**一般反馈**: 如果您对这本书的任何方面有疑问，请在邮件主题中提及书名，并给我们发送邮件至 `customercare@packtpub.com`。

**勘误**: 尽管我们已经尽最大努力确保内容的准确性，但错误仍然可能发生。如果您在这本书中发现了错误，如果您能向我们报告，我们将不胜感激。请访问 [www.packt.com/submit-errata](http://www.packt.com/submit-errata)，选择您的书籍，点击勘误提交表单链接，并输入详细信息。

**盗版**: 如果您在互联网上以任何形式遇到我们作品的非法副本，如果您能向我们提供位置地址或网站名称，我们将不胜感激。请通过链接至材料的方式与我们联系至 `copyright@packt.com`。

**如果您有兴趣成为作者**: 如果您在某个主题上具有专业知识，并且您有兴趣撰写或为书籍做出贡献，请访问 [authors.packtpub.com](http://authors.packtpub.com/)。

# 评论

请留下评论。一旦您阅读并使用了这本书，为何不在购买它的网站上留下评论呢？潜在读者可以查看并使用您的客观意见来做出购买决定，Packt 可以了解您对我们产品的看法，我们的作者也可以看到他们对书籍的反馈。谢谢！

有关 Packt 的更多信息，请访问 [packt.com](http://www.packt.com/)。
