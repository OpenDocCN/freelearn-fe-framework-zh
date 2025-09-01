# 前言

决定学习 Angular 可能会感到相当压倒性。这是因为编写 Angular 应用程序的默认方式是使用超集语言 TypeScript，这是一种相对较新的语言。具有讽刺意味的是，TypeScript 通过提供严格类型（如 Java 等严格类型语言中看到的那样）简化了 Angular 应用程序的编写方式，这提高了我们编写的应用程序的预测行为。本书旨在通过解释 TypeScript 的核心概念，帮助那些对 TypeScript 或严格类型知之甚少或一无所知的初学者/中级 Angular 开发者。

# 本书涵盖内容

第一章，《从松散类型到严格类型》，讨论了在 TypeScript 引入之前开发者面临的问题，以及 TypeScript 解决了哪些问题。我们将通过讨论松散类型及其挑战，展示这些挑战之前是如何被解决的，以及为什么 TypeScript 是一个更好的选择。

第二章，《TypeScript 入门》，通过实际示例总结了 TypeScript 的核心概念，展示了如何设置一个纯 JavaScript 加上 TypeScript 的项目。在第一章，《从松散类型到严格类型》中，所有松散类型的示例都将用 TypeScript 重写，以展示 TypeScript 的效率。

第三章，《TypeScript 原生类型和特性》，深入探讨了与现有 JavaScript 松散类型匹配的内置 TypeScript 严格类型。每种类型都将通过工作示例进行详细讨论，展示其应该如何使用以及如何工作。

第四章，《使用 Angular 和 TypeScript 快速入门》，讨论了 TypeScript 如何应用于 Angular。为此，Angular 需要借助 CLI 工具来启动并运行。在本章中，我们将讨论 Angular 和 TypeScript 协同工作的要素。我们还将涵盖在*Hello World*示例中可以找到的基本 Angular 概念。

第五章，《使用 TypeScript 的高级自定义组件》，讨论了 Web 组件的概念以及 Angular 如何使用 TypeScript 构建在 Web 组件之上。我们将看到如何使用类创建组件，使用 TypeScript 接口实现生命周期钩子，以及使用 TypeScript 装饰器定义组件和元数据。

第六章，《使用 TypeScript 进行组件组合》，讨论了 Angular 基于组件的特性。它解释说，组件作为构建块组合在一起，以创建一个功能齐全的应用程序。我们将通过示例讨论组件的模块化使用，以及组件交互（数据传输和事件）。在这个过程中，我们将看到 TypeScript 是如何帮助我们处理所有这些动态部分的。

第七章，*使用强类型服务分离关注点*，讨论了允许我们的逻辑存在于组件中的做法是不好的。在这种情况下，Angular 允许你通过服务提供 API 方法，这些组件可以消费。我们将讨论 TypeScript 如何帮助我们在这类 API 方法和组件之间创建契约（使用类型）。

第八章，*使用 TypeScript 改进表单和事件处理*，解释了 Angular 表单模块如何使我们能够使用 TypeScript 编写可预测的强类型表单，这是从我们的应用程序用户收集数据的一种完美方式。我们还将看到如何使用强类型 DOM 事件（例如，点击、鼠标悬停和 keydown）来响应用户交互。

第九章，*使用 TypeScript 编写模块、指令和管道*，讨论了 Angular 的次要构建块及其如何与 TypeScript 最佳结合使用。你将学习如何在 Angular 中使用类型和装饰器构建自定义指令和管道。

第十章，*SPA 的客户端路由*，解释了单页应用程序（SPA），它是通过将路由委托给客户端而不是服务器来构建的。我们将讨论如何使用 Angular 和 TypeScript，我们可以使用路由模块仅通过单个服务器路由来构建多个视图应用程序。

第十一章，*使用真实托管数据工作*，深入探讨了使用 Angular 的 HTTP 模块消费 API 数据。你将学习如何在我们的 Angular 应用程序中直接发起 HTTP 请求。从这个请求获取的数据可以通过组件进行渲染。

第十二章，*测试和调试*，涵盖了单元测试 Angular 构建块的建议做法。这些包括组件、服务、路由等。

# 你需要这本书什么

这本书涵盖的示例可以在 Windows、Linux 或 macOS PC 上实现。你需要安装 Node 和 npm 来使用 TypeScript，并且需要一个不错的网络浏览器。

# 这本书是为谁而写的

这本书旨在通过解释 TypeScript 的核心概念，帮助那些对 TypeScript 或严格类型化知之甚少或一无所知的初学者/中级 Angular 开发者。它也是那些已经使用过 Angular 1.x 或其他框架并试图迁移到 Angular 2.x 的开发者的完美书籍。

# 习惯用法

在这本书中，你会发现许多文本样式，用于区分不同类型的信息。以下是一些这些样式的示例及其含义的解释。

文本中的代码单词、数据库表名、文件夹名、文件名、文件扩展名、路径名、虚拟 URL、用户输入和 Twitter 昵称如下所示："我们可以通过使用`include`指令来包含其他上下文。"

代码块设置如下：

```js
[default]
exten => s,1,Dial(Zap/1|30)
exten => s,2,Voicemail(u100)
exten => s,102,Voicemail(b100)
exten => i,1,Voicemail(s0)
```

当我们希望将您的注意力引到代码块的一个特定部分时，相关的行或项目将以粗体显示：

```js
[default]
exten => s,1,Dial(Zap/1|30)
exten => s,2,Voicemail(u100)
exten => s,102,Voicemail(b100)
exten => i,1,Voicemail(s0)
```

任何命令行输入或输出都应如下编写：

```js
# cp /usr/src/asterisk-addons/configs/cdr_mysql.conf.sample
/etc/asterisk/cdr_mysql.conf
```

**新术语**和**重要词汇**将以粗体显示。屏幕上显示的单词，例如在菜单或对话框中，将以如下方式显示：“点击下一步按钮将您带到下一屏幕。”

警告或重要提示将以这样的框显示。

小贴士和技巧看起来像这样。

# 读者反馈

我们欢迎读者的反馈。请告诉我们您对这本书的看法——您喜欢或不喜欢的地方。读者的反馈对我们非常重要，因为它帮助我们开发出您真正能从中受益的标题。

要发送一般反馈，请简单地将电子邮件发送到 `feedback@packtpub.com`，并在邮件主题中提及书的标题。

如果您在某个主题领域有专业知识，并且您有兴趣撰写或为本书做出贡献，请参阅我们的作者指南，网址为 [www.packtpub.com/authors](http://www.packtpub.com/authors)。

# 客户支持

现在您是 Packt 图书的骄傲拥有者，我们有一些事情可以帮助您从您的购买中获得最大收益。

# 下载示例代码

您可以从您的账户下载本书的示例代码文件，网址为 [`www.packtpub.com`](http://www.packtpub.com)。如果您在其他地方购买了这本书，您可以访问 [`www.packtpub.com/support`](http://www.packtpub.com/support) 并注册，以便将文件直接通过电子邮件发送给您。您可以通过以下步骤下载代码文件：

1.  使用您的电子邮件地址和密码登录或注册我们的网站。

1.  将鼠标指针悬停在顶部的 SUPPORT 标签上。

1.  点击代码下载与勘误。

1.  在搜索框中输入书的名称。

1.  选择您想要下载代码文件的书籍。

1.  从下拉菜单中选择您购买此书的来源。

1.  点击代码下载。

文件下载完成后，请确保您使用最新版本的软件解压缩或提取文件夹：

+   WinRAR / 7-Zip for Windows

+   Zipeg / iZip / UnRarX for Mac

+   7-Zip / PeaZip for Linux

本书代码包也托管在 GitHub 上，网址为 [`github.com/PacktPublishing/TypeScript-2.x-for-Angular-Developers`](https://github.com/PacktPublishing/TypeScript-2.x-for-Angular-Developers)。我们还有其他来自我们丰富图书和视频目录的代码包可供选择，网址为 [`github.com/PacktPublishing/`](https://github.com/PacktPublishing/)。请查看它们！

# 下载本书中的彩色图像

我们还为您提供了一个包含本书中使用的截图/图表的彩色图像的 PDF 文件。彩色图像将帮助您更好地理解输出的变化。您可以从 [`www.packtpub.com/sites/default/files/downloads/TypeScript2.xforAngularDevelopers_ColorImages.pdf`](https://www.packtpub.com/sites/default/files/downloads/TypeScript2.xforAngularDevelopers_ColorImages.pdf) 下载此文件。

# 勘误

尽管我们已经尽最大努力确保内容的准确性，但错误仍然可能发生。如果您在我们的书中发现错误——可能是文本或代码中的错误——如果您能向我们报告这一点，我们将不胜感激。通过这样做，您可以避免其他读者感到沮丧，并帮助我们改进本书的后续版本。如果您发现任何勘误，请通过访问[`www.packtpub.com/submit-errata`](http://www.packtpub.com/submit-errata)，选择您的书籍，点击勘误提交表单链接，并输入您的勘误详情来报告它们。一旦您的勘误得到验证，您的提交将被接受，勘误将被上传到我们的网站或添加到该标题的勘误部分下的现有勘误列表中。要查看之前提交的勘误，请访问[`www.packtpub.com/books/content/support`](https://www.packtpub.com/books/content/support)，并在搜索字段中输入书籍名称。所需信息将出现在勘误部分下。

# 盗版

互联网上版权材料的盗版是一个持续存在的问题，涉及所有媒体。在 Packt，我们非常重视保护我们的版权和许可证。如果您在网上发现任何形式的我们作品的非法副本，请立即提供位置地址或网站名称，以便我们可以寻求补救措施。请通过`copyright@packtpub.com`与我们联系，并提供疑似盗版材料的链接。我们感谢您的帮助，以保护我们的作者和我们为您提供有价值内容的能力。

# 问题

如果您对本书的任何方面有问题，您可以通过`questions@packtpub.com`联系我们，我们将尽力解决问题。
