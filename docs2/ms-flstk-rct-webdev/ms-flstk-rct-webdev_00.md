# 前言

近年来 JavaScript 编程语言的创新至关重要。例如，自 2009 年以来，Node 的兴起赋予了开发者使用相同的编程语言在浏览器和后端的能力。环境变化在 2017 年也没有放缓。这本书将向您介绍一些新的热门概念，这些概念可以进一步加快全栈开发过程。

在 2016 年和 2017 年，对使用 Falcor 或 GraphQL 等全栈技术使应用程序更快的需求更大。这本书不仅仅是一本关于如何在 Node 中暴露 API 端点并开始使用客户端应用程序消费它们的指南。您将学习如何使用 Netflix 的最新技术，称为 Falcor。除此之外，您还将学习如何使用 React 和 Redux 设置项目。

在这本书中，您将找到从零开始使用 Node.js、Express.js、MongoDB、Mongoose、Falcor 和 Redux 库构建全栈应用的巨大教程。您还将学习如何使用 Docker 和亚马逊的 AWS 服务部署您的应用程序。

# 本书涵盖的内容

第一章, *使用 Node.js、Express.js、MongoDB、Mongoose、Falcor 和 Redux 配置全栈*，从零开始引导您设置应用程序。它帮助您了解 npm 中的不同库如何构建一个可用的全栈 React 入门套件。

第二章, *我们的发布应用的全栈登录和注册*，指导您如何设置 JWT 令牌以实现基本的全栈认证机制。

第三章, *服务器端渲染*，教您如何将服务器端渲染添加到应用程序中，这对于加快应用程序执行和搜索引擎优化很有帮助。

第四章, *客户端的高级 Redux 和 Falcor*，展示了如何向您的应用程序添加更多高级功能，例如集成 WYSIWYG 编辑器和扩展应用程序的 Material-UI 组件，从应用程序用户的视角来看。

第五章, *Falcor 高级概念*，带您深入了解 Falcor 及其后端 Falcor-Router 的相关开发指南。

第六章, *AWS S3 用于图片上传和总结关键应用功能*，指导您完成发布应用文章封面照片的上传过程。

第七章, *在 mLab 上部署 MongoDB*，教您如何为您的应用程序准备远程数据库。

第八章, *Docker 和 EC2 容器服务*，教您如何设置 AWS/Docker。

第九章，*使用单元和行为测试进行持续集成*，展示了你需要准备发布应用程序的 CI 和测试所需的内容。

# 你需要这本书什么

本书是在使用 macOS El Capitan 和 Sierra 的情况下编写的。它在 Linux Ubuntu 和 Windows 10 机器上进行了测试（有关这三个操作系统的差异，已添加了一些额外的注释）。

工具集安装的其余部分在第一章中展示，*使用 Node.js、Express.js、MongoDB、Mongoose、Falcor 和 Redux 配置全栈*。

# 这本书是为谁而写的

你是否想从头开始构建和了解全栈开发？那么这本书就是为你而写的。

如果你是一名寻找提高全栈开发技能集的 React 开发者，那么你也会感到宾至如归。你将使用最新的技术从头开始构建你的下一个全栈发布应用程序。在端到端的指导下创建你的第一个全栈应用程序。

我们在书中假设你已经具备了 React 库的基本知识。

# 惯例

在这本书中，你会找到许多不同的文本样式，用于区分不同类型的信息。以下是一些这些样式的示例及其含义的解释。

文本中的代码单词、数据库表名、文件夹名、文件名、文件扩展名、路径名、虚拟 URL、用户输入和 Twitter 昵称如下所示：“在项目的目录中，创建一个名为`initData.js`的文件。”

代码块设置如下：

```js
[
    {
        articleId: '987654',
        articleTitle: 'Lorem ipsum - article one',
        articleContent: 'Here goes the content of the article'
    },
    {
        articleId: '123456',
        articleTitle: 'Lorem ipsum - article two',
        articleContent: 'Sky is the limit, the content goes here.'
    }
]

```

任何命令行输入或输出都如下所示：

```js
mkdir server
cd server
touch index.js

```

**新术语**和**重要词汇**以粗体显示。你在屏幕上看到的单词，例如在菜单或对话框中，在文本中如下所示：“通过单击创建链接以使用默认值创建连接。”

警告或重要提示会以这样的框出现。

小贴士和技巧看起来像这样。

# 读者反馈

我们读者的反馈总是受欢迎的。告诉我们你对这本书的看法——你喜欢什么或不喜欢什么。读者反馈对我们很重要，因为它帮助我们开发出你真正能从中获得最大价值的标题。

要向我们发送一般反馈，只需发送电子邮件至`feedback@packtpub.com`，并在邮件主题中提及本书的标题。

如果你在一个领域有专业知识，并且你对撰写或为本书做出贡献感兴趣，请参阅我们的作者指南[www.packtpub.com/authors](http://www.packtpub.com/authors)。

# 客户支持

现在你已经是 Packt 图书的骄傲所有者，我们有一些事情可以帮助你从你的购买中获得最大价值。

# 下载示例代码

您可以从[`www.packtpub.com`](http://www.packtpub.com)的账户下载本书的示例代码文件。如果您在其他地方购买了本书，您可以访问[`www.packtpub.com/support`](http://www.packtpub.com/support)并注册，以便将文件直接通过电子邮件发送给您。

您可以通过以下步骤下载代码文件：

1.  使用您的电子邮件地址和密码登录或注册我们的网站。

1.  将鼠标指针悬停在顶部的“支持”标签上。

1.  点击“代码下载与错误清单”。

1.  在搜索框中输入本书的名称。

1.  选择您想要下载代码文件的书籍。

1.  从下拉菜单中选择您购买本书的来源。

1.  点击“代码下载”。

文件下载完成后，请确保使用最新版本的以下软件解压或提取文件夹：

+   WinRAR / 7-Zip for Windows

+   Zipeg / iZip / UnRarX for Mac

+   7-Zip / PeaZip for Linux

本书代码包也托管在 GitHub 上，地址为[`github.com/PacktPublishing/Mastering-Full-Stack-React-Web-Development`](https://github.com/PacktPublishing/Mastering-Full-Stack-React-Web-Development)。我们还有其他来自我们丰富图书和视频目录的代码包可供在[`github.com/PacktPublishing/`](https://github.com/PacktPublishing/)上获取。查看它们吧！

# 下载本书的颜色图像

我们还为您提供了一个包含本书中使用的截图/图表的颜色图像的 PDF 文件。这些颜色图像将帮助您更好地理解输出的变化。您可以从[`www.packtpub.com/sites/default/files/downloads/MasteringFullStackReactWebDevelopment_ColorImages.pdf`](https://www.packtpub.com/sites/default/files/downloads/Bookname_ColorImages.pdf)下载此文件。

# 错误清单

尽管我们已经尽一切努力确保我们内容的准确性，但错误仍然可能发生。如果您在我们的书中发现错误——可能是文本或代码中的错误——如果您能向我们报告，我们将不胜感激。通过这样做，您可以节省其他读者的挫败感，并帮助我们改进本书的后续版本。如果您发现任何错误清单，请通过访问[`www.packtpub.com/submit-errata`](http://www.packtpub.com/submit-errata)，选择您的书籍，点击“错误提交表单”链接，并输入您的错误详细信息来报告它们。一旦您的错误清单得到验证，您的提交将被接受，错误清单将被上传到我们的网站或添加到该标题的“错误清单”部分的现有错误清单中。

要查看之前提交的错误清单，请访问[`www.packtpub.com/books/content/support`](https://www.packtpub.com/books/content/support)，并在搜索框中输入本书的名称。所需信息将出现在“错误清单”部分。

# 盗版

在互联网上对版权材料的盗版是一个横跨所有媒体的持续问题。在 Packt，我们非常重视我们版权和许可证的保护。如果您在互联网上发现任何形式的我们作品的非法副本，请立即提供位置地址或网站名称，以便我们可以寻求补救措施。

请通过`copyright@packtpub.com`与我们联系，并提供疑似盗版材料的链接。

我们感谢您在保护我们作者和我们提供有价值内容的能力方面所提供的帮助。

# 问题

如果您对本书的任何方面有问题，您可以通过`questions@packtpub.com`与我们联系，我们将尽力解决问题。
