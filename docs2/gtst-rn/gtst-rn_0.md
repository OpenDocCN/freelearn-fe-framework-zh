# 前言

为什么会有这么多使用原生语言编写移动应用的替代方案？更重要的是，为什么世界还需要另一种方法？显然，肯定存在尚未解决的问题。

开发者希望只用一种语言来为 iOS 和 Android 开发。Web 开发者希望重用他们现有的 JavaScript 知识，并利用他们已经熟悉和喜爱的 Web 框架。这就是 Apache Cordova（PhoneGap）存在的原因。通过在原生应用中包装一个浏览器，开发者可以将他们的 HTML、CSS 和 JavaScript 应用程序打包在原生外壳中，但为什么不是所有移动应用都基于 Cordova 呢？

用户期望原生性能，拥有原生的用户体验。混合应用并不能解决用户的问题，它们解决的是开发者的问题。我们需要一种能够做到这两点的技术！

React Native 通过真正原生的应用改变了游戏规则。它不使用 WebView 或将 JavaScript 转换为原生语言。把它想象成由 JavaScript 大脑控制的原生 UI 组件。结果是用户体验与任何其他原生应用无法区分，同时开发者体验利用了 JavaScript 和 React 框架的惊人生产力优势。

拥有了 React Native，你终于能够在不牺牲质量或性能的情况下，利用你的 Web 开发技能在移动世界。这是圣杯，我们很兴奋地向你展示 React Native 能做什么，并看到你用它创造出多么惊人的应用！

# 这本书涵盖了什么内容

第一章，*探索示例应用程序*，是运行示例 iOS 应用程序的逐步指南。

第二章，*理解 React Native 基础知识*，涵盖了 React Native 的基础知识，并简要介绍了虚拟 DOM 如何提高性能。然后通过创建你的第一个组件介绍了 props 和 state。

第三章，*从示例应用程序开始*，从为 iOS 和 Android 生成项目文件开始。然后继续创建第一个屏幕并为应用程序添加导航。

第四章，*使用样式和布局*，涵盖了在 React Native 中布局和样式化内容的方方面面。学习如何将 React CSS 和 Flexbox 应用到你的组件中。

第五章，*显示和保存数据*，使用 ListView 显示数据，并使用 AsyncStorage API 保存笔记。

第六章，*使用地理位置和地图*，讨论了地理位置 API 和地图组件。

第七章，*集成本地模块*，专注于将 React Native 社区中的第三方本地模块集成到您的应用程序中。

第八章，*发布应用程序*，介绍了 iOS 和 Android 的发布流程，以便您准备好将应用程序提交到 AppStore 或 Google Play Store。

# 您需要本书的内容

本书所需的软件要求如下：

+   Xcode

+   命令行工具

+   npm 2.x

+   JDK

+   Android SDK

# 本书面向对象

本书面向希望学习使用他们已有的技能构建快速、美观的本地移动应用的 Web 开发者。如果您已经具备一些 JavaScript 知识或正在 Web 上使用 React，那么您将能够快速上手 React Native 用于 iOS 和 Android。

# 习惯用法

在本书中，您将找到许多文本样式，用于区分不同类型的信息。以下是一些这些样式的示例及其含义的解释。

文本中的代码单词、数据库表名、文件夹名、文件名、文件扩展名、路径名、虚拟 URL、用户输入和 Twitter 用户名如下所示：“在 Xcode 中打开`ReactNotes.xcodeproj`文件，位于`ios/`文件夹中。”

代码块如下设置：

```js
NSURL *jsCodeLocation;

/**
* Loading JavaScript code - uncomment the one you want.
*
* OPTION 1
* Load from development server. Start the server from the repository root:
*
* $ npm start
*
* To run on device, change `localhost` to the IP address of your computer
* (you can get this by typing `ifconfig` into the terminal and selecting the
* `inet` value under `en0:`) and make sure your computer and iOS device are
* on the same Wi-Fi network.
*/
```

当我们希望将您的注意力引到代码块的一个特定部分时，相关的行或项目将以粗体显示：

```js
/**
* OPTION 2
* Load from pre-bundled file on disk. To re-generate the static bundle
* from the root of your project directory, run
*
* $ react-native bundle --minify
*
* see http://facebook.github.io/react-native/docs/runningondevice.html
*/

//jsCodeLocation = [[NSBundle mainBundle] URLForResource:@"main" withExtension:@"jsbundle"];
```

任何命令行输入或输出都如下所示：

```js
# cp /usr/src/asterisk-addons/configs/cdr_mysql.conf.sample
 /etc/asterisk/cdr_mysql.conf

```

**新术语**和**重要词汇**以粗体显示。屏幕上显示的单词，例如在菜单或对话框中，以如下形式显示：“选择**运行**，然后在**信息**选项卡下将**构建配置**从**发布**更改为**调试**。”

### 注意

警告或重要注意事项以如下框中的形式出现。

### 小贴士

技巧和窍门以如下形式出现。

# 读者反馈

我们读者的反馈总是受欢迎的。请告诉我们您对本书的看法——您喜欢或不喜欢什么。读者反馈对我们来说很重要，因为它帮助我们开发出您真正能从中获得最大收益的标题。

要向我们发送一般反馈，只需发送电子邮件至`<feedback@packtpub.com>`，并在邮件主题中提及本书的标题。

如果您在某个主题上具有专业知识，并且您有兴趣撰写或为本书做出贡献，请参阅我们的作者指南[www.packtpub.com/authors](http://www.packtpub.com/authors)。

# 客户支持

现在您已经是 Packt 图书的骄傲拥有者，我们有一些事情可以帮助您从您的购买中获得最大收益。

## 下载示例代码

您可以从[`www.packtpub.com`](http://www.packtpub.com)下载您购买的所有 Packt 出版物的示例代码文件。如果您在其他地方购买了这本书，您可以访问[`www.packtpub.com/support`](http://www.packtpub.com/support)并注册，以便将文件直接通过电子邮件发送给您。

## 下载本书的彩色图像

我们还为您提供了一个包含本书中使用的截图/图表彩色图像的 PDF 文件。彩色图像将帮助您更好地理解输出的变化。您可以从[`www.packtpub.com/sites/default/files/downloads/Getting Started with React Native_ColorImages.pdf`](http://www.packtpub.com/sites/default/files/downloads/Getting Started with React Native_ColorImages.pdf)下载此文件。

## 勘误

尽管我们已经尽一切努力确保内容的准确性，但错误仍然可能发生。如果您在我们的某本书中发现错误——可能是文本或代码中的错误——如果您能向我们报告这一点，我们将不胜感激。通过这样做，您可以节省其他读者的挫败感，并帮助我们改进本书的后续版本。如果您发现任何勘误，请通过访问[`www.packtpub.com/submit-errata`](http://www.packtpub.com/submit-errata)，选择您的书籍，点击**勘误提交表单**链接，并输入您的勘误详情来报告它们。一旦您的勘误得到验证，您的提交将被接受，勘误将被上传到我们的网站或添加到该标题的勘误部分下的现有勘误列表中。

要查看之前提交的勘误，请访问[`www.packtpub.com/books/content/support`](https://www.packtpub.com/books/content/support)，并在搜索字段中输入书籍名称。所需信息将出现在**勘误**部分下。

## 盗版

互联网上对版权材料的盗版是一个跨所有媒体的持续问题。在 Packt，我们非常重视保护我们的版权和许可证。如果您在互联网上发现任何形式的我们作品的非法副本，请立即提供位置地址或网站名称，以便我们可以寻求补救措施。

请通过`<copyright@packtpub.com>`与我们联系，并提供疑似盗版材料的链接。

我们感谢您在保护我们的作者和为您提供有价值内容的能力方面的帮助。

## 问题

如果您对本书的任何方面有问题，您可以通过`<questions@packtpub.com>`联系我们，我们将尽力解决问题。
