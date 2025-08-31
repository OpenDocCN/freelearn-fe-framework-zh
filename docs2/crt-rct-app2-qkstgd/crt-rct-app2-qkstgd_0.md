# 前言

Create React App 在我看来是合适的引导工具的终极示例。而不是花费数小时进行乏味的调整和配置，希望一切恰到好处且没有遗漏任何东西，您反而得到了一个工具和一个项目，从您想要开始项目的那一刻起，它就已经构建好并准备好了。现在，启动一个功能齐全的项目所需的时间从数小时，甚至可能几天，缩短到几分钟，没有任何东西能阻止您构建您梦想中的 React 项目！

这本书是为那些想要深入了解 Create React App 工具的人准备的。我们将涵盖所有命令，所有 2.0 版本的新增功能，我们还将从头开始构建项目，并在过程中涉及所有关键概念，以确保您能够充分利用这个工具。

# 这本书的适用对象

这本书是为那些有一定 JavaScript 知识，并希望更好地利用 Create React App 引导工具开始构建自己的精彩 React 项目的人设计的。如果您想提高 JavaScript 技能，并考虑最佳实践来构建项目，这本书也会适合您！即使您是 React 的老手，但从未或很少使用 Create React App 来开始项目，这本书也会给您带来很多学习和工作的机会！

# 为了充分利用这本书

您需要在您的计算机上具备一些设置 Node.js 的经验以及编程经验，并且您还需要一个兼容的代码编辑器，最好是能显示您项目目录结构的编辑器。

# 下载示例代码文件

您可以从[www.packt.com](http://www.packt.com)的账户下载这本书的示例代码文件。如果您在其他地方购买了这本书，您可以访问[www.packt.com/support](http://www.packt.com/support)并注册，以便将文件直接通过电子邮件发送给您。

您可以通过以下步骤下载代码文件：

1.  在[www.packt.com](http://www.packt.com)登录或注册

1.  选择 SUPPORT 标签

1.  点击代码下载与勘误

1.  在搜索框中输入书籍名称，并遵循屏幕上的说明

文件下载完成后，请确保使用最新版本的软件解压或提取文件夹：

+   WinRAR/7-Zip for Windows

+   Zipeg/iZip/UnRarX for Mac

+   7-Zip/PeaZip for Linux

这本书的代码包也托管在 GitHub 上，网址为[`github.com/PacktPublishing/Create-React-App-2.0-Quick-Start-Guide`](https://github.com/PacktPublishing/Create-React-App-2.0-Quick-Start-Guide)。如果代码有更新，它将在现有的 GitHub 仓库中更新。

我们还有其他来自我们丰富的书籍和视频目录的代码包，可在[`github.com/PacktPublishing/`](https://github.com/PacktPublishing/)找到。查看它们吧！

# 下载彩色图像

我们还提供了一个包含本书中使用的截图/图表的彩色图像的 PDF 文件。您可以从这里下载：[`www.packtpub.com/sites/default/files/downloads/9781789952766_ColorImages.pdf`](https://www.packtpub.com/sites/default/files/downloads/9781789952766_ColorImages.pdf)。

# 约定

本书使用了多种文本约定。

`CodeInText`: 表示文本中的代码单词、数据库表名、文件夹名、文件名、文件扩展名、路径名、虚拟 URL、用户输入和 Twitter 昵称。以下是一个示例：“在`App.js`文件的顶部，我们将添加我们的`import`语句。”

代码块设置如下：

```js
const App = () => {
  return <div className="App">Homepage!</div>;
};
```

任何命令行输入或输出都按照以下方式编写：

```js
$ yarn add bootstrap@4 reactstrap@6.5.0
```

**粗体**: 表示新术语、重要单词或屏幕上看到的单词。例如，菜单或对话框中的单词在文本中显示如下。以下是一个示例：“从管理面板中选择系统信息。”

警告或重要提示看起来像这样。

小贴士和技巧看起来像这样。

# 联系我们

我们欢迎读者的反馈。

**一般反馈**: 如果您对本书的任何方面有疑问，请在邮件主题中提及书名，并给我们发送电子邮件至`customercare@packtpub.com`。

**勘误**: 尽管我们已经尽最大努力确保内容的准确性，但错误仍然可能发生。如果您在这本书中发现了错误，我们将非常感激您能向我们报告。请访问[www.packt.com/submit-errata](http://www.packt.com/submit-errata)，选择您的书籍，点击勘误提交表单链接，并输入详细信息。

**盗版**: 如果您在互联网上发现我们作品的任何非法副本，我们将非常感激您能提供位置地址或网站名称。请通过`copyright@packt.com`与我们联系，并提供材料的链接。

**如果您有兴趣成为作者**：如果您在某个领域有专业知识，并且您有兴趣撰写或为书籍做出贡献，请访问[authors.packtpub.com](http://authors.packtpub.com/)。

# 评论

请留下评论。一旦您阅读并使用了这本书，为何不在您购买它的网站上留下评论呢？潜在的读者可以查看并使用您的客观意见来做出购买决定，Packt 公司可以了解您对我们产品的看法，我们的作者也可以看到他们对书籍的反馈。谢谢！

如需更多关于 Packt 的信息，请访问[packt.com](http://www.packt.com/)。
