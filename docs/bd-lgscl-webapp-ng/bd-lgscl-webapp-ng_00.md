# 序言

如果您之前曾受到不可靠的 JavaScript 框架的影响，那么 Angular 平台的成熟度将让您惊叹不已。Angular 可以帮助您构建快速、高效、真实世界的 Web 应用程序。通过本学习路径，您将学习 Angular，并从设计到部署交付高质量、生产就绪的 Angular 应用程序。

您将通过使用 Angular 的构建块创建一个简单的健身应用程序，并将其最终转变为一个全面的个人健身构建器和运行器的最终应用程序“Personal Trainer”，利用 Angular 最基本和强大的特性 - 指令构建。

通过本书，您将学习使用 RxJS 架构 Angular 应用程序的不同方式，以及其中涉及的一些模式。之后，您将介绍路由器优先架构，这是一种设计和开发中大型企业应用程序的七步方法，以及流行的应用程序示例。通过本书的深入学习，您将熟悉使用 Angular、Swagger 和 Docker 进行 Web 开发的范围，学习成功作为 Web 上的个人开发者或企业团队的模式和实践。

本学习路径包括以下 Packt 产品的内容：

+   《Angular 6 指南》，作者：Chandermani Arora, Kevin Hennessy

+   《使用 Redux、RxJS 和 NgRx 架构 Angular 应用程序》，作者：Christoffer Noring

+   《Angular 6 企业级 Web 应用》，作者：Doguhan Uluca

# 本书面向读者

如果您是一名 JavaScript 或前端开发人员，希望全面了解如何使用 Angular 构建端到端企业就绪的应用程序，那么这本学习路径适合您。

# 本书包括哪些内容

《第一章》，*构建我们的第一个应用程序 - 7 分钟锻炼*，教会我们如何构建我们的第一个真正的 Angular 应用程序。在这个过程中，我们将更多了解 Angular 的主要构建块之一，即组件。我们还将介绍 Angular 的模板构造、数据绑定能力和服务。

《第二章》，*个人健身教练*，介绍一个新的练习，我们将 7 分钟锻炼改造成一个通用的个人健身教练应用程序。该应用程序具有创建除原始的 7 分钟锻炼以外的新锻炼计划的能力。本章介绍了 Angular 的表单功能以及如何使用它们来构建自定义锻炼。

第三章，《支持服务器数据持久性》，涉及从服务器保存和检索锻炼数据。随着我们探索 Angular 的 HTTP 客户端库以及它如何使用 RxJS 可观察对象，我们为 Personal Trainer 增添了持久性功能。

《第四章》，*深入理解 Angular 指令*，深入探讨了 Angular 指令和组件的内部工作原理。我们构建了许多指令来支持 Personal Trainer。

第五章，*1.21 Gigawatt – Flux 模式解释*，教授了什么是 Flux 模式以及它包含的概念。展示了如何使用 stores、dispatcher 和几个视图实现 Flux 模式。

第六章，*函数式反应式编程*，深入探讨了函数式编程的某些特性，如高阶函数、不可变性和递归。此外，我们还将看到如何使代码具有响应性以及响应性的意义。

第七章，*操作流和它们的值*，着重向读者传授操作符的知识，这就是为什么 RxJS 如此强大的原因。读者应该在本章中对数据操作以及 Observables 有更多的了解。

第八章，*RxJS 高级*，深入讨论了更高级的 RxJS 概念，例如热和冷 Observables，subjects，错误处理以及如何使用 Marble 测试来测试 RxJS 代码。

第九章，*创建本地天气 Web 应用程序*，介绍了用于沟通想法的易于使用的设计工具的看板方法软件开发。还涵盖了 Angular 基础知识、单元测试以及如何利用 CLI 工具最大程度地发挥作用。

第十章，*为生产发布准备 Angular 应用*，介绍如何使用 Docker 中的容器化来实现云部署。

第十一章，*使用 Angular Material 增强 Angular 应用*，介绍了 Angular Material 并解释了如何使用它构建外观漂亮的应用程序。

第十二章，*创建基于路由的企业级应用程序*，专注于路由优先架构，一个设计和开发中等到大型应用程序的七步方法。

第十三章，*持续集成和 API 设计*，介绍了使用 CircleCI 进行持续集成的方法以及利用 Swagger 与后端 API 进行早期集成。

第十四章，*设计身份验证和授权*，深入探讨了 Angular 和 RESTful 应用程序中与身份验证和授权相关的模式。

第十五章，*Angular 应用程序设计和技巧*，包含了常见的企业应用程序所需的技巧。

第十六章，*AWS 上的高可用云基础设施*，不仅涉及应用程序特性，还介绍了在 AWS 上提供高可用云基础设施的规划。

# 要充分利用本书

我们将使用 TypeScript 语言来构建我们的应用程序；因此，最好使用能够轻松开发 TypeScript 的 IDE。如 Atom、Sublime、WebStorm 和 Visual Studio (或 VS Code)都是此目的的绝佳工具。

# 下载示例代码文件

您可以从[www.packtpub.com](http://www.packtpub.com)的账户下载本书的示例代码文件。如果您在其他地方购买了本书，您可以访问[www.packtpub.com/support](http://www.packtpub.com/support)注册，直接通过电子邮件接收文件。

您可以通过以下步骤下载代码文件：

1.  在[www.packtpub.com](http://www.packtpub.com/support)上登录或注册。

1.  选择“支持”标签。

1.  单击`代码下载和勘误`。

1.  在搜索框中输入书名，然后按照屏幕上的说明进行操作。

下载文件后，请确保使用最新版本的以下工具解压缩或提取文件夹：

+   Windows 平台上使用 WinRAR/7-Zip

+   Mac 平台上使用 Zipeg/iZip/UnRarX

+   Linux 平台上使用 7-Zip/PeaZip

本书的代码束也托管在 GitHub 上，网址为[`github.com/chandermani/angular6byexample`](https://github.com/chandermani/angular6byexample)和[`github.com/PacktPublishing/Architecting-Angular-Applications-with-Redux-RxJs-and-NgRx`](https://github.com/PacktPublishing/Architecting-Angular-Applications-with-Redux-RxJs-and-NgRx)。第三模块的代码束托管在作者的 GitHub 代码库中，网址为[`github.com/duluca/local-weather-app`](https://github.com/duluca/local-weather-app)和[`github.com/duluca/lemon-mart`](https://github.com/duluca/lemon-mart)。如果代码有更新，将在现有的 GitHub 代码库中更新。

我们还在我们丰富的图书和视频目录中有其他代码束可供选择，网址为**[`github.com/PacktPublishing/`](https://github.com/PacktPublishing/)**。去看看吧！

# 采用的约定

本书中使用了许多文本约定。

`CodeInText`：表示文本中的代码词、数据库表名、文件夹名、文件名、文件扩展名、路径名、虚拟 URL、用户输入和 Twitter 账号。以下是一个例子：“我们可以看到路由器如何将`app.routes.ts`中的路由与`workout-builder.routes.ts`中的默认路由合并”。

代码块设置如下所示：

```ts
"styles": [
   "node_modules/bootstrap/dist/css/bootstrap.min.css",
   "src/styles.css"
],
```

当我们希望引起您对代码块的特定部分的注意时，相关行或项会以粗体显示：

```ts
const routes: Routes = [
    ...
    { path: 'builder', loadChildren: './workout-builder/workout-builder.module#WorkoutBuilderModule'},
    { path: '**', redirectTo: '/start' }
];
```

在命令行中输入或输出如下所示：

```ts
ng new guessthenumber --inlineTemplate
```

**粗体**：表示一个新术语、一个重要词或屏幕上看到的字词。例如，菜单或对话框中的字词会以这种方式出现在文本中。以下是一个例子：“在`开发者`工具中打开`Sources`标签”

警告或重要说明如下显示。

类似于此出现的小技巧和窍门。
