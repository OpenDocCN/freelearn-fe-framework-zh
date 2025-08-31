# 前言

几年来，**React**和**React Native**在 JavaScript 开发者中已被证明是全面且实用的 React 生态系统指南的热门选择。第五版包含了 React 的最新功能、增强和修复，同时与 React Native 兼容。它包括新的章节，涵盖了使用 React 和**TypeScript**进行现代跨平台应用开发的关键特性和概念。

从 React 的基础知识到流行的特性，如**Hooks**、服务器端渲染和单元测试，这本权威指南将按步骤帮助你成为一名专业的 React 开发者。

你将从学习 React 组件的基本构建块开始。接下来，你将学习如何使用 TypeScript 提高组件的稳定性。随着你通过章节的深入，你将在应用开发中使用更高级的功能，然后通过为 Web 和原生平台开发用户界面组件来应用你的知识。

在本书结束时，你将能够自信地构建 Web 上的 React 应用和多个平台上的 React Native 应用：Web、移动和桌面。

# 这本书面向谁

这本书适合任何想要开始学习如何使用 React 和 React Native 进行移动和 Web 应用开发的 JavaScript 开发者。不需要具备 React 的先验知识；然而，为了能够跟上本书涵盖的内容，需要具备 JavaScript、HTML 和 CSS 的实际操作能力。

# 这本书涵盖的内容

**第一章**，*为什么选择 React？*，描述了 React 是什么以及为什么你想用它来构建你的应用。

**第二章**，*使用 JSX 进行渲染*，介绍了 React 组件使用的标记语言 JSX 的基础知识。

**第三章**，*理解 React 组件和 Hooks*，介绍了 React 应用中组件和 Hooks 的核心机制。

**第四章**，*React 方式的事件处理*，概述了 React 组件如何处理事件。

**第五章**，*构建可重用组件*，通过示例指导你进行组件重构的过程。

**第六章**，*使用 TypeScript 进行类型检查和验证*，描述了 React 组件经历的各个阶段以及为什么这对 React 开发者来说很重要。

**第七章**，*使用 Routes 处理导航*，提供了大量如何为你的 React Web 应用设置路由的示例。

**第八章**，*使用懒加载组件和 Suspense 进行代码拆分*，介绍了导致性能更优、更高效的应用的代码拆分技术。

**第九章**，*用户界面框架组件*，概述了如何开始使用 MUI，这是一个用于构建 UI 的 React 组件库。

**第十章**，*高性能状态更新*，深入探讨了 React 中允许进行高效状态更新和高性能应用的新特性。

第十一章，*从服务器获取数据*，讨论了我们可以使用各种方式从服务器检索数据。

第十二章，*React 中的状态管理*，涵盖了使用 Redux 和 Mobx 等流行解决方案在应用中管理状态。

第十三章，*服务器端渲染*，教你如何使用 Next.js 构建在服务器和客户端上渲染内容的庞大 React 应用程序。

第十四章，*React 中的单元测试*，概述了使用 Vitest 进行单元测试的软件测试。

第十五章，*为什么选择 React Native*？，描述了 React Native 库是什么以及与原生移动开发的区别。

第十六章，*React Native 内部机制*，概述了 React Native 的架构。

第十七章，*启动 React Native 项目*，教你如何开始一个新的 React Native 项目。

第十八章，*使用 Flexbox 构建响应式布局*，描述了如何创建布局并添加样式。

第十九章，*屏幕间导航*，展示了在应用中切换屏幕的方法。

第二十章，*实现数据列表*，描述了如何在应用中实现数据列表。

第二十一章，*地理位置和地图*，解释了如何在应用中跟踪地理位置并添加地图。

第二十二章，*收集用户输入*，教你如何创建表单。

第二十三章，*响应用户手势*，提供了处理用户手势的示例。

第二十四章，*显示进度*，展示了如何处理进程指示器和进度条。

第二十五章，*显示模态屏幕*，教你如何创建对话框模态。

第二十六章，*使用动画*，描述了如何在应用中实现动画。

第二十七章，*控制图像显示*，概述了如何在 React Native 应用中渲染图像。

第二十八章，*离线使用*，展示了当手机没有互联网连接时如何处理应用。

# 为了充分利用这本书

本书假设你已对 JavaScript 编程语言有基本了解。它还假设你会跟随示例进行操作，这需要命令行终端、代码编辑器和网络浏览器。你将在第一章，*为什么选择 React*？中学习如何设置 React 项目。

学习 React Native 的要求与 React 开发相同，但要在真实设备上运行应用，你需要一部 Android 或 iOS 智能手机。为了在模拟器中运行 iOS 应用，你需要一台 Mac 电脑。要与 Android 模拟器一起工作，你可以使用任何类型的 PC。

## 下载示例代码文件

该书的代码包托管在 GitHub 上，网址为[`github.com/PacktPublishing/React-and-React-Native-5E`](https://github.com/PacktPublishing/React-and-React-Native-5E)。我们还有其他来自我们丰富的图书和视频目录的代码包，可在[`github.com/PacktPublishing/`](https://github.com/PacktPublishing/)找到。去看看吧！

## 下载彩色图片

我们还提供了一个包含本书中使用的截图/图表彩色图像的 PDF 文件。您可以从这里下载：[`packt.link/gbp/9781805127307`](https://packt.link/gbp/9781805127307)。

## 使用的约定

本书中使用了多种文本约定。

`CodeInText`：表示文本中的代码单词、数据库表名、文件夹名、文件名、文件扩展名、路径名、虚拟 URL、用户输入和 Twitter 昵称。例如：“您实际声明的路由作为`<Route>`元素。”

代码块设置如下：

```js
export default function First() {
  return <p>Feature 1, page 1</p>;
} 
```

当我们希望您注意代码块中的特定部分时，相关的行或项目将以粗体显示：

```js
export default function List({ data, fetchItems, refreshItems,
isRefreshing }) {
  return (
    <FlatList
data={data}
**renderItem****=****{({****item** **}) =>****<****Text****style****=****{styles.**
**item****}>****{item.value}****</****Text****>****}**
onEndReached={fetchItems} onRefresh={refreshItems} refreshing={isRefreshing}
/> );
} 
```

任何命令行输入或输出都按以下方式编写：

```js
npm install @react-navigation/bottom-tabs @react-navigation/
drawer 
```

**粗体**：表示新术语、重要单词或您在屏幕上看到的单词。例如，菜单或对话框中的单词在文本中显示如下。例如：“从**管理**面板中选择**系统信息**。”

警告或重要提示看起来是这样的。

小贴士和技巧看起来是这样的。

# 联系我们

我们始终欢迎读者的反馈。

**一般反馈**：请通过`feedback@packtpub.com`发送电子邮件，并在邮件主题中提及书籍的标题。如果您对本书的任何方面有疑问，请通过`questions@packtpub.com`发送电子邮件给我们。

**勘误表**：尽管我们已经尽最大努力确保内容的准确性，但错误仍然可能发生。如果您在这本书中发现了错误，如果您能向我们报告，我们将不胜感激。请访问[`www.packtpub.com/submit-errata`](http://www.packtpub.com/submit-errata)，点击**提交勘误表**，并填写表格。

**盗版**：如果您在互联网上以任何形式遇到我们作品的非法副本，如果您能提供位置地址或网站名称，我们将不胜感激。请通过`copyright@packtpub.com`与我们联系，并提供材料的链接。

**如果您有兴趣成为作者**：如果您在某个主题上具有专业知识，并且您有兴趣撰写或为书籍做出贡献，请访问[`authors.packtpub.com`](http://authors.packtpub.com)。

# 分享您的想法

一旦您阅读了*React 和 React Native，第五版*，我们很乐意听到您的想法！请[点击此处直接进入此书的亚马逊评论页面](https://packt.link/r/1805127306)并分享您的反馈。

您的评论对我们和科技社区都至关重要，并将帮助我们确保我们提供高质量的内容。

# 下载此书的免费 PDF 副本

感谢您购买此书！

您喜欢在路上阅读，但无法携带您的印刷书籍到处走？

您的电子书购买是否与您选择的设备不兼容？

别担心，现在，随着每本 Packt 书籍，您都可以免费获得该书的 DRM 免费 PDF 版本。

在任何地方、任何设备上阅读。直接从您喜欢的技术书籍中搜索、复制和粘贴代码到您的应用程序中。

优惠远不止于此，您将获得独家折扣、新闻通讯以及每天收件箱中的优质免费内容。

按照以下简单步骤获取福利：

1.  扫描下面的二维码或访问以下链接：

![](img/B19636_Free_PDF_QR.png)

[`packt.link/free-ebook/9781805127307`](https://packt.link/free-ebook/9781805127307)

1.  提交您的购买证明。

1.  就这样！我们将直接将您的免费 PDF 和其他福利发送到您的电子邮件。
