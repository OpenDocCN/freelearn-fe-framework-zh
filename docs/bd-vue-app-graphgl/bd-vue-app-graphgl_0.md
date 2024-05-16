# 前言

自 2012 年 Facebook 发布以来，GraphQL 已经席卷了互联网。像 Airbnb 和 Audi 这样的大公司已经开始采用它，而中小型公司现在也意识到了这种基于查询的 API 的潜力。

GraphQL 起初可能看起来很奇怪，但当你开始阅读和体验更多时，你就不会再想使用 REST API 了。

通过本书中的示例，你将学习如何从头开始构建一个完整的实时聊天应用程序。首先创建一个 AWS Amplify 环境，然后深入开发你的第一个 GraphQL 模式。然后学习如何添加 AppSync GraphQL 客户端并创建你的第一个 GraphQL 变异。本书还将帮助你发现 GraphQL 的简单性和数据获取能力，使前端开发人员能够轻松与服务器通信。最后，你将了解如何使用 Quasar Framework 创建应用程序组件和布局。最后，你将了解如何在应用程序中创建 Vuex 模块来管理应用程序状态，使用 GraphQL 客户端获取数据，并将应用程序部署到 Web 上。

# 这本书适合谁

这本书适合中级 Vue.js 开发人员，他们想迈出全栈开发的第一步。如果你想了解更多关于使用自定义业务规则开发 Vuex 以及创建入门级企业架构应用程序，那么这本书适合你。在开始阅读本书之前，需要具备 Vue.js 和 JavaScript 的基础知识。

# 这本书涵盖了什么

第一章，*数据绑定、表单验证、事件和计算属性*，讨论了基本的 Vue 开发和组件概念，包括`v-model`、事件监听器、计算属性和`for`循环。读者将介绍如何使用 Vuelidate 插件进行表单验证以及如何在 Vue 组件上使用它，以及如何使用`vue-devtools`调试 Vue 组件。

第二章，*组件、混入和功能组件*，引导读者通过不同的方法构建组件，包括用于内容的自定义插槽、验证的 props、功能组件以及为了代码重用性而创建的混入。然后介绍了一系列不同的方法来访问子组件的数据，创建依赖注入组件和动态注入组件，以及如何延迟加载组件。

第三章，“设置我们的聊天应用程序-AWS Amplify 环境和 GraphQL”，介绍了 AWS Amplify CLI，介绍了如何创建 Amplify 环境。创建他们的身份验证网关与 AWS Cognito，一个 S3 文件托管桶，最后创建 GraphQL API。在这个过程中，读者将创建用于前端和后端通信的驱动程序。

第四章，“创建自定义应用程序组件和布局”，从现在开始，读者将开始开发应用程序。在这一章中，读者将创建用于聊天应用程序页面的组件。读者将创建组件，如`PasswordInput`，`AvatarInput`，`EmailInput`等。

第五章，“创建用户 Vuex、页面和路由”，引导读者构建应用程序的第一个 Vuex 模块，用于管理用户业务规则和存储用户数据。然后读者将创建用户相关的注册、编辑和验证页面。最后，读者将把这些页面添加到 vue-router 模式中。

第六章，“创建聊天和消息 Vuex、页面和路由”，读者将继续创建应用程序的 Vuex 模块。现在是创建聊天模块的时候了。这个模块将包含用户之间通信的业务规则和存储聊天数据。最后，用户将创建与聊天列表和聊天页面相关的页面，然后将其添加到 vue-router 模式中。

第七章，“将您的应用程序转变为 PWA 并部署到 Web”，在这最后一章中，读者将通过将应用程序转变为 PWA 应用程序，为 iOS 设备添加更新通知和安装横幅来完成应用程序。最后，用户将把应用程序部署到 Web 上。

# 为了充分利用本书

本书从 第二章 *组件、混合和功能组件* 开始使用 Vue.js 2.7，因为这是写作时 Quasar Framework 的最新支持版本。本书将在 第三章 *设置我们的聊天应用 - AWS Amplify 环境和 GraphQL* 中使用 Vue.js 3 的代码。所有代码将在 GitHub 存储库的最终版本发布时进行更新：[`github.com/PacktPublishing/Building-Vue.js-Applications-with-GraphQL`](https://github.com/PacktPublishing/Building-Vue.js-Applications-with-GraphQL)

您需要安装 Node.js 12+，将 Vue CLI 更新到最新版本，并且需要一个良好的代码编辑器。其他要求将在每个示例中介绍。所有软件要求都适用于 Windows、macOS 和 Linux。

以下是总结所有要求的表格：

| **章节编号** | **书中涉及的软件/硬件** | **下载链接** | **操作系统要求** |
| --- | --- | --- | --- |
| 1 到 7 | Vue CLI 4.X | [`cli.vuejs.org/`](https://cli.vuejs.org/) | Windows / Linux / macOS |
| 3 到 7 | Quasar-CLI 1.X | [`quasar.dev/`](https://quasar.dev/) | Windows / Linux / macOS |
| 3 到 7 | Visual Studio Code 1.4.X 和 IntelliJ WebStorm 2020.2 | [`code.visualstudio.com/`](https://code.visualstudio.com/) | Windows / Linux / macOS |
| 3 到 7 | AWS Amplify CLI 3.3.X | [`aws.amazon.com/appsync/resources/`](https://aws.amazon.com/appsync/resources/) | Windows / Linux / macOS |
| 1 到 7 | Node.js 12+- | [`nodejs.org/en/download/`](https://nodejs.org/en/download/) | Windows / Linux / macOS |

**如果您使用的是本书的数字版本，我们建议您自己输入代码或通过 GitHub 存储库访问代码（链接在下一部分中提供）。这样做将帮助您避免与复制和粘贴代码相关的任何潜在错误。**

## 下载示例代码文件

您可以从您在 [www.packt.com](http://www.packt.com) 的帐户中下载本书的示例代码文件。如果您在其他地方购买了这本书，您可以访问 [www.packtpub.com/support](https://www.packtpub.com/support) 并注册，以便文件直接通过电子邮件发送给您。

您可以按照以下步骤下载代码文件：

1.  在 [www.packt.com](http://www.packt.com) 上登录或注册。

1.  选择 Support 选项卡。

1.  点击 Code Downloads。

1.  在 Search 框中输入书名，并按照屏幕上的说明操作。

一旦文件下载完成，请确保使用最新版本的解压软件解压文件夹：

+   Windows 的 WinRAR/7-Zip

+   Mac 的 Zipeg/iZip/UnRarX

+   Linux 的 7-Zip/PeaZip

本书的代码包也托管在 GitHub 上，网址为[`github.com/PacktPublishing/Building-Vue.js-Applications-with-GraphQL`](https://github.com/PacktPublishing/Building-Vue.js-Applications-with-GraphQL)。如果代码有更新，将在现有的 GitHub 存储库上更新。

我们还有其他代码包，来自我们丰富的图书和视频目录，可以在**[`github.com/PacktPublishing/`](https://github.com/PacktPublishing/)**上找到。去看看吧！

## 使用的约定

本书中使用了许多文本约定。

`CodeInText`：表示文本中的代码词，数据库表名，文件夹名，文件名，文件扩展名，路径名，虚拟 URL，用户输入和 Twitter 账号。这是一个例子：“为了做到这一点，以管理员身份打开 PowerShell 并执行`> npm install -g windows-build-tools`命令。”

一个代码块设置如下：

```js
<template>
 <header>
 <div id="blue-portal" />
 </header>
</header>
```

任何命令行输入或输出都以以下方式书写：

```js
> npm run serve
```

**粗体**：表示一个新术语，一个重要词，或者你在屏幕上看到的词。例如，菜单或对话框中的单词会以这种方式出现在文本中。这是一个例子：“点击**电子邮件**按钮，将被重定向到**电子邮件注册**表格”

警告或重要说明会以这种方式出现。提示和技巧会以这种方式出现。

# 部分

在本书中，你会经常看到几个标题（*准备工作*，*如何做*，*它是如何工作的*，*还有更多*，和*另请参阅*）。

为了清晰地说明如何完成一个食谱，使用以下部分：

## 准备工作

这一部分告诉你在食谱中可以期待什么，并描述如何设置任何软件或食谱所需的任何初步设置。

## 如何做…

这一部分包含了遵循食谱所需的步骤。

## 它是如何工作的…

这一部分通常包括对前一部分发生的事情的详细解释。

## 还有更多…

这一部分包括了有关食谱的额外信息，以使你对食谱更加了解。

## 另请参阅

这一部分为食谱提供了其他有用信息的链接。
