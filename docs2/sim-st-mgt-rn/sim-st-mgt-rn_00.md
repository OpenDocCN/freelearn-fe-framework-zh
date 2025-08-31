# 前言

欢迎来到 React Native 应用的奇妙世界！感谢这项技术，你可以在几分钟内拥有自己的原生应用并投入使用。如果你是第一次听说 React Native，不必担心。基本的 JavaScript 知识就足以让你迅速上手。我们将一起学习 React 和 React Native 的重要概念，并了解 React Native 生态系统、设置和工具。到*第四章*，*为 Funbook 应用添加样式和内容*，结束时你将手握一个完全功能化的社交媒体克隆应用。

这里开始变得有趣起来。我们的应用有几个 API 端点，需要在多个组件和屏幕之间管理数据对象。这种情况在中型和大型应用中非常常见。正因为如此，针对这个常见问题有许多解决方案。许多开发者使用经过实战检验且广受赞誉的开源库，如 Redux 或 MobX。其他人寻求创新的想法，并在他们的项目中选择了 XState 或 Jotai。还有一些人仍然使用内置的 React 功能，或者使用 React Query 专注于数据获取而不是状态管理。在这本书中，我们将站在所有这些类型开发者的角度。我们将从应用中挑选一个特定的功能——带有喜欢图片列表的点赞按钮——并逐一尝试这里列出的开源库。

当我们在本书结束时取得胜利，你将对在 React Native 应用中管理状态的不同方式有很好的理解。我希望你也会有一个自己的想法，以及为什么。Redux、MobX、XState、Jotai 和 React Query 都是为了解决相同的问题而创建的，但它们的创造者采取了非常不同的方法。我也希望你会像我写作这本书一样喜欢这本书。

# 本书面向的对象

这本书是为 React 和 React Native 世界的初学者准备的。它涵盖了与 ReactJS 软件开发相关的基本主题。即使你对基本的 React 解决方案有所了解，你也许对 MobX、XState、Jotai 或 React Query 还是新手，这意味着这本书同样适合你。

# 本书涵盖的内容

*第一章*，*什么是 React 和 React Native？*，将从简要回顾网络开发的历史开始，以便更好地理解 React 和 React Native 背后的理念。我们还将讨论 ReactJS 的概念，并熟悉 React Native 代码。

*第二章*，*在简单的 React 应用中管理状态*，将讨论 React 开发者面临的某些现实问题。我们将专注于中等和大型应用的稳健状态管理。由于 React 本身并没有创建用于管理全局状态的工具，我们将探讨现代 React 解决方案和其他状态管理策略。

*第三章*，*规划和设置 Funbook 应用*，将真正开始编码！我们将创建我们自己的应用，一个社交媒体克隆应用，名为 Funbook。我们将了解流行的工具，特别是 Expo，以及 React Native 生态系统。

*第四章*，*为 Funbook 应用进行样式设计和数据填充*，将专注于使我们在手中的应用看起来更好。我们还将填充一些数据，以便我们可以工作在一个接近真实生产应用的项目上。

*第五章*，*在我们的 Funbook 应用中实现 Redux*，将探讨 Redux 动荡的历史，然后介绍如何在应用中配置 Redux 和 Redux Toolkit。一旦设置好依赖项，我们将继续使用 Redux 来实现喜欢按钮和喜欢图片列表。本章包括我与主要 Redux 和 Redux Toolkit 维护者——Mark Erikson（也以 Twitter 昵称`@acemarke`知名）的简短对话回复。

*第六章*，*在 React Native 应用中使用 MobX 作为状态管理器*，将回到我们在*第一章*到*第四章*中创建的裸 React Native 应用，这次，我们将添加 MobX 和 MobX-State-Tree。我们将从了解这个库是如何产生的开始，然后继续在 Funbook 应用中配置它。一旦我们准备好了，我们将用它来处理喜欢图片列表和喜欢按钮。本章包括我与 MobX-State-Tree 维护者——Jamon Holmgren 的交流回复。

*第七章*，*使用 XState 在 React Native 应用中解开复杂流程*，将深入探讨一些高级数学问题，因为 XState 基于高级数学概念。当我们掌握了这些概念后，我们将继续在 Funbook 应用中配置 XState，并用于喜欢图片功能。

本章包括我从 XState 的创建者——David Khourshid（在互联网上更知名为 DavidKPiano）那里收到的回复。

*第八章*，*在 React Native 应用中集成 Jotai*，将再次回到裸 Funbook 应用，这次，我们将实现本书中最新状态管理库：Jotai。我们将了解其概念，配置它，并用于喜欢按钮和喜欢图片列表功能。本章包括我与 Jotai 创建者——Daishi Kato 的对话回复。

*第九章*，*使用 React Query 进行服务器端驱动状态管理*，将以全新的方式探讨状态管理问题：也许我们根本不需要状态管理库。也许我们唯一需要做的就是有效地管理数据获取。为了测试这个假设，我们将安装、配置和使用 React Query，也称为 TanStack Query。

*第十章*，*附录*，将全面回顾本书中我们所学到的所有内容。我还包括了一些与 React Native 应用状态管理主题相关的常见面试问题。

# 为了充分利用本书

您需要在您的计算机上安装 Expo。所有代码示例都已使用 macOS 上的 Expo 44 进行测试，但它们应该与未来的版本发布兼容。

| **本书中涵盖的软件/硬件** | **操作系统要求** |
| --- | --- |
| Expo 44 | Windows、macOS 或 Linux |
| JavaScript (ECMAScript 2020) | Windows、macOS 或 Linux |
| ReactJS v18 及以上 | Windows、macOS 或 Linux |
| React Native | Windows、macOS 或 Linux |

在*第三章*，*规划和设置 Funbook 应用*中详细介绍了额外的设置说明。

**如果您正在使用本书的数字版，我们建议您亲自输入代码或从本书的 GitHub 仓库（下一节中有一个链接）获取代码。这样做将有助于避免与代码的复制和粘贴相关的任何潜在错误。**

本书代表了 2022 年最知名的状态管理库的状态。我鼓励您尝试一些新的、不太为人所知的解决方案，因为每天都有新的库发布。

# 下载示例代码文件

您可以从 GitHub 在[`github.com/PacktPublishing/Simplifying-State-Management-in-React-Native`](https://github.com/PacktPublishing/Simplifying-State-Management-in-React-Native)下载本书的示例代码文件。如果代码有更新，它将在 GitHub 仓库中更新。

我们还提供了来自我们丰富的书籍和视频目录中的其他代码包，可在[`github.com/PacktPublishing/`](https://github.com/PacktPublishing/)找到。查看它们吧！

# 下载彩色图像

我们还提供了一份包含本书中使用的截图和图表的彩色 PDF 文件。您可以从这里下载：[`packt.link/wv4Mk`](https://packt.link/wv4Mk)。

# 使用的约定

本书中使用了多种文本约定。

`文本中的代码`：表示文本中的代码单词、数据库表名、文件夹名、文件名、文件扩展名、路径名、虚拟 URL、用户输入和 Twitter 昵称。以下是一个示例：“`export const ListOfAvatars = () => {`”。

代码块设置如下：

```js
Import { Text } from 'react-native';
const Welcome = () => {
  return <Text>Hello, World! </Text>;
}
```

当我们希望将您的注意力引向代码块的一个特定部分时，相关的行或项目将以粗体显示：

```js
return (
    <View style={{ paddingTop: 30 }}>
      <FlatList
        data={arrayOfAvatars}
        renderItem={renderItem}
        keyExtractor={(item) => item.id}
      />
    </View>
```

任何命令行输入或输出都按照以下方式编写：

```js
$ yarn add react-query
$ expo start
```

**粗体**：表示新术语、重要单词或您在屏幕上看到的单词。例如，菜单或对话框中的单词以**粗体**显示。以下是一个示例：“如果您想在手机上查看您的应用，您可以在“Expo Go 应用”中找到扫描的**二维码**，就在这里。”

浏览示例数据

您可以在任何时候查看应用中使用的示例数据。

# 联系我们

我们始终欢迎读者的反馈。

**一般反馈**：如果您对本书的任何方面有疑问，请通过[customercare@packtpub.com](http://customercare@packtpub.com)给我们发邮件，并在邮件主题中提及书名。

**勘误表**：尽管我们已经尽最大努力确保内容的准确性，但错误仍然可能发生。如果您在这本书中发现了错误，如果您能向我们报告，我们将不胜感激。请访问[www.packtpub.com/support/errata](http://www.packtpub.com/support/errata)并填写表格。

**盗版**：如果您在互联网上以任何形式发现我们作品的非法副本，如果您能向我们提供位置地址或网站名称，我们将不胜感激。请通过[copyright@packt.com](http://copyright@packt.com)与我们联系，并提供材料的链接。

**如果您有兴趣成为作者**：如果您在某个主题上具有专业知识，并且您有兴趣撰写或为书籍做出贡献，请访问[authors.packtpub.com](http://authors.packtpub.com)。

# 分享您的想法

一旦您阅读了*Simplifying State Management in React Native*，我们非常乐意听到您的想法！[请点击此处直接进入此书的亚马逊评论页面](https://packt.link/r/1-803-23503-9)并分享您的反馈。

您的评论对我们和科技社区都很重要，并将帮助我们确保我们提供高质量的内容。

# 下载此书的免费 PDF 副本

感谢您购买此书！

您喜欢在移动中阅读，但又无法随身携带您的印刷书籍吗？

您的电子书购买是否与您选择的设备不兼容？

请放心，现在购买每一本 Packt 书籍，您都可以免费获得该书的 DRM 免费 PDF 版本。

在任何地方、任何时间、任何设备上阅读。直接从您最喜欢的技术书籍中搜索、复制和粘贴代码到您的应用程序中。

优惠不仅限于此，您还可以获得独家折扣、时事通讯和每日免费内容的访问权限。

按照以下简单步骤获取好处：

1.  扫描下面的二维码或访问以下链接

![](img/B18396_QR_Free_PDF.jpg)

[`packt.link/free-ebook/978-1-80323-503-5`](https://packt.link/free-ebook/978-1-80323-503-5)

1.  提交您的购买证明

1.  就这些！我们将直接将您的免费 PDF 和其他优惠发送到您的电子邮件。

# 第一部分 – 学习基础知识：React、状态、属性、钩子和上下文简介

在这部分，我们将从一些对创建真实应用至关重要的理论知识开始。读者将了解 React 的一些历史以及其一般规则、指南和最佳实践。接下来，我们将探讨 React 处理状态的内置策略：本地状态、props、hooks 和 context。

本部分包括以下章节：

+   *第一章*, *什么是 React 和 React Native？*

+   *第二章*, *在简单的 React 应用中管理状态*
