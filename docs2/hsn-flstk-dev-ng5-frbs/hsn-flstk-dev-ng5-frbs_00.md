# 前言

*使用 Angular 5 和 Firebase 进行实战全栈开发*将为你提供开发 Web 应用所需的实际知识。在这本书中，我们介绍了编写完整社交媒体应用所需的所有主要工具和技术。本书以这样的方式编写，你将从头开始构建应用，随着你在书中的进展，你将了解 Web 开发的主要概念。作为我们开发过程的一部分，本书严格遵守常见的软件原则和编码标准。我还介绍了对 Angular 组件、服务和管道进行单元测试。这些软件开发实践使我们成为更好的开发者。

作为本书的一部分，我们将使用 Angular 作为前端和 Firebase 作为后端，从开发到生产构建 Web 应用。Firebase 完全可扩展且实时，提供了开发丰富、协作应用所需的所有工具。使用 Firebase，可以轻松构建和原型化任何商业应用，而无需创建复杂的后端服务。

你将使用 Angular 框架，通过 HTML 和 CSS 构建客户端应用程序。Angular 提供了高级功能，可以将客户端代码模块化到 HTML、CSS、组件和服务中。作为我们开发过程的一部分，我们还将集成其他常用库，如 RxJS。

因此，请期待一段精彩的旅程。

# 本书面向对象

本书面向那些对 Angular 框架有一定了解的 JavaScript 开发者，他们希望使用 Angular 和 Firebase 开始开发实时应用程序。对于任何希望将业务或想法在线化的小型初创公司来说，本书非常实用，因为它提供了快速开发应用程序的实际技巧。本书也非常适合那些希望在不进行大量投资的情况下构建完整 Web 应用的大学生。如果你正在寻找一种更实用、理论性较少的方法来学习主要 Web 应用概念，那么这本书适合你。

# 本书涵盖内容

第一章，*组织你的 Angular 项目结构*，让你了解 Angular 项目结构。你将使用 Angular CLI 命令创建 Angular 项目，并了解 Angular 项目中的所有重要库。

第二章，*创建注册组件*，介绍了如何启用 Firebase 身份验证并创建注册组件、模板和服务。你还将了解 AngularFire2 库。

第三章，*创建登录组件*，教你如何创建登录组件和模板。你还将执行重置密码操作。

第四章，*组件之间的路由和导航*，帮助您为模块启用路由并创建导航栏以在组件之间导航。

第五章, *创建用户个人资料页面*，专注于 RxJs 库并在组件模块之间传递数据。您还将创建带有编辑操作的个人信息页面。

第六章，*创建用户好友列表*，教您如何创建带有分页功能的好友列表页面。您还将创建好友服务和自定义 Angular 日期管道。

第七章, *探索 Firebase 存储*，讨论 Firebase 存储并为您的应用配置存储。您还将从 Firebase 存储上传和下载图片到您的应用中。

第八章，*创建聊天组件*，帮助您创建带有子组件的聊天模块。我们还学习了更多关于 SCSS 变量和 mixin 概念。

第九章, *将聊天组件与 Firebase 数据库连接*，涵盖了如何将您的聊天组件与 Firebase 数据库集成。您还将学习使用路由参数传递数据。

第十章, *对我们的应用进行单元测试*，教您关于 Angular 测试的知识。您将为我们的组件、服务和管道编写单元测试用例，并了解代码覆盖率。

第十一章, *调试技术*，涵盖了调试技术的不同方面。作为本章的一部分，我们将涵盖 Angular、Web、TypeScript、CSS 和网络调试。

第十二章, *Firebase 安全和托管*，教您关于 Firebase 安全并解释如何为用户和聊天消息添加安全规则。您还将学习如何创建多个环境并托管我们的好友应用。

第十三章，*使用 Firebase 扩展我们的应用*，涵盖了 Firebase 云消息。您还将了解 Google Analytics 和广告。

第十四章, *将我们的应用转换为 PWA*，涵盖了 PWA 功能并展示了如何使您的应用符合 PWA 规范。您还将了解服务工作者。

# 要充分利用这本书

要充分利用这本书，您应该有一些使用 HTML 和 CSS 进行 JavaScript 开发的经验。随着您通过章节的进展，将提供所有重要工具、编辑器或框架的链接，这些是开发 Angular 应用所需的。

# 下载示例代码文件

您可以从[www.packtpub.com](http://www.packtpub.com)的账户下载本书的示例代码文件。如果您在其他地方购买了本书，您可以访问[www.packtpub.com/support](http://www.packtpub.com/support)并注册，以便将文件直接通过电子邮件发送给您。

您可以通过以下步骤下载代码文件：

1.  在[www.packtpub.com](http://www.packtpub.com/support)登录或注册。

1.  选择支持选项卡。

1.  点击代码下载与勘误。

1.  在搜索框中输入书籍名称，并遵循屏幕上的说明。

文件下载完成后，请确保使用最新版本解压缩或提取文件夹：

+   WinRAR/7-Zip for Windows

+   Zipeg/iZip/UnRarX for Mac

+   7-Zip/PeaZip for Linux

本书代码包也托管在 GitHub 上，网址为[`github.com/PacktPublishing/Hands-On-Full-Stack-Development-with-Angular-5-and-Firebase`](https://github.com/PacktPublishing/Hands-On-Full-Stack-Development-with-Angular-5-and-Firebase)。如果代码有更新，它将在现有的 GitHub 仓库中更新。

我们还有其他来自我们丰富图书和视频目录的代码包可供选择，请访问**[`github.com/PacktPublishing/`](https://github.com/PacktPublishing/)**。查看它们！

# 使用的约定

在本书中使用了多种文本约定。

`CodeInText`: 表示文本中的代码单词、数据库表名、文件夹名、文件名、文件扩展名、路径名、虚拟 URL、用户输入和 Twitter 昵称。以下是一个示例：“将下载的`WebStorm-10*.dmg`磁盘映像文件作为系统中的另一个磁盘挂载。”

代码块按照以下方式设置：

```js
public addUser(user: User): void {
    this.fireDb.object(`${USERS_CHILD}/${user.uid}`).set(user);
}
```

任何命令行输入或输出都按照以下方式编写：

```js
$ cd <your directory>\friends\src\app
```

**粗体**: 表示新术语、重要单词或屏幕上看到的单词。例如，菜单或对话框中的单词在文本中显示如下。以下是一个示例：“点击认证。”

警告或重要说明看起来像这样。

小贴士和技巧看起来像这样。

# 联系我们

我们始终欢迎读者的反馈。

**一般反馈**: 发送电子邮件至`feedback@packtpub.com`，并在邮件主题中提及书籍标题。如果您对本书的任何方面有疑问，请通过`questions@packtpub.com`发送电子邮件给我们。

**勘误**: 尽管我们已经尽最大努力确保内容的准确性，但错误仍然可能发生。如果您在这本书中发现了错误，我们将非常感激您能向我们报告。请访问[www.packtpub.com/submit-errata](http://www.packtpub.com/submit-errata)，选择您的书籍，点击勘误提交表单链接，并输入详细信息。

**盗版**: 如果您在互联网上以任何形式发现我们作品的非法副本，我们将非常感激您能提供位置地址或网站名称。请通过`copyright@packtpub.com`与我们联系，并提供材料的链接。

**如果您有兴趣成为作者**：如果您在某个领域有专业知识，并且您有兴趣撰写或为书籍做出贡献，请访问 [authors.packtpub.com](http://authors.packtpub.com/)。

# 评论

请留下评论。一旦您阅读并使用了这本书，为何不在您购买它的网站上留下评论呢？潜在读者可以查看并使用您的客观意见来做出购买决定，我们 Packt 可以了解您对我们产品的看法，我们的作者也可以看到他们对书籍的反馈。谢谢！

如需了解 Packt 的更多信息，请访问 [packtpub.com](https://www.packtpub.com/)。
