# 前言

Angular 是谷歌推出的用于构建 Web 应用程序的框架。与 AngularJS 相比，这是一个全新的产品。

AngularJS 以性能问题而闻名，并且并不一定很容易上手。只要你了解框架的具体细节和潜在瓶颈，一切都可能顺利进行。此外，AngularJS 通常被视为一个大工具箱，里面有很多工具，让你可以以多种不同的方式构建应用程序，最终导致同一逻辑的各种实现取决于开发人员。

Angular 在性能方面带来了巨大的改进，同时也是一个更简单、更直接的框架。Angular 简单地让你用更少的代码做更多的事情。

谷歌从 Angular 开发的开始就宣布，该框架将是一个全新的产品，不兼容 AngularJS，尽管他们可能会尝试提供一些工具来简化过渡。通常情况下，从头开始重写应用程序可能是迁移的最佳解决方案。在这种情况下，开发人员需要学习 Angular 框架的关键部分，以启动应用程序和开发它的最佳实践，以及调试和基准应用程序的现有工具。

通过对最有价值的设计模式进行全面的介绍，并清晰地指导如何在 Angular 中有效地使用它们，本书为你提供了学习 Angular 和将其用于满足当今 Web 开发所需的稳定性和质量的最佳途径之一。

我们将带领读者走进 Angular 在现实世界中的设计之旅，结合案例研究、设计模式和要遵循的反模式。

在本书结束时，你将了解 Angular 的各种特性，并能够在工作中应用广为人知的、经过行业验证的设计模式。

# 本书的受众

本书适用于希望增进对 Angular 的理解并将其应用于实际应用程序开发的新手 Angular 开发人员。

# 本书涵盖的内容

第一章《TypeScript 最佳实践》描述了 TypeScript 语言的一些最佳实践。虽然 Angular 与其他编程语言兼容，但在本书中我们使用 TypeScript。TypeScript 功能强大且表达力强，但也有一些需要避免的“坑”。

第二章，*Angular 引导*，允许我们使用最佳可用工具来创建、构建和部署我们的应用程序。

第三章，*经典模式*，在 Angular 的上下文中重新审视了一些众所周知的面向对象模式。

第四章，*导航模式*，侧重于不同的导航 Angular 应用程序的方式。

第五章，*稳定性模式*，介绍了可以用来确保实际 Angular 应用程序稳定性的不同稳定性模式。

第六章，*性能模式*，基于谷歌对 Angular 进行的巨大性能改进，并描述了适用于改进应用程序性能的模式。

第七章，*操作模式*，侧重于在使用众所周知的设计模式实现功能并使用一些性能和稳定性模式后，使我们的应用程序准备好进行操作。

# 为了充分利用本书

为了充分利用本书，读者需要了解 Angular、Typescript 和面向对象编程。

# 下载示例代码文件

您可以从[www.packtpub.com](http://www.packtpub.com)的帐户中下载本书的示例代码文件。如果您在其他地方购买了本书，可以访问[www.packtpub.com/support](http://www.packtpub.com/support)并注册，以便直接通过电子邮件接收文件。

您可以按照以下步骤下载代码文件：

1.  在[www.packtpub.com](http://www.packtpub.com/support)上登录或注册。

1.  选择“支持”选项卡。

1.  点击“代码下载和勘误”。

1.  在搜索框中输入书名，并按照屏幕上的说明操作。

下载文件后，请确保使用最新版本的解压缩软件解压缩文件夹：

+   Windows 上的 WinRAR/7-Zip

+   Mac 上的 Zipeg/iZip/UnRarX

+   Linux 上的 7-Zip/PeaZip

该书的代码包也托管在 GitHub 上，网址为[`github.com/PacktPublishing/Angular-Design-Patterns`](https://github.com/PacktPublishing/Angular-Design-Patterns)。我们还有来自丰富书籍和视频目录的其他代码包，可在**[`github.com/PacktPublishing/`](https://github.com/PacktPublishing/)**上找到。快去看看吧！

# 下载彩色图片

我们还提供了一个 PDF 文件，其中包含本书中使用的屏幕截图/图表的彩色图片。您可以在这里下载：[`www.packtpub.com/sites/default/files/downloads/AngularDesignPatterns_ColorImages.pdf`](http://www.packtpub.com/sites/default/files/downloads/AngularDesignPatterns_ColorImages.pdf)。

# 使用的约定

本书中使用了许多文本约定。

`CodeInText`：表示文本中的代码词、数据库表名、文件夹名、文件名、文件扩展名、路径名、虚拟 URL、用户输入和 Twitter 句柄。这是一个例子：“`APIService`，显示了`@Injectable()`注释，使其可以注入。”

代码块设置如下：

```ts
interface Animal{ 
   eat():void; 
   sleep():void; 
} 
```

当我们希望引起您对代码块的特定部分的注意时，相关行或项目以粗体设置：

```ts
 ReferenceError: window is not defined
```

任何命令行输入或输出都是这样写的：

```ts
$ curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash -
$ sudo apt-get install -y Node.js
```

**粗体**：表示一个新术语、一个重要词或屏幕上看到的词。例如，菜单或对话框中的单词会以这样的方式出现在文本中。这是一个例子：“`Model`根据控制器发送的命令存储应用程序所需的数据。”

警告或重要说明是这样出现的。提示和技巧是这样出现的。
