# 前言

NativeScript 是由 Progress 构建的一个开源框架，用于使用 Angular、TypeScript，甚至是古老的纯 JavaScript 构建真正的原生移动应用。Angular 也是一个由 Google 构建的开源框架，它提供了声明式模板、依赖注入和丰富的模块来构建应用程序。Angular 的多功能视图处理架构允许您的视图以真实的原生 UI 组件的形式渲染——对 iOS 或 Android 来说是原生的——这些组件提供了卓越的性能和流畅的可用性。Angular 视图渲染层的解耦，结合 NativeScript 中原生 API 的力量，共同创造了激动人心的 NativeScript for Angular 的世界。

本书重点介绍您需要了解的关键概念，以便在 iOS 和 Android 上为您的 Angular 移动应用构建 NativeScript。我们将构建一个有趣的多人录音室应用，涉及您在开始构建自己的应用时需要了解的强大原生关键概念。拥有正确的结构对于开发可扩展、高度可维护和可移植的应用至关重要，因此我们将从使用 Angular 的@NgModule 进行项目组织开始。我们将使用 Angular 组件来构建我们的第一个视图，然后创建我们可以通过 Angular 的依赖注入使用的服务。

您将了解 NativeScript 的 tns 命令行工具，用于在 iOS 和 Android 上运行应用。我们将集成第三方插件来构建一些核心功能。接下来，我们将集成@ngrx store 和 effects，建立一些稳固的实践（受 Redux 启发）来处理状态管理。如果应用看起来不好或用户体验不佳，那么拥有出色的数据流和稳固的架构就没有意义，因此我们将使用 SASS 为我们的应用打磨一个样式。之后，我们将处理调试问题，并投入一些时间编写测试，以防止未来出现错误。最后，我们将使用 webpack 打包我们的应用，并将其部署到苹果应用商店和谷歌 Play。

到本书结束时，您将了解构建 NativeScript for Angular 应用所需的大部分关键概念。

# 本书涵盖的内容

第一章，*使用@NgModule 进入形状*，讨论了@NgModule 装饰器，它明确定义了您应用中的一个功能段。这将是您项目的组织单元。在您开始构建应用之前，花点时间思考您可能需要或想要的应用的各种单元/部分/模块，这将带来许多好处。

第二章, *功能模块*，教您如何使用功能模块来构建您的应用，这将为您未来的维护带来许多优势，并减少整个应用中的代码重复。

第三章, *通过组件构建我们的第一个视图*，实际上让我们第一次看到了我们的应用，在这里我们需要为我们的第一个视图构建一个组件。

第四章, *使用 CSS 打造更美观的视图*，探讨了如何通过几个 CSS 类将我们的第一个视图转变为令人惊叹的美丽，同时也会关注如何利用 NativeScript 的核心主题提供一个一致的样式框架来构建。

第五章, *路由和懒加载*，允许用户在应用中的各种视图之间导航，这将需要设置路由。Angular 提供了一个强大的路由器，当与 NativeScript 结合使用时，与 iOS 和 Android 上的原生移动页面导航系统协同工作。此外，我们还将设置各种路由的懒加载，以确保我们的应用启动时间尽可能快。

第六章, *在 iOS 和 Android 上运行应用*，重点关注如何通过 NativeScript 的 tns 命令行工具在 iOS 和 Android 上运行我们的应用。

第七章, *构建多轨播放器*，涵盖了插件集成，并通过 NativeScript 直接访问 iOS 上的 Objective C/Swift API 和 Android 上的 Java API。

第八章, *构建音频录音机*，与原生 API 合作构建 iOS 和 Android 的音频录音机。

第九章, *赋予您的视图力量*，利用 Angular 的灵活性和 NativeScript 的强大功能，以充分利用您应用的用户界面。

第十章, *@ngrx/store + @ngrx/effects 用于状态管理*，通过 ngrx 的单个存储管理应用状态。

第十一章, *使用 SASS 进行润色*，集成 nativescript-dev-sass 插件，以 SASS 润色我们的应用样式。

第十二章, *单元测试*，设置 Karma 单元测试框架，以确保我们的应用具有未来性。

第十三章, *使用 Appium 进行集成测试*，设置 Appium 进行集成测试。

第十四章, *使用 webpack 打包进行部署准备*，与 webpack 合作以优化发布时的包。

第十五章, *部署到 Apple App Store*，让我们通过 Apple App Store 分发我们的应用。

第十六章, *部署到 Google Play*，让我们通过 Google Play 分发我们的应用。

# 您需要为此书准备的内容

本书假设您正在使用 NativeScript 3 或更高版本和 Angular 4.1 或更高版本。如果您计划进行 iOS 开发，您需要一个安装了 XCode 的 Mac 来运行配套的应用程序。您还应安装至少一个模拟器（最好运行 7.0.0 版本，API 24 或更高版本）的 Android SDK 工具。

# 这本书适合谁阅读

本书面向所有对 iOS 和 Android 移动应用开发感兴趣的软件开发人员。它专门针对那些已经对 TypeScript 有一般了解以及一些基本 Angular 功能的用户。刚开始接触 iOS 和 Android 移动应用开发的 Web 开发者也可能从本书的内容中获得很多收益。

# 约定

在这本书中，您将找到多种文本样式，用于区分不同类型的信息。以下是一些这些样式的示例及其含义的解释。

文本中的代码单词、数据库表名、文件夹名、文件名、文件扩展名、路径名、虚拟 URL、用户输入和 Twitter 昵称如下所示：“支持各种常见属性（`padding`、`font size`、`font weight`、`color`、`background color`等）。此外，缩写边距/填充也有效，即 padding: 15 5。”

代码块应如下设置：

```js
[default]
export class AppComponent {}
```

当我们希望引起您对代码块中特定部分的注意时，相关的行或项目将以粗体显示：

```js
[default]
public init() {
 const item = {};
 item.volume = 1; }
```

任何命令行输入或输出都应如下编写：

```js
 # tns run ios --emulator
```

**新术语**和**重要词汇**以粗体显示。屏幕上看到的单词，例如在菜单或对话框中，在文本中如下所示：“再次运行我们的应用，我们现在在点击 Record 按钮时看到登录提示”。

警告或重要注意事项如下所示。

技巧和窍门如下所示。

# 读者反馈

我们欢迎读者的反馈。请告诉我们您对这本书的看法——您喜欢或不喜欢的地方。读者的反馈对我们来说很重要，因为它帮助我们开发出您真正能从中受益的标题。要发送一般反馈，请简单地发送电子邮件至`feedback@packtpub.com`，并在邮件主题中提及书的标题。如果您在某个主题领域有专业知识，并且您对撰写或为书籍做出贡献感兴趣，请参阅我们的作者指南[www.packtpub.com/authors](http://www.packtpub.com/authors)。

# 客户支持

现在，您已经成为 Packt 图书的骄傲拥有者，我们有一些事情可以帮助您充分利用您的购买。

# 下载示例代码

您可以从[`www.packtpub.com`](http://www.packtpub.com)的账户下载这本书的示例代码文件。如果您在其他地方购买了这本书，您可以访问[`www.packtpub.com/support`](http://www.packtpub.com/support)并注册，以便将文件直接通过电子邮件发送给您。您可以通过以下步骤下载代码文件：

1.  使用您的电子邮件地址和密码登录或注册我们的网站。

1.  将鼠标指针悬停在顶部的 SUPPORT 标签上。

1.  点击代码下载与勘误。

1.  在搜索框中输入书籍名称。

1.  选择您想要下载代码文件的书籍。

1.  从您购买此书的下拉菜单中选择。

1.  点击代码下载。

一旦文件下载完成，请确保使用最新版本的以下软件解压或提取文件夹：

+   WinRAR / 7-Zip for Windows

+   Zipeg / iZip / UnRarX for Mac

+   7-Zip / PeaZip for Linux

本书代码包也托管在 GitHub 上，网址为[`github.com/PacktPublishing/NativeScript-for-Angular-Mobile-Development`](https://github.com/PacktPublishing/NativeScript-for-Angular-Mobile-Development)。我们还有其他来自我们丰富图书和视频目录的代码包可供在[`github.com/PacktPublishing/`](https://github.com/PacktPublishing/)找到。查看它们吧！

# 下载本书的颜色图像

我们还为您提供了一个包含本书中使用的截图/图表颜色图像的 PDF 文件。这些颜色图像将帮助您更好地理解输出的变化。您可以从[`www.packtpub.com/sites/default/files/downloads/NativeScriptforAngularMobileDevelopment_ColorImages.pdf`](https://www.packtpub.com/sites/default/files/downloads/NativeScriptforAngularMobileDevelopment_ColorImages.pdf)下载此文件。

# 勘误

尽管我们已经尽最大努力确保内容的准确性，但错误仍然可能发生。如果您在我们的书籍中找到错误——可能是文本或代码中的错误——如果您能向我们报告这一点，我们将不胜感激。通过这样做，您可以避免其他读者的挫败感，并帮助我们改进本书的后续版本。如果您发现任何勘误，请通过访问[`www.packtpub.com/submit-errata`](http://www.packtpub.com/submit-errata)，选择您的书籍，点击勘误提交表单链接，并输入您的勘误详情来报告它们。一旦您的勘误得到验证，您的提交将被接受，勘误将被上传到我们的网站或添加到该标题的勘误部分下的现有勘误列表中。要查看之前提交的勘误，请访问[`www.packtpub.com/books/content/support`](https://www.packtpub.com/books/content/support)，并在搜索字段中输入书籍名称。所需信息将出现在勘误部分下。

# 海盗行为

在互联网上，版权材料的盗版问题是一个持续存在的问题，涉及所有媒体。在 Packt，我们非常重视我们版权和许可证的保护。如果您在网上遇到任何形式的我们作品的非法副本，请立即向我们提供位置地址或网站名称，以便我们可以寻求补救措施。请通过发送链接到`copyright@packtpub.com`与我们联系，以提供疑似盗版材料。我们感谢您的帮助，以保护我们的作者和向您提供有价值内容的能力。

# 问题

如果您在这本书的任何方面遇到问题，您可以联系我们的`questions@packtpub.com`，我们将尽力解决问题。
