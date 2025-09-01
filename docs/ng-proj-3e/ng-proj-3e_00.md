# 前言

Angular 是一个流行的 JavaScript 框架，可以在包括 Web、桌面和移动在内的广泛平台上运行。它提供了一系列丰富的功能，并拥有广泛的工具，使其在开发者中非常受欢迎。本更新的第三版 Angular Projects 将教你如何使用 Angular 构建高效和优化的 Web 应用程序。

你将从创建 10 个不同的真实世界 Web 应用程序开始，探索框架的基本特性。每个应用程序都将展示如何将 Angular 与不同的库和工具集成。随着你的进步，你将学习如何在构建问题跟踪系统、PWA 天气应用程序、移动照片地理标记应用程序、组件 UI 库等多个令人兴奋的项目的同时，实现流行的技术，如 Angular Router、Scully、Electron、Angular 的服务工作者、Nx 的单仓库工具、NgRx 等。在结论章节中，你将学会使用 schematics 自定义 Angular CLI 命令。

在本书结束时，你将具备根据你或你的客户需求使用各种不同技术构建 Angular 应用程序所需的技能。

# 本书面向对象

如果你是一位具有 Angular 入门级经验的开发者，并且希望熟练掌握处理 Angular 可能遇到的各种用例的基本工具，那么这本 Angular 开发书籍就是为你准备的。假设你具备 Web 应用程序开发的基础知识，以及使用 ES6 或 TypeScript 的基本经验。

# 本书涵盖内容

*第一章*，*在 Angular 中创建你的第一个 Web 应用程序*，探讨了 Angular 框架的主要特性，并教你了解构成典型 Angular 应用程序的基本构建块。你将研究 Angular 生态系统中可用的不同工具和 IDE 扩展，以增强开发者的工作流程和体验。

*第二章*，*使用 Scully 和 Angular Router 构建 SPA 应用程序*，探讨了 Angular 应用程序基于**单页应用**（**SPA**）架构，通常我们有多页内容，这些内容由不同的 URL 或路由提供服务。另一方面，Jamstack 是一种新兴的热门技术，它允许你构建快速、静态的网站，并直接从 CDN 上提供服务。在本章中，我们将使用 Angular Router 在 Angular 应用程序中实现路由功能。我们还将使用 Scully，这是 Angular 最佳静态站点生成器，来创建一个采用 Jamstack 架构的个人博客。

*第三章*，*使用响应式表单构建问题跟踪系统*，介绍了我们如何构建问题跟踪管理系统，并使用 Angular 响应式表单向系统中添加新问题。我们将使用来自 VMware 的 Clarity Components 设计我们的表单，并包含内置和自定义验证。我们还将对表单中的值变化做出反应，并据此采取行动。

*第四章*，*使用 Angular Service Worker 构建 PWA 天气应用程序*，讨论了 Web 应用程序的用户体验对于所有用户来说并不相同，尤其是在网络覆盖和连接性较差的地方。当我们构建 Web 应用程序时，我们应该考虑到所有类型的网络。在本章中，我们将创建一个使用 OpenWeather API 显示指定地区天气的应用程序。我们将学习如何将应用程序部署到 Firebase Hosting。我们还将探索使用 Angular service worker 的 PWA 技术，以在离线时提供无缝的用户体验。

*第五章*，*使用 Electron 构建桌面 WYSIWYG 编辑器*，这是一个跨平台的 JavaScript 框架，用于使用 Web 技术构建桌面应用程序。当与 Angular 结合使用时，它可以产生真正高性能的应用。在本章中，我们将创建一个可以在桌面上运行的 WYSIWYG 编辑器。我们将构建一个 Angular 应用程序，并将其与流行的 WYSIWYG Angular 库 ngx-wig 集成，然后使用 Electron 将其打包为桌面应用程序。数据将通过 Node.js API 在文件系统中本地持久化。

*第六章*，*使用 Capacitor 和 3D 地图构建移动照片地理标记应用程序*，介绍了 Capacitor，这是 Ionic 框架提供的一项服务，可以将任何 Web 应用程序（如使用 Angular 创建的应用程序）转换为原生应用程序。其主要优势是我们可以使用相同的代码库构建原生移动应用程序和 Web 应用程序。Cesium 是一个流行的 JavaScript 框架，用于构建 3D 地图。在本章中，我们将使用 Capacitor 为我们拍摄的照片构建一个地理标记的移动应用程序。我们将使用各种 Ionic 插件在指定位置拍照并将其持久化到 Cloud Firestore。然后，我们将在 Cesium 3D 查看器中显示所有拍摄照片的列表。

*第七章*，*使用 Angular 为 GitHub 个人资料构建 SSR 应用程序*，深入探讨了**搜索引擎优化**（**SEO**），这是任何网站当今的一个关键方面。谁不想在通过社交媒体分享时让他们的网站看起来很好看？客户端 Web 应用程序的真正挑战是优化它，这可以通过在服务器上渲染内容来实现。在本章中，我们将学习如何使用 GitHub API 创建 GitHub 个人资料应用程序。然后，我们将在服务器上渲染它，并学习如何将状态传输到浏览器。我们还将看到如何动态设置页面标题和附加元数据。

*第八章*，*使用 Nx Monorepo 工具和 NgRx 构建 Enterprise Portal*，介绍了 monorepo 架构，这是一种在单个存储库下处理多个应用程序时流行的技术，它为开发过程提供了速度和灵活性。在本章中，我们将使用 Nx monorepo 开发工具创建两个门户：一个用于最终用户，他们可以在地图上选择一个**兴趣点**（**POI**）并访问它；另一个用于管理员检查特定 POI 的访问统计信息。应用程序状态使用 NgRx 进行管理。

*第九章*，*使用 Angular CLI 和 Angular CDK 构建组件 UI 库*，讨论了企业组织通常需要跨不同 Web 应用程序使用的自定义 UI 库。Angular CDK 提供了创建可访问性和高性能 UI 组件的广泛功能。在本章中，我们将使用 Angular CDK 和 Bulma CSS 框架创建两个不同的组件。我们还将将它们打包成一个单一的 Angular 库，并学习如何在 npm 上发布它们，以便在不同的应用程序中重用。我们还将研究如何将每个组件用作 Angular 元素。

*第十章*，*使用 Schematics 自定义 Angular CLI 命令*，介绍了组织在创建 Angular 实体（如组件或服务）时通常遵循不同的指南。Angular Schematics 可以通过扩展 Angular CLI 命令和提供自定义自动化来帮助他们。在本章中，我们将学习如何使用 Angular Schematics API 来构建我们自己的命令集，用于生成组件和服务。我们将构建一个用于创建包含 Tailwind CSS 框架的 Angular 组件的 schematic，我们还将构建一个默认使用内置 HTTP 客户端的 Angular 服务。

# 为了充分利用本书

您需要在您的计算机上安装 Angular 16 的版本，最好是最新版本。所有代码示例都已在 Windows 操作系统上的 Angular 16.0.0 上进行过测试，但它们也应该适用于 Angular 16 的任何未来版本。

## 下载示例代码文件

本书代码包托管在 GitHub 上，网址为 [`github.com/PacktPublishing/Angular-Projects-Third-Edition`](https://github.com/PacktPublishing/Angular-Projects-Third-Edition)。我们还有其他来自我们丰富图书和视频目录的代码包，可在 [`github.com/PacktPublishing/`](https://github.com/PacktPublishing/) 找到。查看它们吧！

## 下载彩色图像

我们还提供了一份包含本书中使用的截图/图表彩色图像的 PDF 文件。您可以从这里下载：[`packt.link/UbmtQ`](https://packt.link/UbmtQ)。

## 使用的约定

本书使用了多种文本约定。

`CodeInText`: 表示文本中的代码单词、数据库表名、文件夹名、文件名、文件扩展名、路径名、虚拟 URL、用户输入和 Twitter 昵称。例如：“将下载的`WebStorm-10*.dmg`磁盘映像文件作为系统中的另一个磁盘挂载。”

代码块按以下方式设置：

```js
getWeather(city: string): Observable<Weather> {
  const options = new HttpParams()
    .set('units', 'metric')
    .set('q', city)
    .set('appId', this.apiKey);
  return this.http.get<Weather>(this.apiUrl + 'weather', { params: options });
} 
```

当我们希望您注意代码块中的特定部分时，相关的行或项目将以粗体显示：

```js
**import** **{** **HttpClientModule** **}** **from****'@angular/common/http'****;**
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser'; 
```

任何命令行输入或输出都按以下方式编写：

```js
ng generate service weather 
```

**粗体**: 表示新术语、重要单词或屏幕上看到的单词。例如，菜单或对话框中的单词在文本中显示如下。例如：“从**管理**面板中选择**系统信息**。”

警告或重要提示看起来像这样。

小贴士和技巧看起来像这样。

# 联系我们

我们始终欢迎读者的反馈。

**一般反馈**: 发送电子邮件至`feedback@packtpub.com`，并在邮件主题中提及本书的标题。如果您对本书的任何方面有疑问，请通过`questions@packtpub.com`与我们联系。

**勘误**: 尽管我们已经尽最大努力确保内容的准确性，但错误仍然可能发生。如果您在这本书中发现了错误，如果您能向我们报告，我们将不胜感激。请访问[`www.packtpub.com/submit-errata`](http://www.packtpub.com/submit-errata)，点击**提交勘误**，并填写表格。

**盗版**: 如果您在互联网上以任何形式发现我们作品的非法副本，如果您能提供位置地址或网站名称，我们将不胜感激。请通过`copyright@packtpub.com`与我们联系，并提供材料的链接。

**如果您有兴趣成为作者**: 如果您在某个领域有专业知识，并且您有兴趣撰写或为书籍做出贡献，请访问[`authors.packtpub.com`](http://authors.packtpub.com)。

# 分享您的想法

一旦您阅读了《Angular Projects，第三版》，我们很乐意听到您的想法！请[点击此处直接进入此书的亚马逊评论页面](https://packt.link/r/1803239115)并分享您的反馈。

您的评论对我们和科技社区都很重要，并将帮助我们确保我们提供高质量的内容。

# 下载本书的免费 PDF 副本

感谢您购买本书！

您喜欢在路上阅读，但无法携带您的印刷书籍到处走？您的电子书购买是否与您选择的设备不兼容？

不要担心，现在，随着每本 Packt 书籍，您都可以免费获得该书的 DRM 免费 PDF 版本。

在任何地方、任何地点、任何设备上阅读。直接从您喜欢的技术书籍中搜索、复制和粘贴代码到您的应用程序中。

优惠不会就此停止，您还可以获得独家折扣、时事通讯和每日收件箱中的精彩免费内容。

按照以下简单步骤获取好处：

1.  扫描二维码或访问下面的链接

![图片](img/B18465_QR_Free_PDF.png)

[`packt.link/free-ebook/9781803239118`](https://packt.link/free-ebook/9781803239118)

1.  提交您的购买证明

1.  就这样！我们将直接将您的免费 PDF 和其他优惠发送到您的邮箱
