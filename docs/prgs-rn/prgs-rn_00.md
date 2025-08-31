# 前言

React Native 框架提供了一系列强大的功能，使得在多个平台（如 iOS、Android、Linux、macOS X、Windows 和 Web）上高效构建高质量、易于维护的前端应用程序成为可能，这有助于你节省时间和金钱。

在 *专业 React Native* 一书中，你将找到对基本概念、最佳实践、高级流程以及日常开发者问题的易于使用技巧的全面覆盖。通过逐步解释、实际示例和专家指导，你将了解 React Native 在底层是如何工作的，然后利用这些知识来开发高性能的应用程序。随着你的学习，你将了解 React 和 React Native 之间的区别，导航 React Native 生态系统，并回顾创建 React Native 应用程序所需的 JavaScript 和 TypeScript 的基础知识。你还将处理动画，并通过手势控制你的应用程序。最后，你将能够通过自动化流程、测试和持续集成来构建更大的应用程序并提高开发效率。

在完成这本 React native 应用程序开发书籍之后，你将获得信心来构建适用于多个平台的高性能应用程序，甚至是在更大规模上。

# 本书面向的对象

这本书是为使用 React Native 进行开发的开发者编写的，他们有兴趣构建专业的跨平台应用程序。需要熟悉 JavaScript（包括其语法）的基础知识以及一般的软件工程概念，包括数据类型、控制流程和服务器/客户端结构。

# 本书涵盖的内容

*第一章*, *什么是 React Native?*，将包含对 React Native 的简要介绍，它如何与 React 和 Expo 相关联，以及它是如何由社区驱动的。

*第二章*, *理解 JavaScript 和 TypeScript 的基本要素*，展示了避免最常见错误和不良模式的重要基础概念。你将获得有用的提示，学习最佳实践，并重复使用 JavaScript 在应用程序中的最重要的基础知识。

*第三章*, *Hello React Native*，将帮助你更深入地了解 React Native。它包含了一个示例应用程序中的核心概念解释，以及关于 React Native 架构的理论信息以及如何将不同平台连接到 React Native JavaScript 包。

*第四章*, *React Native 中的样式、存储和导航*，涵盖了不同的领域，这些领域对于使用 React Native 创建高质量产品都至关重要。你必须关注良好的用户体验，这包括良好的设计和清晰的导航。此外，你的用户应该能够在没有网络连接的情况下尽可能多地使用你的应用程序，这意味着需要处理本地存储的数据。

*第五章*，*管理状态和连接后端*，大量关注数据。首先，您将学习如何在您的应用中处理更复杂的数据。然后，我们将探讨如何通过连接远程后端使您的应用与世界其他部分进行通信的不同选项。

*第六章*，*与动画一起工作*，专注于屏幕动画。在 React Native 中实现平滑动画有多种方法。根据您要构建的项目类型和动画，您可以从多种解决方案中选择，每种解决方案都有其自身的优缺点。我们将在本章中讨论最佳和最广泛使用的解决方案。

*第七章*，*在 React Native 中处理手势*，教您如何处理手势，如何结合手势和动画，以及提供用户反馈的最佳实践。

*第八章*，*JavaScript 引擎和 Hermes*，主要是一个理论章节，您将学习 React Native 中不同的 JavaScript 引擎是如何工作的，以及为什么 Hermes 是生产应用中首选的解决方案（当可以使用时）。它包括一些理论背景以及在不同环境中的关键指标测试。

*第九章*，*提高 React Native 开发效率的必备工具*，教您关于使开发更轻松的有用工具，尤其是在处理大型项目时。您将了解 Storybook 是如何工作的，以及为什么这是一个 React Native 开发的绝佳工具。您还将学习关于 React Native 的样式组件，不同 UI 库的建议，ESLint/TSLint，以及如 Ignite 之类的样板 CLI。

*第十章*，*构建大规模、多平台项目结构*，教您如何构建大规模项目。这包括应用架构、多个开发者成功协作的流程，以及确保良好代码质量的流程。

*第十一章*，*创建和自动化工作流程*，专注于工作流程自动化。您将学习如何设置多个 CI 管道进行代码质量检查、自动化的 PR 检查、通过邮件、Slack 或板问题进行自动化的通信，以及将应用部署到应用商店。我们将探讨 GitHub Actions、fastlane、Bitrise 和其他 CI/CD 解决方案。

*第十二章*，*React Native 应用的自动化测试*，教您如何使用 Jest 和 react-native-testing-library 进行单元和快照测试，如何确保一定的测试覆盖率，如何使用 Detox 进行端到端测试，甚至如何使用 AWS Device Farm 和 Appium 在真实设备上进行测试。

*第十三章*，*小贴士与展望*分为两部分。在第一部分，您可以阅读我关于如何使您的 React Native 项目成功的最有用的技巧。第二部分侧重于框架的展望以及我认为 React Native、其社区及其生态系统将如何在未来发展。这基于技术发展以及社区中不同大玩家的承诺。

# 为了充分利用这本书

您应该有一个可工作的 React Native 环境，以便能够运行本书中的示例。所有示例都使用 React Native 0.68 进行测试，但它们也应该与未来的版本兼容。

| **本书涵盖的软件** | **操作系统要求** |
| --- | --- |
| React Native 0.68 | Windows、macOS 或 Linux，最好是 macOS |
| TypeScript 4.4 |  |
| ECMAScript 12 |  |

**如果您正在使用这本书的数字版，我们建议您亲自输入代码或从书的 GitHub 仓库（下一节中有一个链接）获取代码。这样做将帮助您避免与代码复制粘贴相关的任何潜在错误。**

# 下载示例代码文件

您可以从 GitHub 下载本书的示例代码文件[`github.com/alexkuttig/prn-videoexample`](https://github.com/alexkuttig/prn-videoexample)。如果代码有更新，它将在 GitHub 仓库中更新。

我们还有其他来自我们丰富的图书和视频目录的代码包，可在[`github.com/PacktPublishing/`](https://github.com/PacktPublishing/)找到。查看它们！

# 下载彩色图像

我们还提供了一份包含本书中使用的截图和图表彩色图像的 PDF 文件。您可以从这里下载：[`packt.link/xPgoW`](https://packt.link/xPgoW)。

# 使用的约定

本书使用了多种文本约定。

`文本中的代码`：表示文本中的代码单词、数据库表名、文件夹名、文件名、文件扩展名、路径名、虚拟 URL、用户输入和 Twitter 昵称。以下是一个示例：“这是上一章示例项目中来自`<Header />`组件，但使用内联样式来设置`Text`组件的样式。”

代码块设置如下：

```js
import React from 'react'; 
import {ScrollView, Text, View} from 'react-native'; 
const App = () => { 
  return ( 
    <ScrollView contentInsetAdjustmentBehavior="automatic"> 
      <View> 
        <Text>Hello World!</Text> 
      </View> 
    </ScrollView> 
  ); 
};
```

当我们希望您注意代码块中的特定部分时，相关的行或项目将以粗体显示：

```js
<Pressable 
  key={genre.name} 
onPress={() => props.onGenrePress(genre)} 
testID={'test' + genre.name}> 
<Text style={styles.genreTitle}>{genre.name}</Text> 
</Pressable>
```

任何命令行输入或输出应按以下方式编写：

```js
npx react-native init videoexample  
    --template react-native-template-typescript
```

**粗体**：表示新术语、重要单词或屏幕上看到的单词。例如，菜单或对话框中的单词以粗体显示。以下是一个示例：“转到**设置**，滚动到页面底部，并选择**开发者**。”

小贴士或重要注意事项

看起来像这样。

# 联系我们

我们欢迎读者的反馈。

**一般反馈**：如果您对本书的任何方面有疑问，请通过电子邮件发送至 customercare@packtpub.com，并在邮件主题中提及书名。

**勘误**: 尽管我们已经尽最大努力确保内容的准确性，但错误仍然可能发生。如果您在这本书中发现了错误，如果您能向我们报告，我们将不胜感激。请访问 [www.packtpub.com/support/errata](http://www.packtpub.com/support/errata) 并填写表格。

**盗版**: 如果您在互联网上发现我们作品的任何非法副本，如果您能提供位置地址或网站名称，我们将不胜感激。请通过 copyright@packt.com 与我们联系，并提供材料的链接。

**如果您有兴趣成为作者**: 如果您在某个领域有专业知识，并且您有兴趣撰写或为书籍做出贡献，请访问 [authors.packtpub.com](http://authors.packtpub.com)。

# 分享您的想法

读完 *Professional React Native* 后，我们很乐意听听您的想法！请选择 [`www.amazon.in/review/create-review/error?asin=180056368X`](https://www.amazon.in/review/create-review/error?asin=180056368X) 为这本书提供反馈。

您的评论对我们和科技社区都至关重要，并将帮助我们确保我们提供高质量的内容。

# 第一部分：React Native 入门

本模块主要是帮助您达到理解更高级模块（2 和 3）所需的基本知识水平，即 React 和 React Native。阅读后，您将了解基于 React 的现代客户端开发是如何工作的，以及 React、React Native 和 Expo 之间的区别。

以下章节属于本节：

+   *第一章*, *什么是 React Native?*

+   *第二章*, *理解 JavaScript 和 TypeScript 的基本要素*

+   *第三章*, *Hello React Native*
