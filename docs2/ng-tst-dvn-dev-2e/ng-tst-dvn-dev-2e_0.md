# 前言

本书将为读者提供关于以 JavaScript 为重点的测试驱动开发（TDD）的完整指南，然后深入 Angular 方法。它将提供清晰、逐步的示例，以不断强化 TDD 中的最佳实践。本书将探讨使用 Karma 进行单元测试和使用 Protractor 进行端到端测试，不仅关注如何使用这些工具，还理解它们被构建的原因以及为什么应该使用它们。在整个过程中，将重点关注何时、何地以及如何使用这些工具，不断强化测试驱动开发生命周期（测试、执行和重构）的原则。

本书中的所有示例均基于 Angular v2，且与 Angular v4 兼容。

# 本书涵盖的内容

第一章*测试驱动开发简介*通过解释为什么以及如何 TDD 可以帮助开发过程，向我们介绍了测试驱动开发的基本原理。

第二章*JavaScript 测试的细节*在 JavaScript 环境中涵盖了 TDD。本章探讨了 JavaScript 应用程序中需要的测试类型，如单元测试、行为测试、集成测试和端到端测试。这也解释了不同类型的 JavaScript 测试工具、框架及其在 Angular 应用程序中的用途。

第三章*Karma 之道*探讨了 Karma 的起源以及为什么它必须在任何 Angular 项目中使用。到本章结束时，读者不仅将理解 Karma 解决的问题，还将通过 Karma 的完整示例进行实践。

第四章*使用 Protractor 进行端到端测试*查看端到端测试应用程序，贯穿应用程序的所有层。本章向读者介绍了来自 Angular 团队的端到端测试工具 Protractor。然后解释了为什么创建 Protractor 以及它是如何解决问题的。最后，逐步指导读者如何在现有的 Angular 项目中使用 TDD 安装、配置和使用 Protractor。

第五章*量角器，更进一步*对 Protractor 进行了更深入的探讨，并探索了一些高级配置。然后，它通过示例解释了测试套件的调试过程。这也探讨了 Protractor 的一些常用 API 及其相关示例。

第六章*第一步*通过介绍如何使用 TDD 用类、组件和服务构建 Angular 应用程序，提供了一个入门级教程。本章帮助读者开始 TDD 之旅，并看到基础原理的实际应用。到目前为止，本书一直专注于 TDD 和工具的基础。然后，通过更进一步，它深入探讨了 Angular 中的 TDD。

第七章*开关*继续扩展我们应用 TDD（测试驱动开发）的知识，使用 Angular 的路由和导航功能通过我们的示例 Angular 应用获取结果集。除了 Angular 功能外，本章还指导读者如何借助 Protractor 对这些特定功能进行端到端测试。

第八章*告诉世界*，涵盖了更多针对示例 Angular 应用的单元测试，包括路由和导航。除此之外，本章还对现有代码进行了重构，使其更易于测试。然后，在重构代码的同时介绍了 Angular 服务和事件广播，并引入 MockBackend 来测试服务中的 HTTP 请求。

# 你需要这本书的内容

在这本书中，我们使用了**Node 包管理器**（**npm**）作为我们的命令工具来运行应用程序和各种测试工具。因此，在您的操作系统上全局安装 npm 是先决条件。要安装它，您必须在操作系统上安装 Node.js。

我们不会探讨如何安装 Node.js 和 npm。关于在任意操作系统上安装它们的资源已经很多了。

# 这本书面向的对象

这本书是为那些对 Angular 有基本经验但想了解何时、为什么以及如何应用测试技术和最佳实践以创建高质量代码的开发的读者而写的。为了最大限度地利用这本书，你应该对 HTML、CSS 和 JavaScript 有良好的理解，并对 Angular 和 TypeScript 有基本的了解。

# 惯例

在这本书中，您将找到许多文本样式，用于区分不同类型的信息。以下是一些这些样式的示例及其含义的解释。

文本中的代码单词、数据库表名、文件夹名、文件名、文件扩展名、路径名、虚拟 URL、用户输入和 Twitter 用户名如下所示："接下来的代码读取链接并将其分配给`Calculator`函数。"

代码块设置如下：

```js
var calculator = {
   multiply : function(amount1, amount2) {
       return amount1 * amount2;
   }
};
```

当我们希望将您的注意力引到代码块的一个特定部分时，相关的行或项目将被设置为粗体：

```js
<!DOCTYPE html>
<html>
  <head>
    <title>Test Runner</title>
  </head>
  <body>
    // ...
 <script src="img/calculator.js"></script>
  </body>
</html>
```

任何命令行输入或输出都应如下所示：

```js
$ npm install protractor
$ npm protractor --version

```

**新术语**和**重要词汇**以粗体显示。屏幕上看到的单词，例如在菜单或对话框中，在文本中如下所示："为了下载新模块，我们将前往**文件** | **设置** | **项目名称** | **项目解释器**"。

### 注意事项

警告或重要注意事项以如下框的形式出现。

### 小贴士

小技巧和技巧看起来像这样。

# 读者反馈

我们欢迎读者的反馈。请告诉我们您对这本书的看法——您喜欢或不喜欢的地方。读者反馈对我们很重要，因为它帮助我们开发出您真正能从中获得最大价值的标题。要发送一般反馈，请简单地发送电子邮件至 feedback@packtpub.com，并在邮件主题中提及书籍标题。如果您在某个主题领域有专业知识，并且对撰写或为书籍做出贡献感兴趣，请参阅我们的作者指南[www.packtpub.com/authors](http://www.packtpub.com/authors)。

# 客户支持

现在您已经是 Packt 图书的骄傲拥有者，我们有一些事情可以帮助您从您的购买中获得最大价值。

## 下载示例代码

您可以从您的账户[`www.packtpub.com`](http://www.packtpub.com)下载本书的示例代码文件。如果您在其他地方购买了这本书，您可以访问[`www.packtpub.com/support`](http://www.packtpub.com/support)并注册，以便将文件直接通过电子邮件发送给您。

您可以通过以下步骤下载代码文件：

1.  使用您的电子邮件地址和密码登录或注册我们的网站。

1.  将鼠标指针悬停在顶部的**支持**选项卡上。

1.  点击顶部的**代码下载与勘误**。

1.  在**搜索**框中输入书籍名称。

1.  选择您想要下载代码文件的书籍。

1.  从下拉菜单中选择您购买此书的来源。

1.  点击**代码下载**。

下载文件后，请确保您使用最新版本解压缩或提取文件夹。

+   Windows 上的 WinRAR / 7-Zip

+   Mac 上的 Zipeg / iZip / UnRarX

+   Linux 上的 7-Zip / PeaZip

本书代码包也托管在 GitHub 上，网址为[`github.com/PacktPublishing/Angular-Test-Driven-Development-Second-Edition`](https://github.com/PacktPublishing/Angular-Test-Driven-Development-Second-Edition)。我们还有其他来自我们丰富图书和视频目录的代码包可供选择，网址为[`github.com/PacktPublishing/`](https://github.com/PacktPublishing/)。请查看它们！

## 下载本书的彩色图像

我们还为您提供了一个包含本书中使用的截图/图表彩色图像的 PDF 文件。这些彩色图像将帮助您更好地理解输出中的变化。您可以从[`www.packtpub.com/sites/default/files/downloads/AngularTestDrivenDevelopment_ColorImages.pdf`](https://www.packtpub.com/sites/default/files/downloads/AngularTestDrivenDevelopment_ColorImages.pdf)下载此文件。

## 勘误

尽管我们已经尽最大努力确保内容的准确性，但错误仍然可能发生。如果您在我们的书中发现错误——可能是文本或代码中的错误——如果您能向我们报告这个问题，我们将不胜感激。通过这样做，您可以避免其他读者的挫败感，并帮助我们改进本书的后续版本。如果您发现任何勘误，请通过访问 [`www.packtpub.com/submit-errata`](http://www.packtpub.com/submit-errata)，选择您的书籍，点击**勘误提交表单**链接，并输入您的勘误详情来报告它们。一旦您的勘误得到验证，您的提交将被接受，勘误将被上传到我们的网站或添加到该标题的勘误部分下的现有勘误列表中。

要查看之前提交的勘误，请访问 [`www.packtpub.com/books/content/support`](https://www.packtpub.com/books/content/support)，并在搜索字段中输入书籍名称。所需信息将出现在**勘误**部分下。

## 侵权

在互联网上对版权材料的侵权是一个跨所有媒体的持续问题。在 Packt，我们非常重视保护我们的版权和许可证。如果您在互联网上发现任何形式的我们作品的非法副本，请立即向我们提供位置地址或网站名称，以便我们可以寻求补救措施。

请通过发送邮件至 copyright@packtpub.com 并附上涉嫌侵权材料的链接与我们联系。

我们感谢您在保护我们的作者和我们为您提供有价值内容的能力方面的帮助。

## 问题

如果您在这本书的任何方面遇到问题，您可以给我们发送邮件至 questions@packtpub.com，我们将尽力解决问题。
