# 前言

欢迎来到精彩的网络开发世界！本书旨在教你 Angular 平台的基础知识，并为你提供有用的食谱和实用的代码示例，以便你可以创建丰富且可扩展的业务线应用程序。本书强调一种极简主义方法，通过最大化使用内置库，避免引入额外的第三方依赖项来实现预期结果。由于这种方法，你的代码将更容易维护和升级，因为 Angular 的新版本发布频率很高。你可以继续使用本书作为学习资源，因为书中包含的基本概念、技术和示例在未来一段时间内仍将保持相关性，尽管会有一些细微的修改。书中推荐的工具和服务已更新至大约 2020 年的最新版本，然而，工具和服务不断演变、变化，有时甚至完全消失。如果发生这种情况，请随时联系我寻求替代方案。

本书还将致力于在你心中培养敏捷和 DevOps 思维，以便你自信地创建可靠和灵活的解决方案。无论你认为自己是一位为小企业开发软件的自由职业者、全栈开发者、企业开发者还是网络开发者，你需要了解的设计、架构、开发、维护、交付和部署网络应用程序的知识，以及你需要应用的最佳实践和模式，变化并不大。如果你向用户群体交付应用程序，从某种意义上说，你是一位全栈开发者，因为你必须了解许多服务器技术。实际上，如果你掌握了如何使用 TypeScript 交付 Angular 应用程序，那么在本书后面的具体实现中，你将能够轻松地使用 Node.js、Express.js 和 TypeScript 编写自己的 RESTful API。

根据某些定义，全栈开发者需要了解从餐饮到国际版权法的一切，以便在今天的互联网上成功创建和运营应用程序。如果你是一位企业家，从某种意义上说，这是真的。然而，在这本书中，你的烹饪技能和法律学位并不适用。本书假设你已经知道如何使用网络开发基础知识，并且熟悉使用你选择的技术的 RESTful API，如果不是这样，别担心，只需遵循动手的逐步指导，你将能够迅速创建你的第一个具有 API 功能的 Angular 应用程序。

# 本书面向的对象

本书适合初学者和经验丰富的开发者，他们希望学习 Angular 或一般的 Web 开发。如果您是 Angular 开发者，您将接触到设计和部署 Angular 应用程序到生产环境的整个范围。您将了解易于理解和教授他人的 Angular 模式。如果您是自由职业者，您将掌握有效的工具和技术，以安全、自信和可靠的方式交付您的 Angular 应用程序。如果您是企业开发者，您将学习编写具有可扩展架构的 Angular 应用程序的模式和实践。

# 本书涵盖了以下内容

*第一章*，*Angular 及其概念简介*，向读者介绍了 Angular 和 Web 开发的世界。

*第二章*，*设置您的开发环境*，介绍了一种可脚本化的方法来设置您的环境。

*第三章*，*创建基本 Angular 应用程序*，介绍了软件开发的看板方法，使用易于使用的设计工具来传达想法，并涵盖 Angular 基础知识，利用 CLI 工具最大化您的效果。

*第四章*，*自动化测试、持续集成和发布到生产环境*，涵盖了单元测试、持续集成和快速云部署。

*第五章*，*使用 Material 提供高质量 UX*，向您介绍 Angular Material，并解释如何使用它来构建外观出色的应用程序。

*第六章*，*表单、Observables 和 Subjects*，教您如何舒适地使用 Angular 表单和 RxJS 进行响应式编程。

*第七章*，*创建以路由器为首要的业务应用*，专注于路由器优先架构，这是一种针对中大型业务应用设计和开发的七步方法。

*第八章*，*设计身份验证和授权*，深入探讨了 Angular 和 RESTful 应用中的身份验证和授权相关模式。

*第九章*，*使用 Docker 进行 DevOps*，深入探讨了使用 Docker 进行容器化，以实现跨不同生态系统的可重复的开发和运营工作流程。

*第十章*，*RESTful API 与全栈实现*，将引导您了解如何实现一个支持业务应用的现实世界 MEAN 栈应用程序。

*第十一章*，*食谱 - 可重用性、路由和缓存*，包含围绕捕获和操作用户数据的食谱，这些数据对于业务应用通常需要。

*第十二章*，*食谱 - 主/详情、数据表和 NgRx*，包含围绕展示业务应用中常见用户数据的食谱，以及使用 NgRx 在 Angular 中实现 Flux 模式的介绍。

*第十三章*，*在 AWS 上构建高可用云基础设施*，不仅涉及应用程序功能，还涉及在 AWS 上配置高可用云基础设施。

*第十四章*，*Google Analytics 和高级云操作*，探讨了拥有、运营和优化您的云基础设施，以及使用 Google Analytics 来捕捉用户行为的细微差别。

*附录 A*，*调试 Angular*，涵盖了如何使用 Chrome DevTools 处理常见的 Angular 错误和断点调试。

*附录 B*，*Angular 速查表*，是 Angular CLI 命令、主要 Angular 组件和常见 RxJS 操作符的快速参考。

*附录 C*，*保持 Angular 和工具始终如一*，包含了有关如何保持您的开发环境、Angular 及其依赖项更新的详细信息。您可以通过[`static.packt-cdn.com/downloads/9781838648800_Appendix_C_Keeping_Angular_and_Tools_Evergreen.pdf`](https://static.packt-cdn.com/downloads/9781838648800_Appendix_C_Keeping_Angular_and_Tools_Evergreen.)阅读此附录。您还可以通过[`expertlysimple.io/stay-evergreen`](https://expertlysimple.io/stay-evergreen)阅读此附录。

*附录 D*，*自我评估答案*，包含了每章末尾的测试题答案。您可以通过[`static.packt-cdn.com/downloads/9781838648800_Appendix_D_Self-Assessment_Answers.pdf`](https://static.packt-cdn.com/downloads/9781838648800_Appendix_D_Self-Assessment_Answers.pdf)阅读此附录。您还可以通过[`expertlysimple.io/angular-self-assessment/`](https://expertlysimple.io/angular-self-assessment/)阅读此附录。

# 为了充分利用这本书

+   遵循每章和每节开头的说明。

+   在 GitHub 上检查最新的代码示例。

+   熟悉全栈 Web 开发会有所帮助，但这不是先决条件。

+   如果您是初学者，请按照出版顺序阅读本书，并在每个章节的内容旁边编写您的解决方案。

+   您可以从任何章节开始阅读，只要您从 GitHub 克隆了前一章节的实现，并理解了*第二章*，*设置您的开发环境*中涵盖的假设。

## 下载示例代码文件

您可以在 GitHub 上获取本书示例代码文件的最新版本。有四个项目直接支持本书的内容：

1.  Web 开发环境设置脚本[`github.com/duluca/web-dev-environment-setup`](https://github.com/duluca/web-dev-environment-setup)

1.  本地天气应用[`github.com/duluca/local-weather-app`](https://github.com/duluca/local-weather-app)

1.  LemonMart[`github.com/duluca/lemon-mart`](https://github.com/duluca/lemon-mart)

1.  LemonMart 服务器[`github.com/duluca/lemon-mart-server`](https://github.com/duluca/lemon-mart-server)

在每个章节中，您可以找到访问特定章节代码示例的具体说明。在演示持续集成和持续部署配置时，使用 Git 分支和 GitHub 拉取请求来演示特定的配置元素。

您可以在发布时从您的[`www.packtpub.com`](http://www.packtpub.com)账户下载本书的示例代码文件快照。如果您在其他地方购买了本书，您可以访问[`www.packtpub.com/support`](http://www.packtpub.com/support)并注册，以便将文件直接通过电子邮件发送给您。

您可以通过以下步骤下载代码文件：

1.  在[`www.packtpub.com`](http://www.packtpub.com)登录或注册。

1.  选择**支持**标签。

1.  点击**代码下载与勘误**。

1.  在**搜索**框中输入书籍名称，并遵循屏幕上的说明。

文件下载完成后，请确保使用最新版本解压或提取文件夹：

+   WinRAR / 7-Zip for Windows

+   Zipeg / iZip / UnRarX for Mac

+   7-Zip / PeaZip for Linux

该书的代码包也托管在 GitHub 上，网址为[`github.com/PacktPublishing/Angular-for-Enterprise-Ready-Web-Applications-Second-Edition`](https://github.com/PacktPublishing/Angular-for-Enterprise-Ready-Web-Applications-Second-Edition)。我们还有其他来自我们丰富图书和视频目录的代码包，可在[`github.com/PacktPublishing/`](https://github.com/PacktPublishing/)找到。查看它们吧！

## 下载彩色图像

我们还提供了一份包含本书中使用的截图/图表的彩色 PDF 文件。您可以从以下链接下载：[`www.packtpub.com/sites/default/files/downloads/9781838648800_ColorImages.pdf`](http://www.packtpub.com/sites/default/files/downloads/9781838648800_ColorImages.pdf)。

## 使用的约定

本书使用了多种文本约定。

`CodeInText`：表示文本中的代码单词、数据库表名、文件夹名、文件名、文件扩展名、路径名、虚拟 URL、用户输入和 Twitter 昵称。例如；“将下载的`WebStorm-10*.dmg`磁盘映像文件挂载为系统中的另一个磁盘。”

一段代码设置如下：

```js
{
  "name": "local-weather-app",
  "version": "0.0.0",
  "license": "MIT",
  ...
} 
```

当我们希望您注意代码块中的特定部分时，相关的行或项目将以粗体显示：

```js
 "scripts": {
    "ng": "ng",
    "start": "ng serve",
    **"build": "ng build",**
 **"test": "ng test",**
    "lint": "ng lint",
    "e2e": "ng e2e"
  }, 
```

任何跨平台或 macOS 特定的命令行输入或输出如下所示：

```js
$ brew tap caskroom/cask 
```

Windows 特定的命令行输入或输出如下所示：

```js
PS> Set-ExecutionPolicy AllSigned; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1')) 
```

**粗体**：表示新术语、重要单词或您在屏幕上看到的单词，例如在菜单或对话框中，这些单词在文本中也会以这种方式出现。例如：“浏览器供应商应按照**万维网联盟**（**W3C**）定义的技术来实现这些技术。”

警告或重要提示如下所示。

技巧和窍门如下所示。

# 联系我们

我们欢迎读者的反馈。

**一般反馈**：请发送电子邮件至`feedback@packtpub.com`，并在邮件主题中提及书籍标题。如果您对本书的任何方面有疑问，请发送电子邮件至`questions@packtpub.com`。

**勘误表**：尽管我们已经尽最大努力确保内容的准确性，但错误仍然可能发生。如果您在这本书中发现了错误，我们将不胜感激，如果您能向我们报告，我们将不胜感激。请访问[`www.packtpub.com/submit-errata`](http://www.packtpub.com/submit-errata)，选择您的书籍，点击勘误提交表单链接，并输入详细信息。

**盗版**：如果您在互联网上以任何形式发现我们作品的非法副本，我们将不胜感激，如果您能向我们提供位置地址或网站名称。请通过`copyright@packtpub.com`与我们联系，并提供材料的链接。

**如果您有兴趣成为作者**：如果您在某个领域有专业知识，并且您有兴趣撰写或为书籍做出贡献，请访问[`authors.packtpub.com`](http://authors.packtpub.com)。

## 评论

请留下评论。一旦您阅读并使用了这本书，为何不在您购买它的网站上留下评论呢？潜在读者可以查看并使用您的客观意见来做出购买决定，Packt 公司可以了解您对我们产品的看法，我们的作者也可以看到他们对书籍的反馈。谢谢！

如需了解 Packt 的更多信息，请访问[packtpub.com](http://packtpub.com)。
