# 前言

在过去几年中，越来越多的 Web 开发者依赖 JavaScript 来构建他们的前端和后端。本书涵盖了 Apollo、Express.js、Node.js 和 React 的一些主要技术。我们将介绍如何设置 React 和 Apollo，以便在用 Node.js 和 Sequelize 构建的后端上运行 GraphQL 请求。在此基础上，我们还将介绍对所编写的组件或函数进行测试，并使用 CircleCI 在 AWS ECS 上自动化部署。到本书结束时，您将了解如何结合最新的前端和后端技术。

# 这本书面向的对象

这本书是为熟悉 React 和 GraphQL 的 Web 开发者编写的，他们希望提高自己的技能，并使用 React、Apollo、Node.js 和 SQL 等行业标准构建全栈应用程序，同时学习使用 GraphQL 解决复杂问题。

# 这本书涵盖的内容

*第一章*, *准备开发环境*，通过介绍一些核心概念、完整流程以及准备一个可工作的 React 设置来解释应用程序的架构。我们将看到 React、Apollo Client 和 Express.js 是如何协同工作的，并介绍在 React 中工作时的一些良好实践。此外，我们还将向您展示如何使用 React Developer Tools 调试前端。

*第二章*, *使用 Express.js 设置 GraphQL*，教您如何通过安装 Express.js 和 Apollo 通过 NPM 配置您的后端。Express.js 将用于 Web 服务器，它处理并将所有 GraphQL 请求传递给 Apollo。

*第三章*, *连接到数据库*，讨论了 GraphQL 在数据修改和查询方面提供的机会。例如，我们将使用传统的 SQL 构建一个完整的应用程序。为了简化数据库代码，我们将使用 Sequelize，它允许我们使用普通的 JavaScript 对象查询我们的 SQL 服务器，并允许我们使用 MySQL、MSSQL、PostgresSQL 或仅仅是 SQLite 文件。我们将在 Apollo 和 Sequelize 中为用户和帖子构建模型和模式。

*第四章*, *将 Apollo 集成到 React 中*，您将学习如何将 Apollo 集成到 React 中，并构建前端组件以发送 GraphQL 请求。这一章将解释 Apollo 特定的配置。

*第五章*, *可重用 React 组件和 React Hooks*，在基本概念和获取及展示数据的流程清晰的情况下，将更深入地探讨编写更复杂的 React 组件以及在这些组件间共享数据。

*第六章*, *使用 Apollo 和 React 进行身份验证*，将解释在 Web 和 GraphQL 中验证用户的常见方式。您将通过使用最佳实践来构建完整的身份验证工作流程。

*第七章*, *处理图像上传*，是您将在 Apollo 之上构建一个工作认证和授权系统的点。继续前进，为了超越具有 JSON 响应的正常请求，就像 GraphQL 那样，我们现在将通过 Apollo 上传图像并将它们保存在单独的对象存储中，例如 AWS S3。

*第八章*, *React 中的路由*, 是您将实现一些进一步功能以构建面向最终用户的完整应用程序的地方，例如个人资料页面。我们将通过安装 React Router v5 来完成这项工作。

*第九章*, *实现服务器端渲染*，涵盖了服务器端渲染。对于许多应用程序来说，这是必需的。这对于 SEO 很重要，但也可以对您的最终用户产生积极影响。本章将专注于将您当前的应用程序迁移到服务器端渲染设置。

*第十章*, *实时订阅*，探讨了我们的应用程序是如何成为 WebSocket 和 Apollo 订阅的绝佳用例。我们日常使用的许多应用程序都有一个自我更新的通知栏。本章将专注于如何使用名为订阅的实验性 GraphQL 和 Apollo 功能构建此功能。

*第十一章*, *为 React 和 Node.js 编写测试*，探讨了真实的生产就绪应用程序总是有一个自动化的测试环境。我们将使用 Mocha，一个 JavaScript 单元测试框架，以及 Enzyme，一个 React 测试工具，以确保我们应用程序的质量。本章将专注于测试 GraphQL 后端以及如何使用 Enzyme 正确测试 React 应用程序。

*第十二章*, *使用 CircleCI 和 AWS 进行持续部署*，研究了部署。部署应用程序意味着不再需要通过 FTP 手动上传文件。如今，您可以在没有完整服务器运行的情况下在云中几乎运行您的应用程序。为了便于部署我们的应用程序，我们将使用 Docker。在部署我们的应用程序之前，我们将快速介绍一个基本的持续部署设置，这将让您轻松地部署所有新代码。本章将解释如何使用 Git、Docker、AWS 和 CircleCI 来部署您的应用程序。

# 要充分利用本书

要开始阅读本书并编写功能代码，您需要满足一些要求。关于操作系统，您几乎可以在所有操作系统上运行完整代码和其他依赖项。本书的主要依赖项将在本书中逐一解释。

**本书涵盖的软件/硬件**：

+   Node.js 14+

+   React 17+

+   Sequelize 6+

+   MySQL 5 或 8

# 下载示例代码文件

您可以从 GitHub 下载本书的示例代码文件[`github.com/PacktPublishing/Full-Stack-Web-Development-with-GraphQL-and-React-Second-Edition`](https://github.com/PacktPublishing/Full-Stack-Web-Development-with-GraphQL-and-React-Second-Edition)。如果代码有更新，它将在 GitHub 仓库中更新。

我们还有其他来自我们丰富的图书和视频目录的代码包，可在[`github.com/PacktPublishing/`](https://github.com/PacktPublishing/)找到。查看它们吧！

# 下载彩色图像

我们还提供了一个包含本书中使用的截图和图表的彩色图像 PDF 文件。您可以从这里下载：[`static.packt-cdn.com/downloads/9781801077880_ColorImages.pdf`](https://static.packt-cdn.com/downloads/9781801077880_ColorImages.pdf)

[`static.packt-cdn.com/downloads/9781801077880_ColorImages.pdf`](https://static.packt-cdn.com/downloads/9781801077880_ColorImages.pdf)

# 使用的约定

本书使用了多种文本约定。

`文本中的代码`：表示文本中的代码单词、数据库表名、文件夹名、文件名、文件扩展名、路径名、虚拟 URL、用户输入和 Twitter 昵称。以下是一个示例：“Apollo Client 的最新版本附带`useQuery`钩子。”

代码块设置如下：

```js
if (loading) return 'Loading...';
if (error) return 'Error! ${error.message}';
```

当我们希望您注意代码块中的特定部分时，相关的行或项目将以粗体显示：

```js
mkdir src/client/apollo touch src/client/apollo/index.js
```

任何命令行输入或输出都应如下编写：

```js
mkdir src/client/components
```

**粗体**：表示新术语、重要单词或您在屏幕上看到的单词。例如，菜单或对话框中的单词以粗体显示。以下是一个示例：“在顶部栏中，您将找到**Prettify**按钮，它可以整理您的查询，使其更易于阅读。”

小贴士或重要提示

看起来是这样的。

# 联系我们

我们欢迎读者的反馈。

**一般反馈**：如果您对本书的任何方面有疑问，请通过 customercare@packtpub.com 给我们发邮件，并在邮件主题中提及书名。

**勘误表**：尽管我们已经尽一切努力确保内容的准确性，但错误仍然可能发生。如果您在这本书中发现了错误，我们将不胜感激，如果您能向我们报告，我们将非常感谢。请访问[www.packtpub.com/support/errata](http://www.packtpub.com/support/errata)并填写表格。

**盗版**：如果您在互联网上以任何形式发现我们作品的非法副本，如果您能提供位置地址或网站名称，我们将不胜感激。请通过版权@packt.com 与我们联系，并提供材料的链接。

**如果您有兴趣成为作者**：如果您在某个领域有专业知识，并且您有兴趣撰写或为书籍做出贡献，请访问[authors.packtpub.com](http://authors.packtpub.com)。

# 分享您的想法

一旦您阅读了《使用 GraphQL 和 React 第二版进行全栈 Web 开发》，我们非常期待听到您的想法！扫描下面的二维码直接进入此书的亚马逊评论页面并分享您的反馈。

![](img/B17337_QR_code.jpg)

https://packt.link/r/1801077886

您的评论对我们和科技社区都非常重要，并将帮助我们确保我们提供高质量的内容。
