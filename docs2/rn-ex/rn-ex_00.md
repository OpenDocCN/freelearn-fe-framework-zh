# 前言

React Native 是一个非常强大的框架，它使得以网络为中心的程序员能够更容易地在多个平台上进行开发。在这本书中，你将学习如何使用 React Native 构建移动应用，这些应用可以部署到 iOS App Store 和 Google Play。

# 本书涵盖的内容

第一章，*第一个项目 - 创建基本待办事项应用*，开始了使用 React Native 编写待办事项应用的过程。你将规划该应用并了解 StyleSheet、Flexbox 和 ES6 的概览。你还将使用 React Native SDK 的四个不同部分来创建应用的构建块。

第二章，*待办事项应用的高级功能*，深入探讨了第一章中开始构建的应用。你将学习如何处理导航、日期和时间选择、构建按钮，并为应用创建一个自定义可折叠和动画组件。你还将将这些课程内容转化为应用的 Android 版本。

第三章，*第二个项目 - 预算应用*，将开始本书的第二个项目。你将规划一个支出跟踪应用，为 React Native 安装第三方矢量图标库，创建可以在整个应用中使用的工具文件，并创建一个模态组件。

第四章，*预算应用的高级功能*，是第二个项目的延续。你将学习如何为用户创建一个类似下拉组件，以便从项目列表中进行选择，并为应用创建标签导航。

第五章，*第三个项目 - Facebook 客户端*，将开始本书的第三个也是最后一个项目。你将规划一个连接到第三方 Facebook SDK 的应用，将 SDK 安装到你的项目中，允许用户使用 Facebook 凭证登录，然后进行信息请求。

第六章，*使用 Facebook 客户端的高级功能*，总结了上一章开始的项目。你将学习如何为应用构建下拉刷新机制，为用户渲染图片，允许用户在不离开应用的情况下打开链接，然后使用这些课程内容制作应用的 Android 版本。

第七章，*添加 Redux*，介绍了流行的 Redux 架构。你将学习如何将第二章中的待办事项应用转换为支持 Redux 原则的应用。

第八章，*部署你的应用*，展示了如何打包、上传并使你的应用可在 Apple iOS 应用商店和 Google Play 商店下载。你还将获得一些创建应用标志和截图的技巧，以及如何启动应用的 beta 测试。

第九章，*额外的 React Native 组件*，深入探讨了我们在本书其他部分未能涵盖的 React Native SDK 的部分。在其中，你将构建一个游乐场风格的 app，学习 SDK 的不同部分。你将从任何第三方端点获取数据，控制用户的振动马达，通过你的 app 中的链接打开其他已安装的应用，等等。你还将学习如何将第四章中的预算应用转换为 Android，因为那个章节的空间有限。

# 你需要这本书什么

在硬件方面，你需要一台 Mac 来使用这本书。本书的内容以 iOS 为主，要开发 iOS 应用，你必须拥有苹果电脑。可选地，iOS 和 Android 设备对于在设备上测试应用会有所帮助，但不是必需的。本书的最后一章有一个 API 需要物理设备来测试（振动），另一个 API 在物理设备上测试会更简单（链接）。

你需要为你的 Mac 安装 React Native SDK。安装说明可以在[`facebook.github.io/react-native/docs/getting-started.html`](https://facebook.github.io/react-native/docs/getting-started.html)找到。安装 React Native SDK 的先决条件可以在该页面上找到。

安装 Xcode 和 Android Studio 的说明也可以在该页面上找到，用于在你的机器上安装 React Native SDK。

# 这本书面向的对象

如果你热衷于学习使用革命性的移动开发工具 React Native 来构建原生移动应用，那么这本书就是为你准备的。具备 JavaScript 的经验会有所帮助。

# 习惯用法

在这本书中，你会找到许多文本样式，用于区分不同类型的信息。以下是一些这些样式的示例及其含义的解释。

文本中的代码单词、数据库表名、文件夹名、文件名、文件扩展名、路径名、虚拟 URL、用户输入和 Twitter 昵称如下所示："基于这种布局，我们可以看到我们应用的 iOS 版本入口点是`index.ios.js`，并且生成了一个特定的`iOS`文件夹（以及相应的`Android`文件夹）。"

代码块设置如下：

```js
class Tasks extends Component { 
  render () { 
    return ( 
      <View style = {{ flex: 1, justifyContent: 'center',  
        alignItems: 'center', backgroundColor: '#F5FCFF'  
      }}> 
        <Text style = {{ fontSize: 20, textAlign:  
          'center', margin: 10 }}> 
          Welcome to React Native! 
        </Text> 
      </View> 
    ) 
  } 
}

```

任何命令行输入或输出如下所示：

```js
react-native init Tasks

```

**新术语**和**重要词汇**以粗体显示。屏幕上看到的单词，例如在菜单或对话框中，在文本中如下所示："当你打开开发者菜单时，你会看到以下选项。"

警告或重要注意事项以如下框的形式出现。

小贴士和技巧看起来像这样。

# 读者反馈

我们始终欢迎读者的反馈。告诉我们您对本书的看法——您喜欢或不喜欢什么。读者反馈对我们很重要，因为它帮助我们开发出您真正能从中获得最大收益的标题。

要发送一般反馈，请简单地将电子邮件发送到`feedback@packtpub.com`，并在邮件主题中提及本书的标题。

如果您在某个主题上具有专业知识，并且您对撰写或为本书做出贡献感兴趣，请参阅我们的作者指南[www.packtpub.com/authors](http://www.packtpub.com/authors)。

# 客户支持

现在您已经是 Packt 图书的骄傲拥有者，我们有一些东西可以帮助您从您的购买中获得最大收益。

# 下载示例代码

您可以从[`www.packtpub.com`](http://www.packtpub.com)的账户下载本书的示例代码文件。如果您在其他地方购买了本书，您可以访问[`www.packtpub.com/support`](http://www.packtpub.com/support)并注册，以便将文件直接通过电子邮件发送给您。

您可以通过以下步骤下载代码文件：

1.  使用您的电子邮件地址和密码登录或注册我们的网站。

1.  将鼠标指针悬停在顶部的“支持”标签上。

1.  点击“代码下载与勘误”。

1.  在搜索框中输入本书的名称。

1.  选择您想要下载代码文件的书籍。

1.  从下拉菜单中选择您购买本书的地方。

1.  点击“代码下载”。

文件下载完成后，请确保使用最新版本的软件解压缩或提取文件夹：

+   Windows 版的 WinRAR / 7-Zip

+   Mac 版的 Zipeg / iZip / UnRarX

+   Linux 版的 7-Zip / PeaZip

本书代码包也托管在 GitHub 上[`github.com/PacktPublishing/React-Native-By-Example`](https://github.com/PacktPublishing/React-Native-By-Example)。我们还有其他来自我们丰富图书和视频目录的代码包可供选择，请访问[`github.com/PacktPublishing/`](https://github.com/PacktPublishing/)。查看它们吧！

# 下载本书的颜色图像。

我们还为您提供了一个包含本书中使用的截图/图表的颜色图像的 PDF 文件。这些颜色图像将帮助您更好地理解输出的变化。您可以从[`www.packtpub.com/sites/default/files/downloads/ReactNativeByExample_ColorImages.pdf`](https://www.packtpub.com/sites/default/files/downloads/ReactNativeByExample_ColorImages.pdf)下载此文件。

# 勘误

尽管我们已经尽一切努力确保我们内容的准确性，但错误仍然可能发生。如果您在我们的书中发现错误——可能是文本或代码中的错误——如果您能向我们报告这一点，我们将不胜感激。通过这样做，您可以节省其他读者的挫败感，并帮助我们改进本书的后续版本。如果您发现任何勘误，请通过访问[`www.packtpub.com/submit-errata`](http://www.packtpub.com/submit-errata)，选择您的书籍，点击勘误提交表单链接，并输入您的勘误详情来报告它们。一旦您的勘误得到验证，您的提交将被接受，勘误将被上传到我们的网站或添加到该标题的勘误部分下的现有勘误列表中。

要查看之前提交的勘误，请访问[`www.packtpub.com/books/content/support`](https://www.packtpub.com/books/content/support)，并在搜索字段中输入书籍名称。所需信息将出现在勘误部分下。

# 盗版

在互联网上，版权材料盗版是一个跨所有媒体的持续问题。在 Packt，我们非常重视我们版权和许可证的保护。如果您在互联网上发现我们作品的任何非法副本，无论形式如何，请立即提供位置地址或网站名称，以便我们可以寻求补救措施。

请通过`copyright@packtpub.com`与我们联系，并附上涉嫌盗版材料的链接。

我们感谢您在保护我们作者和我们为您提供有价值内容的能力方面所提供的帮助。

# 询问

如果您对本书的任何方面有问题，您可以联系我们的`questions@packtpub.com`，我们将尽力解决问题。
