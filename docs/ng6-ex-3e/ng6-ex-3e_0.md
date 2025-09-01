# 前言

Angular 6 已经到来，我们非常兴奋！这本书让我们有机会向你伸出援手，帮助你学习 Angular。Angular 已经变得主流，并已成为网络和移动开发的通用平台。

如果你是一名 AngularJS 开发者，那么有许多令人兴奋的内容可以学习，对于刚开始的开发者来说，有一个全新的世界可以探索。即使是经验丰富的 AngularJS 开发者，开始学习 Angular 也可能感到不知所措。会有很多术语被抛向你：例如 TypeScript、Transpiler、Shim、Observable、Immutable、Modules、Exports、Decorators、Components、Web Component 和 Shadow DOM。放松！我们正在努力拥抱现代网络，这里的新事物都是为了使我们的生活更简单。许多这些概念并非特定于 Angular 本身，而是突出了网络平台开发的发展方向。我们将尽力以清晰简洁的方式呈现这些概念，帮助每个人理解这些部分如何融入这个庞大的生态系统。通过示例学习有其优点，例如，你将立即看到概念在实际中的应用。这本书遵循其前辈的相同模式。使用**自己动手做**（**DIY**）的方法，我们使用 Angular 构建了多个简单和复杂的应用程序。

# 这本书面向谁

Angular 可以帮助你更快、更高效、更灵活地构建跨平台应用程序。Angular 目前处于第 6 个版本，其之前的版本中进行了重大变更。这是一本独特的网络开发书籍，将帮助你掌握 Angular 并探索开发单页应用程序的强大解决方案。

# 这本书涵盖了什么内容

第一章，*入门*，介绍了 Angular 框架。我们在 Angular 中创建了一个超级简单的应用程序，突出了框架的一些核心功能。

第二章，*构建我们的第一个应用程序 – 7 分钟健身*，教我们如何构建我们的第一个真正的 Angular 应用程序。在这个过程中，我们将了解更多关于 Angular 的主要构建块之一——组件。我们还将介绍 Angular 的模板结构、数据绑定能力和服务。

第三章，*更多 Angular – SPA 和路由*，介绍了框架中的路由结构，我们在其中为*7 分钟健身*构建了多个页面。本章还探讨了组件间通信的多种模式。

第四章，*私人教练*，介绍了一种新的锻炼，我们将*7 分钟健身*转变为通用的私人教练应用程序。这个应用程序具有创建除原始 7 分钟健身之外的新锻炼计划的能力。本章涵盖了 Angular 的表单功能以及我们如何使用它们来构建自定义锻炼。

第五章，*支持服务器数据持久性*，处理从服务器保存和检索锻炼数据。当我们探索 Angular 的 http 客户端库及其如何使用 RxJS Observables 时，我们增强了个人教练的持久性功能。

第六章，*深入 Angular 指令*，深入探讨了 Angular 指令和组件的内部工作原理。我们构建了一系列指令来支持个人教练。

*第七章*，*测试个人教练*，向您介绍了 Angular 的测试世界。您构建了一系列单元测试和端到端测试，以验证个人教练的功能。

第八章，*一些实用场景*，提供了一些在开发此框架上的应用可能遇到的场景的实用技巧和指导。我们涵盖了诸如身份验证和授权、性能以及最重要的案例，即从 AngularJS 迁移到 Angular 最新版本的应用。

# 要充分利用本书

我们将使用 TypeScript 语言构建我们的应用程序；因此，如果您有一个使 TypeScript 开发变得容易的 IDE，那就更好了。Atom、Sublime、WebStorm 和 Visual Studio（或 VS Code）都是这个目的的出色工具。

# 下载示例代码文件

您可以从[www.packtpub.com](http://www.packtpub.com)的账户下载本书的示例代码文件。如果您在其他地方购买了此书，您可以访问[www.packtpub.com/support](http://www.packtpub.com/support)并注册，以便将文件直接通过电子邮件发送给您。

您可以通过以下步骤下载代码文件：

1.  在[www.packtpub.com](http://www.packtpub.com/support)上登录或注册。

1.  选择“支持”标签。

1.  点击“代码下载与勘误”。

1.  在搜索框中输入书籍名称，并遵循屏幕上的说明。

下载文件后，请确保您使用最新版本解压缩或提取文件夹，使用以下工具：

+   Windows 版的 WinRAR/7-Zip

+   Mac 版的 Zipeg/iZip/UnRarX

+   Linux 版的 7-Zip/PeaZip

本书代码包也托管在 GitHub 上，网址为[`github.com/chandermani/angular6byexample`](https://github.com/chandermani/angular6byexample)。如果代码有更新，它将在现有的 GitHub 仓库中更新。

我们还有其他来自我们丰富的图书和视频目录的代码包可供选择，网址为**[`github.com/PacktPublishing/`](https://github.com/PacktPublishing/)**。查看它们吧！

# 使用的约定

本书使用了多种文本约定。

`CodeInText`: 表示文本中的代码单词、数据库表名、文件夹名、文件名、文件扩展名、路径名、虚拟 URL、用户输入和 Twitter 昵称。以下是一个示例：“我们可以看到路由器如何将`app.routes.ts`中的路由与`workout-builder.routes.ts`中的默认路由组合在一起”。

代码块如下设置：

```js
"styles": [
   "node_modules/bootstrap/dist/css/bootstrap.min.css",
   "src/styles.css"
],
```

当我们希望您注意代码块中的特定部分时，相关的行或项目将以粗体显示：

```js
const routes: Routes = [
    ...
    { path: 'builder', loadChildren: './workout-builder/workout-builder.module#WorkoutBuilderModule'},
    { path: '**', redirectTo: '/start' }
];
```

任何命令行输入或输出都应如下编写：

```js
ng new guessthenumber --inlineTemplate
```

**粗体**：表示新术语、重要单词或您在屏幕上看到的单词。例如，菜单或对话框中的单词在文本中如下所示。以下是一个示例：“在`Sources`标签页中打开`Developer`工具”。

警告或重要注意事项如下所示。

小技巧和技巧如下所示。

# 联系我们

我们始终欢迎读者的反馈。

**一般反馈**：请发送电子邮件至`feedback@packtpub.com`，并在邮件主题中提及书籍标题。如果您对本书的任何方面有疑问，请发送电子邮件至`questions@packtpub.com`。

**勘误**：尽管我们已经尽一切努力确保内容的准确性，但错误仍然可能发生。如果您在这本书中发现了错误，我们将不胜感激，如果您能向我们报告此错误。请访问[www.packtpub.com/submit-errata](http://www.packtpub.com/submit-errata)，选择您的书籍，点击“勘误提交表单”链接，并输入详细信息。

**盗版**：如果您在互联网上以任何形式发现我们作品的非法副本，如果您能提供位置地址或网站名称，我们将不胜感激。请通过`copyright@packtpub.com`与我们联系，并提供材料的链接。

**如果您有兴趣成为作者**：如果您在某个领域有专业知识，并且您有兴趣撰写或为书籍做出贡献，请访问[authors.packtpub.com](http://authors.packtpub.com/)。

# 评论

请留下评论。一旦您阅读并使用了这本书，为何不在购买该书的网站上留下评论呢？潜在读者可以查看并使用您的客观意见来做出购买决定，Packt 公司可以了解您对我们产品的看法，我们的作者也可以看到他们对书籍的反馈。谢谢！

如需了解 Packt 的更多信息，请访问[packtpub.com](https://www.packtpub.com/)。
