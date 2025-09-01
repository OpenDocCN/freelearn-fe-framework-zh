# 前言

欢迎来到 Angular 企业开发的奇妙世界！独立项目、信号和控制流语法为框架注入了新鲜血液。本书出版时，Angular 17.1 已发布，新增功能将基于信号的组件更接近现实，使 Angular 生态系统始终保持活力。如果这一趋势持续下去，到 Angular 20 时，框架将比以往任何时候都更容易使用，并能够创建任何规模的可信和高性能的应用程序。本书的新版将内容重点放在企业架构上，并继续探索掌握复杂业务需求的高级和可扩展的 Angular 解决方案。

与前版类似，本书经过精心设计，旨在为你提供不可或缺的知识和实用的示例，以便你能够设计、构建和交付健壮的 Angular 应用程序。重点仍然是采用高效和简约的方法——最大限度地发挥 Angular 本身的能力，避免不必要的依赖。这导致代码更加流畅，随着 Angular 快速发展的步伐，维护起来也更加简单。

你从本书中学到的基本概念始终保持活力，即使工具和技术不断演变。以路由为首要架构、Angular 组件、响应式编程模型、强大的路由系统以及直观的模板语法都经受了时间的考验。它们将为你提供一个坚实的基石，随着框架的变化和新版本的出现。

本版对关键主题进行了广泛扩展，包括变化检测、状态管理、解耦组件、模块化设计、路由编排以及与后端系统的集成。你还将学习到企业开发中至关重要的实战技能，如用户认证、数据验证、优化最佳实践和 CI/CD 管道。

这本权威指南分享了构建满足任何业务需求的网络系统的宝贵经验。多年来，代码和内容都经过了行业领先专家的审查和改进，本版也不例外。通过这些专家的集体经验，你可以学习到可操作的食谱、内部技巧和展示专业技术的示例应用程序。

访问配套网站[`AngularForEnterprise.com`](https://AngularForEnterprise.com)，加入社区并保持最新动态。

# 本书面向对象

这本书是为经验丰富的开发者准备的。如果你是 Angular 的新手，请从[`angular.dev/tutorials`](https://angular.dev/tutorials)上的优秀教程开始，熟悉基础知识，然后回到这本书。作为一名 Angular 开发者，你将深化对框架的理解，并接触到设计和部署 Angular 应用到生产环境的整个范围。你将学习易于理解和教授他人的 Angular 模式。作为一名自由职业者，你将掌握有效的工具和技术，以安全、自信和可靠地交付你的 Angular 应用。作为一名企业开发者，你将学习编写具有可扩展架构的 Angular 应用程序的模式和实践，并利用流行的企业工具。

# 本书涵盖的内容

*第一章*，*Angular 的架构和概念*，介绍了 Angular 作为使用 TypeScript、RxJS 和 NgRx 构建复杂、高性能 Web 应用的成熟平台。它介绍了诸如响应式编程、Flux 模式、独立组件、使用 Signals 的细粒度响应性以及保持 Angular 更新的重要性等关键概念。

*第二章*，*表单、Observables、Signals 和 Subjects*，涵盖了创建搜索功能、使用表单、启用组件之间的交互、避免内存泄漏、比较命令式和响应式编程、链式调用 API、使用 Signals 提高性能，以及构建一个小型天气应用程序来展示基本的 Angular 概念。

*第三章*，*构建企业应用架构*，涵盖了在企业 Angular 项目中作为技术负责人或架构师取得成功时的最佳实践和考虑因素，包括成功运行项目的要素、为什么 Angular 适合企业需求、性能优化工具和技术，如 80-20 规则和路由第一架构，以及使用看板进行敏捷规划。

*第四章*，*创建以路由为第一线的业务应用*，涵盖了使用 Angular CLI 生成项目脚手架和组件，实现品牌和图标，使用 DevTools 调试路由，以及路由第一架构的核心原则——早期定义角色、懒加载、骨架导航、围绕数据实体设计、完成高级 UX 设计、实现无状态和松耦合组件、区分控件和组件，以及使用 TypeScript/ECMAScript 最大化代码重用。

*第五章*，*设计身份验证和授权*，涵盖了使用 TypeScript 实现基于令牌的身份验证，使用 JWT 进行安全数据处理，构建具有面向对象原则（如继承和抽象类）的可扩展服务，缓存和 HTTP 拦截器的基本原理以保留登录状态，以及用于测试的内存身份验证服务。关键主题是构建安全的身份验证和授权服务，并将 SOLID 原则应用于使其可扩展。

*第六章*，*实现基于角色的导航*，涵盖了设计条件导航体验，创建用于警报的可重用 UI 服务，使用路由守卫来控制访问，强调服务器端安全性，根据环境动态提供不同的身份验证提供者，以及使用 Firebase 实现身份验证。

*第七章*，*与 REST 和 GraphQL API 一起工作*，涵盖了使用 MEAN 栈的全栈架构 – 使用 TypeScript 构建 Node.js 服务器，使用 Docker 进行容器化，使用 Docker Compose 进行基础设施即代码，CI/CD 验证，使用 OpenAPI 设计 REST API 和 Apollo 的 GraphQL，在 Express 中实现 JWT 身份验证和 RBAC 中间件，以及使用 HttpClient 和 Apollo 在 Angular 中构建自定义身份验证提供者。关键主题是全栈开发、API 设计、RBAC 和端到端身份验证。

*第八章*，*食谱 – 可重用性、表单和缓存*，涵盖了在 Angular 中构建可重用表单、指令和用户控件，包括多步骤响应式表单、通过继承和抽象移除样板代码、动态表单元素如日期选择器、自动完成和表单数组、具有输入掩码和自定义组件的交互式控件、通过`ControlValueAccessor`实现的无缝集成，以及通过提取部分来线性扩展表单复杂性 – 以及布局技术如网格列表。关键主题是可重用性、动态性和交互式表单构建块。

*第九章*，*食谱 – 主/详细、数据表和 NgRx*，通过使用路由优先架构和食谱来实现业务线应用程序，完成了对主要 Angular 应用程序设计考虑的覆盖，包括编辑用户、解析路由数据、重用组件、构建主/详细视图和数据表、使用 NgRx 或 SignalStore 实现状态管理，比较状态管理选项如 NgRx Data、ComponentStore、Signals、Akita 和 Elf，添加预加载动画和全局加载指示器，通过重构应用程序以使用 SignalStore 来预览 Angular 的基于信号的未来。

*第十章*，*使用 CI/CD 发布到生产环境*，涵盖了实现持续集成/持续交付管道，强调自动化测试以实现企业中的快速交付，配置 CircleCI 进行 CI，使用 GitHub flow 通过主干开发强制执行质量门，部署到 Vercel 和 Firebase，使用 Docker 和 NPM 脚本的基础设施即代码技术，容器化和部署到 Google Cloud Run，门控 CI 工作流程，使用工作流程和 orbs 的 CircleCI 编排，代码覆盖率指标，以及自动化部署以实现持续交付 – 允许快速迭代和共享应用程序构建。

*附录 A*，*设置开发环境*，通过使用 CLI 工具自动化和确保 Windows 和 macOS 之间的一致性来设置高效的 Angular 开发环境，创建初始 Angular 项目，优化 VS Code 配置，实施自动 linting 和修复以执行编码标准并捕获错误，通过脚本记录团队规范，以及标准化环境和编码风格如何提高团队生产力和故障排除。

# 为了充分利用本书

+   按照附录 A，*设置开发环境*中涵盖的脚本设置您的系统以进行网络开发。

+   如果你刚开始接触 Angular，请完成[`angular.dev/tutorials`](https://angular.dev/tutorials)上的教程。

+   遵循每章开头的*技术要求*部分以及章节内的信息框。

+   在 GitHub 上查看最新的代码示例[`github.com/duluca`](https://github.com/duluca)。

+   在[`angularforenterprise.com`](https://angularforenterprise.com)注册配套网站以完成自我评估。

+   对于初学者、新接触 Angular 的开发者或经验不足的开发者：

    1.  按照出版顺序阅读本书，并在每一章的内容旁边编码你的解决方案。

    1.  熟悉全栈网络开发有助于，但不是先决条件。

## 下载示例代码文件

你可以在 GitHub 上获取示例代码文件的最新版本。本书直接支持以下四个项目：

1.  网络开发环境设置脚本[`github.com/duluca/web-dev-environment-setup`](https://github.com/duluca/web-dev-environment-setup)

1.  本地天气应用[`github.com/duluca/local-weather-app`](https://github.com/duluca/local-weather-app)

1.  LemonMart[`github.com/duluca/lemon-mart`](https://github.com/duluca/lemon-mart)

1.  LemonMart 服务器[`github.com/duluca/lemon-mart-server`](https://github.com/duluca/lemon-mart-server)

在每一章中，你可以找到访问特定章节代码示例的特定说明，这些示例被称为阶段。在演示持续集成和部署配置时，使用 Git 分支和 GitHub 拉取请求来演示特定的配置元素。

注意，GitHub 上的代码可能与本书内容不同，因为 Angular 在不断发展。

## 下载彩色图像

我们还提供了一份包含本书中使用的截图/图表彩色图像的 PDF 文件。您可以从这里下载：[`packt.link/gbp/9781805127123`](https://packt.link/gbp/9781805127123)。

## 使用的约定

本书使用了多种文本约定。

`CodeInText`：表示文本中的代码单词、数据库表名、文件夹名、文件名、文件扩展名、路径名、虚拟 URL、用户输入和`X`处理。例如，“将下载的`WebStorm-10*.dmg`磁盘映像文件作为系统中的另一个磁盘挂载。”

代码块设置如下：

```js
{
  "name": "local-weather-app",
  "version": "0.0.0",
  "license": "MIT",
  ...
} 
```

当我们希望将您的注意力引到代码块的一部分时，相关的行或项目将以粗体显示：

```js
 "scripts": {
    "ng": "ng",
    "start": "ng serve",
    **"build"**: **"ng build"**,
    **"test"**: **"ng test"**,
    "lint": "ng lint",
    "e2e": "ng e2e"
  }, 
```

任何跨平台或 macOS 特定的命令行输入或输出如下所示：

```js
$ brew tap caskroom/cask 
```

Windows 特定的命令行输入或输出如下所示：

```js
PS> Set-ExecutionPolicy AllSigned; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1')) 
```

**粗体**：表示新术语、重要单词或您在屏幕上看到的单词。例如，菜单或对话框中的单词也以这种方式出现在文本中。例如：“从**管理**面板中选择**系统信息**。”

警告或重要注意事项如下所示。

技巧和窍门如下所示。

# 联系我们

我们读者的反馈总是受欢迎的。

**一般反馈**：请通过`feedback@packtpub.com`发送电子邮件，并在邮件主题中提及书籍标题。如果您对本书的任何方面有疑问，请通过`questions@packtpub.com`与我们联系。

**勘误**：尽管我们已经尽一切努力确保我们内容的准确性，但错误仍然会发生。如果您在这本书中发现了错误，我们将不胜感激，如果您能向我们报告，请访问 http://www.packtpub.com/submit-errata，点击**提交勘误**，并填写表格。

**盗版**：如果您在互联网上以任何形式遇到我们作品的非法副本，如果您能提供位置地址或网站名称，我们将不胜感激。请通过`copyright@packtpub.com`与我们联系，并提供材料的链接。

**如果您有兴趣成为作者**：如果您在某个主题上具有专业知识，并且您有兴趣撰写或为书籍做出贡献，请访问 http://authors.packtpub.com。

# 分享您的想法

一旦您阅读了《Angular for Enterprise Applications, Third Edition》，我们很乐意听到您的想法！请[点击此处直接进入亚马逊评论](https://packt.link/r/1805127128)页面并分享您的反馈。

您的评论对我们和科技社区都很重要，并将帮助我们确保我们提供高质量的内容。

# 下载这本书的免费 PDF 副本

感谢您购买这本书！

你喜欢在旅途中阅读，但无法随身携带你的印刷书籍吗？

您的电子书购买是否与您选择的设备不兼容？

不要担心，现在，您每购买一本 Packt 书籍，都可以免费获得该书的 DRM 免费 PDF 版本。

在任何地方、任何设备上阅读。直接从您最喜欢的技术书籍中搜索、复制和粘贴代码到您的应用程序中。

优惠不会就此停止，您还可以获得独家折扣、时事通讯和每日免费内容的每日电子邮件。

按照以下简单步骤获取优惠：

1.  扫描二维码或访问以下链接

*![二维码描述自动生成](img/B20960FREEPDF_QR.png)*

[`packt.link/free-ebook/9781805127123`](https://packt.link/free-ebook/9781805127123)

1.  提交您的购买证明

1.  就这样！我们将直接将您的免费 PDF 和其他优惠发送到您的电子邮件。
