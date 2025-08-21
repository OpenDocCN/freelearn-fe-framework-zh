## 第一章：使用 Angular 入门

如果您正在阅读本书，那是因为您是.NET 开发人员，想了解如何将 Angular 与.NET Framework 技术一起使用，如 ASP.NET **Model View Controller**（MVC）和 Web API，以及诸如 Web Forms 和 Web Services 之类的传统技术。它使开发人员能够开发由 Angular 驱动的更丰富和动态的.NET Web 应用程序。Angular 是一个帮助创建动态 Web 应用程序的开源 JavaScript 框架。

在本章中，我们将涵盖以下主题：

+   介绍 Angular

+   Angular 架构

+   使用 Angular 构建一个 Hello World 应用程序

## 介绍 Angular

* * *

在向您介绍 Angular 之前，让我们讨论一下 AngularJS 的历史。一切都始于改进客户端 Web 开发过程。作为改进的一部分，微软引入了 XML HTTP 请求对象以从服务器检索数据。随着引入了像 jQuery 和 Prototype 这样先进的 JavaScript 库，开发人员开始使用 Ajax 从服务器异步请求数据。这些库被广泛用于操作 DOM 并绑定数据到 UI，直到 90 年代末。

Ajax 是异步 JavaScript 和 XML 的缩写。Ajax 可以使 Web 应用程序在不干扰页面显示和行为的情况下异步发送数据到服务器或从服务器检索数据。Ajax 允许 Web 应用程序动态更改内容，而无需重新加载整个页面，通过将数据交换层与表现层解耦来实现。 

2010 年底，引入了两个 JavaScript MVC 框架：Backbone 和 Knockout。Backbone 提供了完整的模型-视图-控制器（MVC）体验，而 Knockout 主要侧重于使用 MVVM 模式进行绑定。随着这些框架的发布，人们开始相信客户端 MVC 框架的威力。

### AngularJS 的诞生

来自 Google 的开发人员认为市场上存在的客户端 MVC 框架中有一个主要的缺失部分，即可测试性。他感觉有更好的方法来实现客户端 MVC，这让他开始了构建 Angular 的旅程。

Google 支持了 Angular 项目，看到了它的潜力，并且使其开源供世界免费使用。Angular 在市场中的所有 MVC 框架之间引起了很大的关注，因为它得到了 Google 的支持，并且具有诸如可测试性和指令等特性。如今，Angular 团队已经从单个开发人员发展到了大量开发人员，并且已经成为在小型、中型或大型 Web 应用程序中添加客户端 MVC 功能的首选。

### 为什么选择 AngularJS？

让我们讨论为什么使用 AngularJS 以及通过使用 AngularJS 我们的应用程序可以获得什么好处或增值：

+   **AngularJS 提供双向绑定**：许多客户端 MVC 框架只提供单向绑定。这意味着其他 MVC 框架只会使用来自服务器的模型来更新 HTML，当用户在页面上更改模型时，框架不会根据所做的更改更新模型。开发人员需要编写代码来根据用户操作更新模型。然而，AngularJS 方便了双向绑定，并通过根据用户在其上的操作更新模型使开发人员的生活更轻松。

+   **AngularJS 利用声明性视图**：这意味着功能将以 HTML 中的声明性指令的形式进行通信，以渲染模型并与 DOM 交互，根据模型的改变改变页面状态。这大大减少了用于此目的的代码量，将其减少了约 50%至 75%，并简化了开发人员的工作。

+   **AngularJS 支持指令概念**：这就像为 Web 应用程序编写一个特定领域的语言。指令将扩展 HTML 的功能，并根据应用程序的变化动态渲染它们，而不仅仅是显示 HTML 页面。

+   **AngularJS 非常易于测试**：如前所述，Angular 开发的主要目标之一是引入可测试的客户端 MVC 框架。AngularJS 非常易于测试，事实上，Angular 团队已经推出了两个框架：Karma 和 Protractor，用于编写端到端单元测试，以确保代码的稳定性，并确保自信地重构代码。

### Angular 2

AngularJS 是一个很好的框架。然而，它已经有六年的历史了，在这六年里，Web 世界发生了很多变化。为了适应 AngularJS 中所有这些现代发展，它将不得不在现有的实现中进行许多改变，这使得 Angular 团队从头开始编写 AngularJS。

在 2014 年 10 月举行的 ngEurope 大会上，宣布了 Angular 2 作为构建复杂 Web 应用的 Angular 1 的重大更新。ngCommunity 有点不满，因为他们在学习和实施 Angular 1 上投入了很多时间，而现在他们又不得不重新学习和实施 Angular。然而，谷歌在从 Angular 1 升级到 2 的迁移和升级过程中投入了大量精力，引入了 ngUpgrade 和 ngForward。一旦开发人员开始学习并使用 Angular 2 构建产品，他们就意识到了更清洁、更快速和更容易的 Angular 2 的威力。

Angular 2 是从零开始重写的。它帮助我们编写干净的、可测试的代码，可以在任何设备和平台上运行。Angular 2 消除了 Angular 1 中的许多概念。Angular 2 遵循了 ECMAScript 2015 的标准化。随着最近的 Web 标准化，影子 DOM 取代了传递和 ECMAScript 6 模块取代了 Angular 模块。Angular 2 比 Angular 1.x 快五倍。

### Angular 2 的优势

以下是 Angular 2 的特性和优势：

+   它支持跨平台应用程序开发，比如高性能应用程序，如使用 Ionic Framework、NativeScript、React Native 创建本机应用程序，并通过使用 Angular 方法访问本机操作系统 API 创建桌面安装应用程序。

+   Angular 2 继承了 Angular 1 的所有优点。它用组件取代了控制器和指令。

+   Angular 2 是用 TypeScript 编写的，也让开发人员能够使用 TypeScript 编写 Angular 2 应用程序。

+   Angular 2 比 Angular 1 明显快得多。新的组件路由器只会加载渲染所请求的视图所需的代码。模板语法使开发人员能够快速创建具有强大模板语法的视图。

+   Angular 2 使我们能够使用阴影 DOM（Document Object Model）。阴影 DOM 封装了 CSS、模板和组件。这样就可以与主文档的 DOM 分离。

+   这是更简单的认知模型。Angular 2 中删除了许多指令，这意味着 Angular 2 的部件更少，移动部件也更少，因此使用 Angular 2 比使用 Angular 1 更容易构建更大的应用程序。

### Angular 2 中的开发流程

Angular 2 有两个开发过程，即以下内容：

+   使用转译器

+   没有转译器

#### 什么是 ECMAScript 6？

ES6 是脚本语言规范的最新版本。它是世界范围内用于客户端脚本的 JavaScript 语言。ECMAScript 6 是 JavaScript 语言的一个伟大更新，这些特性正在 JavaScript 引擎中的实现过程中。

#### 什么是转译器？

转译器基本上将任何特定语言转换为 JavaScript。一个很好的例子就是 Typescript 转译器，它将 Typescript 代码转换为 JavaScript。

#### 什么是 TypeScript？

TypeScript 是由微软开发的开源编程语言。它是 JavaScript 的超集，它使程序员能够用 JavaScript 编写面向对象的程序。 TypeScript 还用于开发转译器，将 TypeScript 转换为 JavaScript。它旨在开发更大型的应用程序。 TypeScript 是根据 ECMAScript 标准的提案开发的。 TypeScript 具有类、模块和箭头函数语法等功能，这些功能是 ECMAScript 6 标准中提出的。

##### JavaScript 的开发流程

在讨论使用转译器的开发过程之前，让我们看看特定于 JavaScript 构建 Web 应用程序的开发过程。我们将在**ECMAScript 5**中编写我们的代码并**部署**到**服务器**上。 ECMAScript 5 是今天每个浏览器都理解的脚本。当**浏览器**发出**请求**时，服务器将提供脚本，浏览器将在客户端运行它。下面的图表显示了 JavaScript 的典型开发流程：:

![](img/image_01_001.png)

JavaScript 的开发流程

##### 带有构建时转译器的开发

我们不仅可以使用当前版本的 JavaScript（ECMAScript 5）编写脚本，还可以使用 Typecript 编写 **ECMAScript 6+** 的脚本并将其 **转译** 成 **ECMAScript 5**。然后，将转译后的脚本 **部署** 到 **服务器**，然后 **浏览器** 的 **请求** 将提供要在客户端执行的 **转译后的脚本** ，即 ECMAScript 5。这样做的好处是我们可以使用最新版本的 JavaScript 或 ECMAScript 的新功能。

![](img/image_01_002.png)

使用构建时转译器的开发过程

##### 使用运行时转译器进行开发

还有一种开发选项称为运行时转译器。在这种情况下，我们首先使用 Typecript 或 CoffeeScript 在 **ECMAScript 6+** 中编写脚本，然后 **部署** 到 **服务器**。当 **请求** 到达 **服务器** 时，它简单地提供在 **浏览器** 中不经转译的 **ECMAScript 6+** 代码。然后，浏览器使用运行时转译器将脚本转译为 **ECMAScript 5** 在客户端执行。这种类型的选项对于生产应用程序不好，因为它会给浏览器增加额外的负载。

![](img/image_01_003.png)

使用运行时转译器的开发过程

##### 转译器选项

在 Angular 2 中，我们有两种选项 - 使用转译器或不使用转译器。以下是一些可用的转译器类型：

+   **Traceur**：这是谷歌公司最受欢迎的转译器，可以在构建时模式和运行时模式下使用。

+   **Babel**：这个转译器适用于最新版本的 ECMAScript。

+   **TypeScript**：这是 Angular 中最受欢迎和首选的转译器之一。Angular 团队与 TypeScript 团队合作，他们一起合作构建了 Angular 2。

### Angular 3 发生了什么？

在发布 Angular 2 后，团队决定采用语义版本控制。语义版本控制遵循三数版本控制，表示主要、次要和补丁。补丁版本是版本中的最后一个数字，通常用于修复 bug。次要版本是版本中的中间数字，处理新功能或增强的发布。最后，主要版本是版本中的第一个数字，用于具有重大更改的发布。

Angular 团队从 Angular 2 使用的 TypeScript 1.8 切换到了 TypeScript 2.2。这带来了一些重大变化，很明显需要增加主要版本号。此外，当前路由模块的版本是 3.3.0，与其他仍在 2.3.0 版本的 Angular 模块不一致。因此，为了使所有模块版本保持同步并遵循语义版本控制，Angular 团队决定在下一个主要发布中使用 Angular 而不是 Angular 3。

### Angular 中的新功能是什么？

以下是 Angular 中的新功能：

+   Angular 需要的脚本语言是 TyepScript 2.1+。

+   预编译模式使得 Angular 在构建过程中编译模板并生成 JavaScript 代码。这有助于我们在构建时识别模板中的错误，而不是在运行时。

+   Angular 动画有着自己的包，这意味着你不需要为那些不需要动画的项目提供动画包。

+   模板标签现在已经被弃用，因为它会与 Web 组件中使用的模板 HTML 标签引起混淆。所以，ng-template 被引入作为 Angular 中的模板。

除此之外，还有新功能在代码级别上被引入。

### 为何对于.NET 开发者来说 Angular 是个好选择？

在.NET Web 应用程序中使用 JavaScript 编写客户端代码的复杂性不断增加，比如数据绑定、服务器调用和验证。.NET 开发人员在使用 JavaScript 编写客户端验证时遇到了困难。所以，他们发现并开始使用 jQuery 插件来进行验证，并大多仅仅用来根据用户动作改变视图。在后来阶段，.NET 开发人员得到了能确保代码结构并提供良好功能以简化客户端代码的 JavaScript 库的照顾。然后，他们最终使用了一些市场上的客户端 MVC 框架。然而，他们只是用 MVC 框架来与服务器通信并更新视图。

后来，**SPA**（**单页应用**）的趋势在 Web 开发场景中出现。这种类型的应用将会用一个初始页面提供服务，可能是在布局视图或主视图中。然后，其他视图将在请求时加载到主视图上。这种情景通过实现客户端路由来实现，这样客户端将从服务器请求视图的一小部分而不是整个视图。这些步骤的实现增加了客户端开发的复杂性。

AngularJS 为.NET 开发者带来了福音，使他们能够减少处理应用程序的客户端开发所需的工作，比如 SPA 等。数据绑定是 Angular 中最酷的功能，它使开发人员能够集中精力处理应用程序的其他部分，而不是编写大量代码来处理数据绑定、遍历、操作和监听 DOM。Angular 中的模板只是简单的纯 HTML 字符串，将被浏览器解析为 DOM；Angular 编译器遍历 DOM 以进行数据绑定和渲染指令。Angular 使我们能够创建自定义 HTML 标签并扩展 DOM 中现有元素的行为。通过内建的依赖注入支持，Angular 通过提供它们的实例来解析依赖参数。

## 用 Angular 构建一个 Hello World 应用

* * *

在我们开始构建我们的第一个 Angular 应用之前，让我们设置开发环境来开始使用 Angular 应用。

### 设置开发环境

在编写任何代码之前要做的第一件事是设置本地开发环境。我们需要一个编辑器来编写代码，一个本地服务器来运行应用程序，包管理工具来管理外部库，编译器来编译代码等等。

#### 安装 Visual Studio Code

Visual Studio Code 是用于编写 Angular 应用程序的最佳编辑器之一。因此，我们首先安装 Visual Studio Code。前往[`code.visualstudio.com/`](https://code.visualstudio.com/)，然后点击**`Download Code for Windows`**。Visual Studio Code 支持 Windows、Linux 和 OS X 等平台。因此，根据您的需求也可以在其他平台上下载它。

![](img/image_01_004.png)

Visual Studio Code 的首页

Visual Studio Code 是一款开源的跨平台编辑器，支持 Windows、Linux 和 OS X。它是一个功能强大的文本编辑器，包括诸如导航、可自定义绑定的键盘支持、语法高亮、括号匹配、自动缩进和片段等功能，支持许多编程语言。它具有内置的 IntelliSense 代码补全、更丰富的语义代码理解和导航、代码重构支持。它提供了简化的、集成的调试体验，支持 Node.js 调试。它是 Visual Studio 的一个轻量级版本。它不包含任何内置的开发服务器，如 IIS Express。但是，在开发过程中，测试 Web 应用程序在本地 Web 服务器中非常重要。市场上有几种可用的方法来设置本地 Web 服务器。

但是，我选择了 lite-server，因为它是一个轻量级的仅用于开发的 Node 服务器，用于提供静态内容，检测更改，刷新浏览器，并提供许多自定义选项。Lite-server 作为 Node.js 的 NPM 包可用。首先，我们将在下一节看如何安装 Node.js。

#### 安装 Node.js

Node.js 用于开发服务器端 Web 应用程序。它是一个开源的跨平台运行时环境。Node.js 中的内置库允许应用程序充当独立的 Web 服务器。Node.js 可用于需要轻量级实时响应的场景，例如通讯应用程序和基于 Web 的游戏。

Node.js 可用于多种平台，如 Windows、Linux、Mac OS X、Sun OS 和 ARM。您还可以下载 Node.js 的源代码，并根据您的需求进行定制。

要安装 Node.js，请前往[`nodejs.org/en/`](https://nodejs.org/en/)，并下载适用于 Windows 的成熟可靠的 LTS（长期支持）版本。

![](img/image_01_005.png)

Node.js 的首页

Node.js 带有 NPM，一个用于获取和管理 JavaScript 库的软件包管理器。要验证 Node.js 和 NPM 的安装是否成功，请按照以下步骤进行检查：

1.  打开 Windows 命令提示符，输入`node -v`命令并运行。您将得到我们安装的 Node.js 的版本。

1.  现在，检查 NPM 是否与 Node.js 一起安装。运行`NPM -v`命令，您将得到已安装的 NPM 的版本号。

![](img/image_01_006.png)

使用命令验证 Node.js 和 NPM 安装的命令提示符

现在，我们拥有了写我们的第一个 Angular 应用程序所需的一切。让我们开始吧。

### 创建一个 Angular 应用程序

我假设您已经安装了 Node.js、NPM 和 Visual Studio Code，并准备好用它们进行开发。现在，让我们按照以下步骤通过克隆 git 存储库创建一个 Angular 应用程序：

1.  打开 Node.Js 命令提示符并执行以下命令：

```ts
      git clone https://github.com/angular/quickstart my-
      angular
```

这个命令将克隆 Angular 快速起步存储库，并为你创建一个名为`my-angular`的 Angular 应用程序，其中包含所需的所有样板代码。

1.  使用 Visual Studio Code 打开克隆的`my-angular`应用程序：

![](img/image_01_007-2.png)

my-angular 应用程序的文件夹结构

文件夹结构和样板代码按照[`angular.io/docs/ts/latest/guide/style-guide.html`](https://angular.io/docs/ts/latest/guide/style-guide.html)上的官方样式指南进行组织。`src`文件夹中包含与应用程序逻辑相关的代码文件，`e2e`文件夹中包含与端到端测试相关的文件。现在不要担心应用程序中的其他文件。现在让我们只关注`package.json`。

1.  点击`package.json`文件；它将包含有关元数据和项目依赖项配置的详细信息。以下是`package.json`文件的内容：

```ts
      {
      "name":"angular-quickstart",
      "version":"1.0.0",
      "description":"QuickStart package.json from the 
      documentation,             
      supplemented with testing support",
      "scripts":{
      "build":"tsc -p src/",
      "build:watch":"tsc -p src/ -w",
      "build:e2e":"tsc -p e2e/",
      "serve":"lite-server -c=bs-config.json",
      "serve:e2e":"lite-server -c=bs-config.e2e.json",
      "prestart":"npm run build",
      "start":"concurrently \"npm run build:watch\" \"npm 
      run serve\"",
      "pree2e":"npm run build:e2e",
      "e2e":"concurrently \"npm run serve:e2e\" \"npm run 
      protractor\"             
      --kill-others --success first",
      "preprotractor":"webdriver-manager update",
      "protractor":"protractor protractor.config.js",
      "pretest":"npm run build",
      "test":"concurrently \"npm run build:watch\" \"karma 
      start             
      karma.conf.js\"",
      "pretest:once":"npm run build",
      "test:once":"karma start karma.conf.js --single-
      run",
      "lint":"tslint ./src/**/*.ts -t verbose"
      },
      "keywords":[
      ],
      "author":"",
      "license":"MIT",
      "dependencies":{
      "@angular/common":"~4.0.0",
      "@angular/compiler":"~4.0.0",
      "@angular/core":"~4.0.0",
      "@angular/forms":"~4.0.0",
      "@angular/http":"~4.0.0",
      "@angular/platform-browser":"~4.0.0",
      "@angular/platform-browser-dynamic":"~4.0.0",
      "@angular/router":"~4.0.0",
      "angular-in-memory-web-api":"~0.3.0",
      "systemjs":"0.19.40",
      "core-js":"².4.1",
      "rxjs":"5.0.1",
      "zone.js":"⁰.8.4"
      },
      "devDependencies":{
      "concurrently":"³.2.0",
      "lite-server":"².2.2",
      "typescript":"~2.1.0",
      "canonical-path":"0.0.2",
      "tslint":"³.15.1",
      "lodash":"⁴.16.4",
      "jasmine-core":"~2.4.1",
      "karma":"¹.3.0",
      "karma-chrome-launcher":"².0.0",
      "karma-cli":"¹.0.1",
      "karma-jasmine":"¹.0.2",
      "karma-jasmine-html-reporter":"⁰.2.2",
      "protractor":"~4.0.14",
      "rimraf":"².5.4",
      "@types/node":"⁶.0.46",
      "@types/jasmine":"2.5.36"
      },
      "repository":{
      }
      }
```

1.  现在，我们需要在命令窗口中运行 NPM install 命令，通过导航到应用程序文件夹来安装`package.json`中指定的必需依赖项：

![](img/image_01_008-2.png)

执行 NPM 命令来安装 package.json 中指定的依赖项

1.  现在，您将会在`node_modules`文件夹下添加所有的依赖项，如下图所示：

![](img/image_01_009-2.png)

`node_modules`文件夹下的依赖项

1.  现在，让我们运行这个应用程序。要运行它，在命令窗口中执行以下命令：

```ts
 npm start
```

1.  打开任何浏览器，并导航到`http://localhost:3000/`；您将会在应用程序中看到以下页面。运行这个命令会构建应用程序，启动 lite-server，并在上面托管应用程序。

![](img/image_01_010-2.png)

在 VS Code 中激活调试窗口

现在让我们详细看一下`index.html`的内容。以下是`index.html`的内容：

```ts
<!DOCTYPE html>
<html>
<head>
<title>Hello Angular </title>
<base href="/">
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<link rel="stylesheet" href="styles.css">
<!-- Polyfill(s) for older browsers -->
<script src="img/shim.min.js"></script>
<script src="img/zone.js"></script>
<script src="img/system.src.js"></script>
<script src="img/systemjs.config.js"></script>
<script>
System.import('main.js').catch(function(err){ console.error(err); });
</script>
</head>
<body>
<my-app>My first Angular app for Packt Publishing...</my-app>
</body>
</html>
```

到目前为止，我们已经看到了如何通过克隆 GitHub 上的官方 QuickStart 存储库来创建 Angular 应用程序。我们将在接下来的章节详细介绍创建 Angular 应用程序的步骤。请注意，脚本是使用 System.js 加载的。System.js 是在运行时加载模块的模块加载器。

## Angular 的架构

* * *

在我们跳转到 Angular 上的 Hello World 应用程序之前，请让我快速介绍一下 Angular 的架构。Angular 的架构由八个核心构建块组成：模块，组件，模板，元数据，数据绑定，服务，指令和依赖注入。

![](img/image_01_011.png)

Angular 的架构

一个 Angular 应用程序通常是从使用 Angular 标签或标记设计模板开始。然后，我们编写组件来处理模板。应用程序特定的逻辑将添加到服务中。最后，起始组件或根组件将传递给 Angular 启动器。

当我们运行应用程序时，Angular 负责向浏览器呈现模板，并根据组件和指令中提供的逻辑处理模板中元素的用户交互。

让我们看看 Angular 的每个模块的目标：

+   任何 Angular 应用程序都将由一组组件组成。

+   服务将被注入组件中。

+   模板负责以 HTML 形式呈现组件。

+   组件包含支持视图或模板的应用程序逻辑。

+   Angular 本身是一组模块。在 Angular 1 中，使用`ng-app`指令引导主模块或应用程序模块。我们可以包含我们的应用程序模块或主模块依赖的其他模块列表；它们将在`angular.module('myApp', [])`中定义为空数组。Angular 使用 ES6 模块，模块中定义的函数或变量应显式导出以供其他模块消费。通过使用 import 关键字，导出的函数或变量可在其他模块中使用，后跟函数名，然后跟随模块名。例如，`import {http}` from `@angular/http`。

+   每个 Angular 库实际上是许多相关的私有模块的外观。

+   指令提供指令以呈现模板。

我们将在接下来的章节中详细介绍 Angular 架构的每个构建块。

## 总结

* * *

很简单，不是吗？我们刚刚向您介绍了 Angular 框架。

我们从 AngularJS 的历史开始。然后，我们讨论了 AngularJS 的优点和 AngularJS 的诞生。我们讨论了 Angular 的新功能，并对 Angular 的架构进行了简要介绍。

我们还看到了编写 Angular 应用程序所需的开发环境设置。

最后，我们演示了如何使用 Visual Studio Code 和 Node.js 创建你的第一个 Angular 应用程序。

这一章节我们有了一个很好的开端，在学习了一些基础知识。然而，这只是开始。在下一章中，我们将讨论 Angular 架构的一些核心构建模块，比如模块、组件、模板和指令。让我们开始吧！
