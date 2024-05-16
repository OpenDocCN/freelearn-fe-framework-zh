# 前言

Nuxt.js（本书中将其称为 Nuxt）是建立在 Vue.js 之上的渐进式 Web 框架（本书中将其称为 Vue）用于服务器端渲染（SSR）。使用 Nuxt 和 Vue，构建通用和静态生成的应用程序比以往任何时候都更容易。本书将帮助您快速了解 Nuxt 的基础知识以及如何将其与最新版本的 Vue 集成，使您能够使用 Nuxt 和 Vue.js 构建整个项目，包括身份验证、测试和部署。您将探索 Nuxt 的目录结构，并通过使用 Nuxt 的页面、视图、路由和 Vue 组件以及编写插件、模块、Vuex 存储和中间件来创建您的 Nuxt 项目。此外，您还将学习如何使用 Koa.js（本书中将其称为 Koa）、PHP 标准建议（PSRs）、MongoDB 和 MySQL 从头开始创建 Node.js 和 PHP API 或数据平台，以及使用 WordPress 作为无头 CMS 和 REST API。您还将使用 Keystone.js 作为 GraphQL API 来补充 Nuxt。您将学习如何使用 Socket.IO 和 RethinkDB 创建实时 Nuxt 应用程序和 API，最后使用 Nuxt 从远程 API 生成静态站点并流式传输资源（图像和视频），无论是 REST API 还是 GraphQL API。

# 本书适合对象

这本书适用于任何想要构建服务器端渲染的 Vue.js 应用程序的 JavaScript 或全栈开发人员。对 Vue.js 框架的基本理解将有助于理解本书涵盖的关键概念。

# 本书内容

第一章《介绍 Nuxt》是您将了解 Nuxt 的主要特点的地方。您将了解今天有哪些类型的 Web 应用程序，以及 Nuxt 属于哪些类别。然后，您将在接下来的章节中了解您可以使用 Nuxt 做什么。

第二章《开始使用 Nuxt》是您将安装 Nuxt 的地方，使用脚手架工具，或者从头开始创建您的第一个基本 Nuxt 应用程序。您将了解 Nuxt 项目中的默认目录结构，配置 Nuxt 以适应您的项目，并理解资源服务。

第三章《添加 UI 框架》是您将添加自定义 UI 框架，例如 Zurb Foundation，Motion UI，Less CSS 等等，以使您在 Nuxt 中的 UI 开发更加轻松和有趣。

第四章，“添加视图、路由和过渡”，是您将在 Nuxt 应用程序中创建导航路由、自定义页面、布局和模板的地方。您将学习如何添加过渡和动画，创建自定义错误页面，自定义全局 meta 标签，并为单个页面添加特定标签。

第五章，“添加 Vue 组件”，是您将在 Nuxt 应用程序中添加 Vue 组件的地方。您将学习如何创建全局和局部组件并重用它们，编写基本和全局 mixin，并定义符合命名约定的组件名称。

第六章，“编写插件和模块”，是您将在 Nuxt 应用程序中创建和添加插件、模块和模块片段的地方。您将学习如何创建 Vue 插件并将其安装在您的 Nuxt 项目中，编写全局函数并注册它们。

第七章，“添加 Vue 表单”，是您将使用`v-model`和`v-bind`创建表单的地方，验证表单元素并通过使用修饰符进行动态值绑定。您还将学习如何使用 Vue 插件 VeeValidate，使前端验证变得更加简单。

第八章，“添加服务器端框架”，是您将使用 Koa 作为服务器端框架创建 API 来补充您的 Nuxt 应用程序的地方。您将学习如何安装 Koa 及其必要的 Node.js 包以创建一个完全可用的 API，并将其与您的 Nuxt 应用程序集成。此外，您还将学习如何在 Nuxt 中使用异步数据从 Koa API 获取数据，通过异步数据访问 Nuxt 上下文，监听查询变化，处理错误，并使用 Axios 作为请求 API 数据的 HTTP 客户端。

第九章，“添加服务器端数据库”，是您将使用 MongoDB 管理 Nuxt 应用程序的数据库的地方。您将学习如何安装 MongoDB，编写基本的 MongoDB 查询，向 MongoDB 数据库添加一些虚拟数据，将 MongoDB 与上一章的 Koa API 集成，然后从 Nuxt 应用程序中获取数据。

第十章，*添加 Vuex 存储*，是您将使用 Vuex 管理和集中存储 Nuxt 应用程序数据的地方。您将了解 Vuex 架构，使用存储的变异和操作方法来改变存储数据，当存储变得更大时如何以模块化的方式构建您的存储程序，以及如何在 Vuex 存储中处理表单。

第十一章，*编写路由中间件和服务器中间件*，是您将在 Nuxt 应用程序中创建路由中间件和服务器中间件的地方。您将学习如何使用 Vue Router 创建中间件，使用 Vue CLI 创建 Vue 应用程序，并使用 Express.js（本书中称为 Express）、Koa 和 Connect.js（本书中称为 Connect）作为服务器中间件。

第十二章，*创建用户登录和 API 身份验证*，是您将在 Nuxt 应用程序中为受限页面添加身份验证的地方，使用会话、cookies、JSON Web Tokens（JWTs）、Google OAuth 以及您在上一章中学到的路由中间件。您将学习如何使用 JWT 在后端进行身份验证，在 Nuxt 应用程序中在客户端和服务器端使用 cookies（前端身份验证），以及在后端和前端身份验证中添加 Google OAuth。

第十三章，*编写端到端测试*，是您将使用 AVA、jsdom 和 Nightwatch.js 创建端到端测试的地方。您将学习如何安装这些工具，设置测试环境，并为上一章中 Nuxt 应用程序的页面编写测试。

第十四章，*使用 Linter、格式化程序和部署命令*，是您将使用 ESLint、Prettier 和 StandardJS 来保持代码清洁、可读和格式化的地方。您将学习如何安装和配置这些工具以满足您的需求，并在 Nuxt 应用程序中集成不同的 linter。最后，您将学习如何使用 Nuxt 命令部署您的 Nuxt 应用程序，并了解发布应用程序的托管服务。

第十五章，*使用 Nuxt 创建 SPA*，您将学习如何在 Nuxt 中开发**单页应用程序**（**SPA**），了解 Nuxt 中 SPA 与经典 SPA 的区别，并生成静态 SPA 以部署到静态托管服务器 GitHub Pages。

第十六章，*为 Nuxt 创建一个与框架无关的 PHP API*，您将使用 PHP 创建 API 来补充您的 Nuxt 应用程序。您将学习如何安装 Apache 服务器和 PHP 引擎，了解 HTTP 消息和 PHP 标准，将 MySQL 安装为您的数据库系统，为 MySQL 编写 CRUD 操作，通过遵守 PHP 标准创建与框架无关的 PHP API，然后将您的 API 与 Nuxt 应用程序集成。

第十七章，*使用 Nuxt 创建实时应用程序*，您将使用 RethinkDB、Socket.IO 和 Koa 开发实时 Nuxt 应用程序。您将学习如何安装 RethinkDB，介绍 ReQL，将 RethinkDB 集成到您的 Koa API 中，将 Socket.IO 添加到 API 和 Nuxt 应用程序中，最终将您的 Nuxt 应用程序转换为具有 RethinkDB changefeeds 的实时 Web 应用程序。

第十八章，*使用 CMS 和 GraphQL 创建 Nuxt 应用程序*，您将使用（无头）CMS 和 GraphQL 来补充您的 Nuxt 应用程序。您将学习如何将 WordPress 转换为无头 CMS，在 WordPress 中创建自定义文章类型并扩展 WordPress REST API。您将学习如何在 Nuxt 应用程序中使用 GraphQL，了解 GraphQL 模式和解析器，使用 Appolo Server 创建 GraphQL API，并使用 Keystone.js GraphQL API。此外，您还将学习如何安装和保护 PostgreSQL 和 MongoDB，使用 Nuxt 生成静态站点，并从远程 API 流式传输资源（图像和视频），无论是 REST API 还是 GraphQL API。

# 本书最大的收获

在整本书中，您将需要一个 Nuxt.js 的版本-最新版本，如果可能的话。所有代码示例都是在 Ubuntu 20.10 上使用 Nuxt 2.14.x 进行测试的。以下是本书的其他必要软件、框架和技术列表：

| **书中涵盖的软件/硬件** | **操作系统要求** |
| --- | --- |
| Koa.js v2.13.0 | 任何平台 |
| Axios v0.19.2 | 任何平台 |
| Keystone.js v11.2.0 | 任何平台 |
| Socket.IO v2.3.0 | 任何平台 |
| MongoDB v4.2.6  | 任何平台 |
| MySQL v10.3.22-MariaDB  | 任何平台 |
| RethinkDB v2.4.0 | Linux, macOS |
| PHP v7.4.5  | 任何平台 |
| Foundation v6.6.3 | 任何平台 |
| Swiper.js v6.0.0 | 任何平台 |
| Node.js v12.18.2 LTS (至少 v8.9.0)   | 任何平台 |
| NPM v6.14.7 | 任何平台 |

**如果您使用本书的数字版本，我们建议您自己输入代码或通过 GitHub 存储库访问代码（链接在下一节中提供）。这样做将有助于避免与复制和粘贴代码相关的潜在错误。**

## 下载示例代码文件

您可以从您在[www.packt.com](http://www.packt.com)的账户中下载本书的示例代码文件。如果您在其他地方购买了本书，您可以访问[www.packtpub.com/support](https://www.packtpub.com/support)并注册，文件将直接发送到您的邮箱。

您可以按照以下步骤下载代码文件：

1.  登录或注册网址为[www.packt.com](http://www.packt.com)。

1.  选择“支持”选项卡。

1.  点击“代码下载”。

1.  在搜索框中输入书名，并按照屏幕上的说明进行操作。

下载文件后，请确保使用最新版本的解压缩软件解压缩文件夹：

+   Windows 的 WinRAR/7-Zip

+   Mac 的 Zipeg/iZip/UnRarX

+   Linux 的 7-Zip/PeaZip

该书的代码包也托管在 GitHub 上，网址为[`github.com/PacktPublishing/Hands-on-Nuxt.js-Web-Development`](https://github.com/PacktPublishing/Hands-on-Nuxt.js-Web-Development)。如果代码有更新，将在现有的 GitHub 存储库中进行更新。

我们还有来自丰富书籍和视频目录的其他代码包，可在**[`github.com/PacktPublishing/`](https://github.com/PacktPublishing/)**上找到。快去看看吧！

## 使用的约定

本书中使用了许多文本约定。

`CodeInText`：指示文本中的代码词、数据库表名、文件夹名、文件名、文件扩展名、路径名、虚拟 URL、用户输入和 Twitter 句柄。例如："然后，您可以在 `.css` 文件中创建过渡样式。"

代码块设置如下：

```js
// pages/about.vue
<script>
export default {
  transition: {
    name: 'fade'
```

当我们希望引起您对代码块的特定部分的注意时，相关行或项目将以粗体显示：

```js
[default]
exten => s,1,Dial(Zap/1|30)
exten => s,2,Voicemail(u100)
exten => s,102,Voicemail(b100)
exten => i,1,Voicemail(s0)
```

任何命令行输入或输出都以如下形式书写：

```js
$ npm i less --save-dev
$ npm i less-loader --save-dev
```

**粗体**：表示一个新术语，一个重要词，或者屏幕上看到的词。例如，菜单或对话框中的单词会以这种方式出现在文本中。这是一个例子：“选择手动选择功能以从提示你选择的选项中选择路由器，以选择你需要的功能。”

警告或重要提示会显示为这样。提示和技巧会显示为这样。
