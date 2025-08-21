# 前言

欢迎！如果你想学习并精通 Angular 开发，你来对地方了。这本书旨在向你灌输敏捷和 DevOps 的思维，让你能够自信地创建可靠和灵活的解决方案。无论你是自由职业者为小型企业开发软件，全栈开发人员，企业开发人员还是网页开发人员，你需要了解如何设计、架构、开发、维护、交付和部署 Web 应用程序，以及需要应用的最佳实践和模式并没有太大的差异。如果你要向用户群交付应用程序，从某种意义上说，你是一个全栈开发人员，因为你必须了解许多服务器技术。事实上，如果你掌握了如何使用 TypeScript 交付 Angular 应用程序，那么使用 Node.js、Express.js 和 TypeScript 编写自己的 RESTful API 对你来说并不困难，但这超出了本书的范围。

根据某些定义，全栈开发人员需要了解从满足国际版权法到成功在当今的网络上创建和运营应用程序的一切。从某种意义上说，如果你是一名企业家，这是正确的。然而，在这本书中，你的烹饪技能和法律学位并不适用。这本书假设你已经知道如何使用你选择的技术栈编写 RESTful API，如果不知道，不要担心！你仍然可以受益并了解如何使用 RESTful API 工作。

# 本书适合对象

这本书既适合初学者又适合有经验的开发人员，他们想学习 Angular 或者网页开发。如果你是 Angular 开发人员，你将接触到设计和部署 Angular 应用程序到生产环境的整个过程。你将学习易于理解并能够教给他人的 Angular 模式。如果你是自由职业者，你将掌握交付 Angular 应用程序的有效工具和技术，以安全、自信和可靠的方式。如果你是企业开发人员，你将学习编写具有可扩展架构的 Angular 应用程序的模式和实践。

# 充分利用本书

1.  你应该已经熟悉全栈 Web 开发

1.  按照出版顺序跟随本书，在每一章的内容旁边编写你的解决方案。

# 下载示例代码文件

您可以从[www.packtpub.com](http://www.packtpub.com)的帐户下载本书的示例代码文件。如果您在其他地方购买了这本书，您可以访问[www.packtpub.com/support](http://www.packtpub.com/support)并注册，以便文件直接通过电子邮件发送给您。

您可以按照以下步骤下载代码文件：

1.  在[www.packtpub.com](http://www.packtpub.com/support)上登录或注册。

1.  选择“支持”选项卡。

1.  单击“代码下载和勘误”。

1.  在搜索框中输入书名，然后按照屏幕上的说明操作。

下载文件后，请确保使用最新版本的解压缩软件解压缩文件夹。

+   WinRAR/7-Zip for Windows

+   Zipeg/iZip/UnRarX for Mac

+   7-Zip/PeaZip for Linux

该书的代码包托管在 GitHub 上，网址为**[`github.com/PacktPublishing/Angular-6-for-Enterprise-Ready-Web-Applications`](https://github.com/PacktPublishing/Angular-6-for-Enterprise-Ready-Web-Applications)**。

该书的代码包也托管在作者的 GitHub 存储库中，网址为[`github.com/duluca/local-weather-app`](https://github.com/duluca/local-weather-app)和[`github.com/duluca/lemon-mart`](https://github.com/duluca/lemon-mart)。

我们还提供来自我们丰富书籍和视频目录的其他代码包，可在**[`github.com/PacktPublishing/`](https://github.com/PacktPublishing/)**上找到。去看看吧！

# 使用的约定

本书中使用了许多文本约定。

`CodeInText`：表示文本中的代码词、数据库表名、文件夹名、文件名、文件扩展名、路径名、虚拟 URL、用户输入和 Twitter 句柄。这是一个例子：“将下载的`WebStorm-10*.dmg`磁盘映像文件挂载为系统中的另一个磁盘。”

代码块设置如下：

```ts
{
  "name": "local-weather-app",
  "version": "0.0.0",
  "license": "MIT",
  **...**
```

当我们希望引起您对代码块的特定部分的注意时，相关行或项目将以粗体显示：

```ts
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

```ts
$ brew tap caskroom/cask
```

Windows 特定的命令行输入或输出如下所示：

```ts
PS> Set-ExecutionPolicy AllSigned; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
```

**粗体**：表示新术语、重要单词或您在屏幕上看到的单词。例如，菜单或对话框中的单词会以这种方式出现在文本中。这是一个例子：“启动开始菜单。”

警告或重要说明会出现在这样的样式中。提示和技巧会出现在这样的样式中。
