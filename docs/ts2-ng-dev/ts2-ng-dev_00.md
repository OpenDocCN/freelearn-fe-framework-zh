# 前言

决定学习 Angular 可能会让人感到非常不知所措。这是因为编写 Angular 应用程序的事实方式是使用一种名为 TypeScript 的超集语言，这是一种相当新的语言。讽刺的是，TypeScript 通过提供严格类型（如 Java 等严格类型语言中所见）简化了编写 Angular 应用程序的方式，从而改善了我们编写的应用程序的预测行为。本书旨在通过解释 TypeScript 的核心概念，帮助初学者/中级 Angular 开发人员了解 TypeScript 或严格类型的基本概念。

# 本书涵盖内容

第一章《从松散类型到严格类型》讨论了 TypeScript 推出之前开发人员面临的问题，以及 TypeScript 解决了哪些问题。我们将通过讨论松散类型及其挑战，展示一些先前如何解决这些挑战的示例，以及为什么 TypeScript 是更好的选择。

第二章《开始使用 Typescript》概述了 TypeScript 的核心概念，并提供了如何设置一个纯 JavaScript 加 TypeScript 项目的实际示例。第一章中的所有松散类型示例将被重写为 TypeScript，以展示 TypeScript 的效率。

第三章《Typescript 本地类型和特性》深入探讨了内置的 TypeScript 严格类型，这些类型与现有的 JavaScript 松散类型相匹配。每种类型都将通过工作示例进行广泛讨论，展示应该如何使用以及应该如何工作。

第四章《使用 Angular 和 TypeScript 快速上手》讨论了 TypeScript 如何应用于 Angular。为此，需要借助 CLI 工具使 Angular 快速上手。在本章中，我们将讨论使 Angular 和 TypeScript 协同工作所需的条件。我们还将介绍在“Hello World”示例中可能找到的基本 Angular 概念。

第五章，*使用 TypeScript 创建高级自定义组件*，讨论了 Web 组件的概念以及 Angular 如何借助 TypeScript 构建 Web 组件。我们将看到如何使用类创建组件，如何使用 TypeScript 接口实现生命周期钩子，并使用 TypeScript 装饰器定义组件元数据。

第六章，*使用 TypeScript 进行组件组合*，讨论了 Angular 是基于组件的。它解释了组件是如何作为构建块组合在一起，以使一个完全功能的应用程序。我们将讨论使用示例和组件交互（数据传输和事件）对组件进行模块化组合。在这样做的过程中，我们将看到 TypeScript 如何用于让我们检查所有这些移动部分。

第七章，*使用类型服务分离关注点*，讨论了允许逻辑存在于组件中是不好的做法。在这种情况下，Angular 允许您通过服务提供 API 方法，这些组件可以使用。我们将讨论 TypeScript 如何帮助我们在这些 API 方法和组件之间创建合同（使用类型）。

第八章，*使用 TypeScript 改进表单和事件处理*，解释了 Angular 表单模块如何使我们能够使用 TypeScript 编写可预测的类型表单，这是从我们的应用程序用户收集数据的完美手段。我们还将看到如何使用类型化的 DOM 事件（例如，点击、鼠标悬停和按键）来响应用户交互。

第九章，*使用 TypeScript 编写模块、指令和管道*，讨论了 Angular 的次要构建模块以及它们如何最好地与 TypeScript 一起使用。您将学习如何在 Angular 中使用类型和装饰器构建自定义指令和管道。

第十章，*SPA 的客户端路由*，解释了单页应用程序（SPA），它是通过使用 JavaScript 而不是服务器来处理路由来构建的。我们将讨论如何使用 Angular 和 TypeScript，可以使用路由器模块仅使用单个服务器路由构建多个视图应用程序。

第十一章，*使用真实托管数据*，深入探讨了使用 Angular 的 HTTP 模块消耗 API 数据。您将学习如何直接从我们的 Angular 应用程序发出 HTTP 请求。从此请求中获取的数据可以由组件呈现。

第十二章，*测试和调试*，涵盖了对 Angular 构建块进行单元测试的推荐实践。这些包括组件、服务、路由等。

# 本书适合谁

本书中涵盖的示例可以在 Windows、Linux 或 macOS PC 上实现。您需要安装 Node 和 npm 来使用 TypeScript，以及一个体面的网络浏览器。

# 这本书适合谁

本书旨在通过解释 TypeScript 的核心概念，帮助初学者/中级 Angular 开发人员了解 TypeScript 或严格类型的知识很少或根本没有的人。对于已经使用过 Angular 1.x 或其他框架并试图转移到 Angular 2.x 的开发人员来说，这也是一本完美的书籍。

# 约定

在本书中，您将找到一些文本样式，用于区分不同类型的信息。以下是一些样式的示例及其含义的解释。

文本中的代码单词、数据库表名、文件夹名、文件名、文件扩展名、路径名、虚拟 URL、用户输入和 Twitter 句柄显示如下："我们可以通过使用`include`指令来包含其他上下文。"

代码块设置如下：

```ts
[default]
exten => s,1,Dial(Zap/1|30)
exten => s,2,Voicemail(u100)
exten => s,102,Voicemail(b100)
exten => i,1,Voicemail(s0)
```

当我们希望引起您对代码块的特定部分的注意时，相关行或项目会以粗体显示：

```ts
[default]
exten => s,1,Dial(Zap/1|30)
exten => s,2,Voicemail(u100)
exten => s,102,Voicemail(b100)
exten => i,1,Voicemail(s0)
```

任何命令行输入或输出都将按以下方式编写：

```ts
# cp /usr/src/asterisk-addons/configs/cdr_mysql.conf.sample
/etc/asterisk/cdr_mysql.conf
```

**新术语**和**重要单词**以粗体显示。屏幕上显示的单词，比如菜单或对话框中的单词，会以这种方式出现在文本中："点击“下一步”按钮会将您移动到下一个屏幕。"

警告或重要说明会以这样的框出现。

提示和技巧会以这种方式出现。
