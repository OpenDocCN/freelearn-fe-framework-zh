# 前言

本书，*使用 JHipster 进行全栈开发*，旨在解决全栈开发者今天面临的以下挑战：

+   有大量的技术和选项需要学习

+   客户需求增加，因此上市时间变得更加紧迫

+   客户端框架变得复杂，难以集成

+   技术和概念之间的集成如此之多，以至于大多数新手甚至熟练的开发者都会感到不知所措

JHipster 为开发者提供了一个平台，使他们能够轻松地从零开始创建 Web 应用和微服务，无需花费大量时间来连接一切和集成技术。这为开发者节省了大量时间，使他们能够真正专注于解决方案，而不是花费时间学习和编写样板代码。JHipster 将帮助新手和经验丰富的开发者从第一天起就提高生产力。这就像与整个社区进行结对编程。

本书将带您从零开始，成为全栈开发的英雄。您将学习如何从头开始使用 JHipster 创建复杂的、生产就绪的 Spring Boot 和 Angular Web 应用，并将继续在云服务上开发部署功能和业务逻辑。您还将了解微服务，以及如何使用 JHipster 将单体应用转换为随着其发展而演变的微服务架构。此外，您还将学习如何利用 JHipster 中引入的新 React 支持，以及来自 JHipster 社区和核心开发团队的各项最佳实践和建议。

# 本书面向的对象

任何对构建 Java Web 应用有基本了解，并对 Spring 和 Angular/React 有基本接触的人，都可以通过使用本书学习如何使用 JHipster 进行前沿的全栈开发，或者通过减少样板代码和掌握新技术来提高他们的生产力。读者可以大致分为以下几类：

+   想要减少编写的样板代码并节省时间的全栈 Web 应用开发者，尤其是对于绿色项目。

+   想要学习使用 Angular 或 React 进行全栈开发的后端开发者

+   想要学习微服务开发的全栈开发者

+   想要快速启动全栈 Web 应用或微服务开发的开发者

+   想要快速原型化 Web 应用或微服务的开发者

# 本书涵盖的内容

第一章，*现代 Web 应用开发简介*，介绍了两种广泛使用的全栈 Web 应用开发架构。它还概述了全栈 Web 应用开发中常见的一些挑战。

第二章，*开始使用 JHipster*，介绍了 JHipster 平台。它还将为读者提供一个关于 JHipster 提供的不同服务器端、客户端和数据库技术选项的简要概述。本章还将提供安装和使用 JHipster 及其支持的各个工具和选项的说明。

第三章，*使用 JHipster 构建单体 Web 应用程序*，指导用户从头开始使用 JHipster 创建生产就绪的 Spring boot 和 Angular Web 应用程序，并将读者带入生成的代码、屏幕和概念。

第四章，*使用 JHipster 领域语言进行实体建模*，向读者介绍了 JHipster 领域语言（JDL），并教授如何使用实体建模和 JDL 以及 JDL studio 创建实体来构建业务逻辑。

第五章，*定制和进一步开发*，指导读者进一步开发生成的应用程序。它还将教授读者更多关于使用 Angular、Bootstrap、Spring Security、Spring MVC REST 和 Spring Data 等技术的方法。

第六章，*测试和持续集成*，指导读者进行测试并使用 Jenkins 设置持续集成管道。

第七章，*进入生产阶段*，展示了读者如何使用 Docker 以及如何构建和打包应用程序以用于生产。它还将介绍 JHipster 支持的某些生产云部署选项。

第八章，*微服务服务器端技术简介*，概述了 JHipster 微服务堆栈中可用的不同选项。

第九章，*使用 JHipster 构建微服务*，指导读者将 JHipster 单体 Web 应用程序转换为具有网关、注册表、监控控制台和多个微服务的完整微服务架构。它还将指导读者了解生成的代码和组件，如 JHipster 注册表、JHipster 控制台、API 网关和 JWT。

第十章，*与微服务一起工作*，指导读者在本地运行生成的应用程序并使用 JHipster 领域语言为微服务架构创建领域实体。

第十一章，*使用 Docker Compose 部署*，向读者介绍了微服务的先进本地和云部署选项。它还将指导用户使用 Docker Compose 和 JHipster 进行生成的微服务堆栈的本地部署和测试。

第十二章，*使用 Kubernetes 在云中部署*，指导用户使用 Kubernetes 和 JHipster 将生成的微服务堆栈部署到 Google 云。

第十三章，*使用 React 进行客户端开发*，使用 JHipster 生成应用程序，在客户端使用 React 而不是 Angular。

第十四章，*使用 JHipster 的最佳实践*，总结了读者迄今为止所学的内容，并将建议最佳实践和下一步骤来利用所学的技能。

# 为了充分利用本书

为了充分利用本书，您需要了解以下技术的基础知识：

+   网络技术（HTML、JavaScript 和 CSS）

+   Java 8

+   Spring 框架的基础知识

+   对 SQL 数据库的基本理解

+   构建工具（Maven 或 Gradle）

+   npm 或 Yarn

如果您熟悉使用 Docker 和 Kubernetes 等技术，这将使您更容易掌握一些章节。

您还需要安装 JDK8、Git、Docker 和 NodeJS；您喜欢的网络浏览器；一个终端应用程序；以及您喜欢的代码编辑器/IDE。

# 下载示例代码文件

您可以从[www.packtpub.com](http://www.packtpub.com)的账户下载本书的示例代码文件。如果您在其他地方购买了本书，您可以访问[www.packtpub.com/support](http://www.packtpub.com/support)并注册，以便将文件直接通过电子邮件发送给您。

您可以通过以下步骤下载代码文件：

1.  在 [www.packtpub.com](http://www.packtpub.com/support) 登录或注册。

1.  选择 SUPPORT 标签。

1.  点击代码下载与勘误。

1.  在搜索框中输入本书的名称，并遵循屏幕上的说明。

一旦文件下载完成，请确保使用最新版本的软件解压缩或提取文件夹：

+   WinRAR/7-Zip for Windows

+   Zipeg/iZip/UnRarX for Mac

+   7-Zip/PeaZip for Linux

本书代码包托管在 GitHub 上，地址为**[`github.com/PacktPublishing/Full-Stack-Development-with-JHipster`](https://github.com/PacktPublishing/Full-Stack-Development-with-JHipster)**。如果代码有更新，它将在现有的 GitHub 仓库中更新。 

我们还有其他丰富的图书和视频的代码包，可在**[`github.com/PacktPublishing/`](https://github.com/PacktPublishing/)**找到。查看它们！

# 使用的约定

本书使用了多种文本约定。

`CodeInText`：表示文本中的代码单词、数据库表名、文件夹名、文件名、文件扩展名、路径名、虚拟 URL、用户输入和 Twitter 昵称。以下是一个示例：“在后台，修改`ProductOrderService.java`中的`save`方法以创建发票和运货单，并为`ProductOrder`保存它们。”

代码块设置如下：

```js
entity Product {
    name String required
    description String
    price BigDecimal required min(0)
    size Size required
    image ImageBlob
}

enum Size {
    S, M, L, XL, XXL
}

entity ProductCategory {
    name String required
    description String
}
```

当我们希望您注意代码块中的特定部分时，相关的行或项目将以粗体显示：

```js
entity ProductOrder {
    placedDate Instant required
    status OrderStatus required
    invoiceId Long
    code String required
}
```

任何命令行输入或输出都按照以下方式编写：

```js
> cd invoice
> ./gradlew
```

**粗体**：表示新术语、重要单词或您在屏幕上看到的单词。例如，菜单或对话框中的单词在文本中显示如下。以下是一个示例：“您可以通过 Gateway 应用程序进行替代测试。登录到我们的 Gateway 应用程序，然后导航到 Administration | Gateway。”

警告或重要提示看起来像这样。

小贴士和技巧看起来像这样。

# 联系我们

我们始终欢迎读者的反馈。

**一般反馈**：请将电子邮件发送至 `feedback@packtpub.com`，并在邮件主题中提及书籍标题。如果您对本书的任何方面有疑问，请通过 `questions@packtpub.com` 发送电子邮件给我们。

**勘误**：尽管我们已经尽一切努力确保内容的准确性，但错误仍然可能发生。如果您在这本书中发现了错误，我们将不胜感激，如果您能向我们报告这个错误。请访问 [www.packtpub.com/submit-errata](http://www.packtpub.com/submit-errata)，选择您的书籍，点击勘误提交表单链接，并输入详细信息。

**盗版**：如果您在互联网上以任何形式发现我们作品的非法副本，如果您能提供位置地址或网站名称，我们将不胜感激。请通过 `copyright@packtpub.com` 联系我们，并提供材料的链接。

**如果您有兴趣成为作者**：如果您在某个领域有专业知识，并且您有兴趣撰写或为书籍做出贡献，请访问 [authors.packtpub.com](http://authors.packtpub.com/).

# 评论

请留下评论。一旦您阅读并使用了这本书，为何不在您购买它的网站上留下评论呢？潜在读者可以查看并使用您的客观意见来做出购买决定，Packt 公司可以了解您对我们产品的看法，我们的作者也可以看到他们对书籍的反馈。谢谢！

如需了解更多关于 Packt 的信息，请访问 [packtpub.com](https://www.packtpub.com/).
