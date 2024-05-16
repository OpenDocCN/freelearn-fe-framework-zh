# 前言

Vue 是一个最小的前端框架，赋予开发人员创建 Web 应用程序、原型、大型企业应用程序、桌面应用程序和移动应用程序的能力。

Vue 3 是 Vue 的完全重写，并对框架的所有核心 API 进行了更改。这次重写改变了用 TypeScript 编写的代码。在 Vue 3 中，我们暴露了所有核心 API，使每个人都有可能使用 Vue。

本书从实现 Vue 3 的新功能到迁移现有 Vue 应用程序到最新版本的方法开始。您将学习如何在 Vue 中使用 TypeScript，并找到解决常见挑战和问题的简洁解决方案，从实现组件、派生物和动画，到构建插件、添加状态管理、路由和开发完整的单页应用程序（SPA）。

本书中使用的一些库、插件和框架可能会在本书编写和您阅读之间接收更新。因此，请注意任何可能导致破坏性变化的 API 更改或版本更改。

# 本书适合对象

这本书适用于希望了解更多关于 Vue 并希望提高他们的 Vue 技能的 Web 开发人员。我们将从介绍 Vue 3 和 TypeScript 技术开始。在接下来的章节中，读者将了解 Vue 中的新概念及其生态系统插件、UI 框架和高级技巧。

通过从头到尾地阅读本书，您将能够创建一个 Vue 应用程序，使用所有必要的 Vue 插件，并使用顶级 Vue UI 框架。如果您已经熟悉 Vue，您将发现相关的新模式。

# 本书涵盖内容

第一章，*理解 Vue 3 和创建组件*，为读者提供了如何使用新的 Vue 3 API 创建自定义 Vue 组件的方法，并使用 Vue 的暴露核心 API 和组合 API。本章还帮助读者将 Vue 2 应用程序初步升级到 Vue 3。

第二章，*介绍 TypeScript 和 Vue 生态系统*，向读者介绍了 TypeScript 超集以及如何使用它，从基本类型、接口和类型注解开始。读者将准备好使用 Vue CLI、TypeScript 和`vue-class-component`开发 Vue 应用程序。

第三章，“数据绑定、表单验证、事件和计算属性”，讨论了基本的 Vue 开发和组件概念，包括`v-model`、事件监听器、计算属性和`for`循环。读者将介绍 Vuelidate 插件用于表单验证以及如何在 Vue 组件上使用它，以及如何使用`vue-devtools`调试 Vue 组件。

第四章，“组件、混合和功能组件”，向读者介绍了使用不同方法构建组件，包括用于内容的自定义插槽、验证的 props、功能组件以及创建用于代码重用的混合。然后，它向读者介绍了一系列不同的方法来访问子组件的数据，创建依赖注入组件和动态注入组件，以及如何延迟加载组件。

第五章，“通过 HTTP 请求从 Web 获取数据”，向读者展示了如何在 JavaScript 上为 HTTP 调用创建 Fetch API 的自定义包装器，如何在 Vue 中使用该包装器，以及如何在 Vue 上实现自定义异步函数。读者还将学习如何在 axios 中替换包装器的 Fetch API，以及如何在 axios 上实现自定义处理程序。

第六章，“使用 vue-router 管理路由”，介绍了 Vue 的路由插件以及如何在 Vue 上使用它为 Vue 应用程序的页面创建路由。它介绍了管理路由路径的过程，路由路径上带有参数的动态路径，页面组件的延迟加载，为路由创建身份验证中间件，以及使用别名和重定向。

第七章，“使用 Vuex 管理应用程序状态”，探讨了 Vue 状态管理插件，帮助读者了解 Vuex 的工作原理以及如何应用于他们的应用程序。本章还为读者提供了创建 Vuex 模块、操作、突变和获取器的配方，并探讨了如何为存储定义基本状态。

第八章，*使用过渡和 CSS 为您的应用程序添加动画*，通过提供基于 CSS 的自定义动画示例，探讨了 CSS 动画和过渡的基础知识。这些将与 Vue 自定义组件一起使用，以实现一个外观漂亮的应用程序，并为应用程序的用户提供最佳体验。

第九章，*使用 UI 框架创建漂亮的应用程序*，介绍了流行的 UI 框架。读者将使用 Buefy、Vuetify 和 Ant-Design 构建用户注册表单，并了解它们的设计概念。本章的目的是教会读者如何使用 UI 框架创建一个外观良好的应用程序。

第十章，*将应用程序部署到云平台*，展示了如何在 Vercel、Netlify 和 Google Firebase 等自定义第三方主机上部署 Vue 应用程序。通过本章的示例，读者将学会如何使用集成的存储库钩子和自动部署功能自动部署他们的应用程序。

第十一章，*专业联赛-指令、插件、SSR 和更多*，探讨了 Vue 的高级主题，包括模式、最佳实践、如何创建插件和指令，以及如何使用 Quasar 和 Nuxt.js 等高级框架创建应用程序。

# 为了充分利用本书

Vue 3 beta 是撰写本书时可用的版本。所有的代码将在 GitHub 存储库的最终版本上进行更新：[`github.com/PacktPublishing/Vue.js-3.0-Cookbook`](https://github.com/PacktPublishing/Vue.js-3.0-Cookbook)

您需要安装 Node.js 12+，将 Vue CLI 更新到最新版本，并拥有某种良好的代码编辑器。其他要求将在每个示例中介绍。所有软件要求都适用于 Windows、macOS 和 Linux。

要开发 iOS 移动应用程序，您需要一台 macOS 机器以便访问 Xcode 和 iOS 模拟器。以下是总结所有要求的表格：

| **书中涵盖的软件/硬件** | **操作系统要求** |
| --- | --- |
| Vue CLI 4.X | Windows / Linux / macOS |
| TypeScript 3.9.X | Windows / Linux / macOS |
| Quasar-CLI 1.X | Windows / Linux / macOS |
| Nuxt-CLI 3.X.X | Windows / Linux / macOS |
| Visual Studio Code 1.4.X 和 IntelliJ WebStorm 2020.2 | Windows / Linux / macOS |
| Netlify-CLI | Windows / Linux / macOS |
| Vercel-CLI | Windows / Linux / macOS |
| Firebase-CLI | Windows / Linux / macOS |
| Node.js 12+- | Windows / Linux / macOS |
| Python 3 | Windows / Linux / macOS |
| Xcode 11.4 和 iOS 模拟器 | macOS |

**如果您使用本书的数字版本，我们建议您自己输入代码或通过 GitHub 存储库（链接在下一节中提供）访问代码。这样做将帮助您避免与复制和粘贴代码相关的任何潜在错误。**

## 下载示例代码文件

您可以从[www.packt.com](http://www.packt.com)的帐户中下载本书的示例代码文件。如果您在其他地方购买了本书，可以访问[www.packtpub.com/support](https://www.packtpub.com/support)注册，直接将文件发送到您的邮箱。

您可以按照以下步骤下载代码文件：

1.  在[www.packt.com](http://www.packt.com)上登录或注册。

1.  选择“支持”选项卡。

1.  单击“代码下载”。

1.  在搜索框中输入书名，然后按照屏幕上的说明操作。

下载文件后，请确保使用以下最新版本解压缩或提取文件夹：

+   Windows 下的 WinRAR/7-Zip

+   Mac 下的 Zipeg/iZip/UnRarX

+   Linux 下的 7-Zip/PeaZip

该书的代码包也托管在 GitHub 上，网址为[`github.com/PacktPublishing/Vue.js-3.0-Cookbook`](https://github.com/PacktPublishing/Vue.js-3.0-Cookbook)。如果代码有更新，将在现有的 GitHub 存储库上进行更新。

我们还有来自我们丰富图书和视频目录的其他代码包，可在**[`github.com/PacktPublishing/`](https://github.com/PacktPublishing/)**上找到。快来看看吧！

## 使用的约定

本书中使用了许多文本约定。

`CodeInText`：表示文本中的代码词，数据库表名，文件夹名，文件名，文件扩展名，路径名，虚拟 URL，用户输入和 Twitter 句柄。 例如："将下载的`WebStorm-10*.dmg`磁盘映像文件挂载为系统中的另一个磁盘。"

代码块设置如下：

```js
<template>
 <header>
 <div id="blue-portal" />
 </header>
</header>
```

任何命令行输入或输出都以以下方式编写：

```js
$ npm run serve
```

**粗体**：表示新术语，重要单词或屏幕上看到的单词。例如，菜单或对话框中的单词会在文本中以这种方式出现。例如："单击**电子邮件**按钮，将被重定向到**电子邮件注册**表单"

警告或重要说明会以这种方式出现。提示和技巧会以这种方式出现。

# 部分

在本书中，您会经常看到几个标题（*准备工作*，*如何做*，*它是如何工作的*，*还有更多*，和*另请参阅*）。

为了清晰地说明如何完成食谱，请按照以下部分使用这些部分：

## 准备工作

本节告诉您在食谱中可以期待什么，并描述如何设置食谱所需的任何软件或初步设置。

## 如何做…

本节包含了遵循食谱所需的步骤。

## 它是如何工作的…

本节通常包括对前一节发生的事情的详细解释。

## 还有更多…

本节包括有关食谱的其他信息，以使您对食谱更加了解。

## 另请参阅

本节为食谱提供了其他有用信息的链接。
