开始使用 Nuxt

本章将指导您完成从头开始安装 Nuxt 项目或使用 Nuxt 脚手架工具的过程。在开发 Nuxt 应用程序时，安装 Nuxt 是您应该做的第一件事。在本书中，我们将为所有示例应用程序使用 Nuxt 脚手架工具，因为它会自动生成必要的项目文件夹和文件（我们将在本章中探讨），但当然，您也可以从头开始进行小型应用程序开发。我们将介绍目录结构以及每个目录的用途和目的。如果您从头开始安装 Nuxt 项目，您仍需要了解目录结构和 Nuxt 将自动从您的项目中读取的官方目录。您还将学习如何配置 Nuxt 以满足您的应用程序特定的需求，即使 Nuxt 已经默认配置以涵盖大多数实际情况。因此，我们将指导您了解配置的要点。此外，我们将介绍 Nuxt 应用程序中的资源服务，特别是用于提供图像。

本章我们将涵盖的主题如下：

+   安装 Nuxt

+   了解目录结构

+   了解自定义配置

+   了解资源服务

# 第二章：技术要求

您应该熟悉以下术语：

+   JavaScript ES6

+   服务器端和客户端开发基础知识

+   应用程序编程接口（API）

支持的操作系统如下：

+   Windows 10 或更高版本，带有 PowerShell

+   带有终端的 macOS（Bash 或 Oh My Zsh）

+   具有终端的 Linux 系统（如 Ubuntu）

建议的跨平台软件如下：

+   Node.js：[`nodejs.org/`](https://nodejs.org/)

+   Node Package Manager（npm）：[`www.npmjs.com/`](https://www.npmjs.com/)

# 安装 Nuxt

有两种简单的方法可以开始使用 Nuxt。最简单的方法是使用 Nuxt 脚手架工具`create-nuxt-app`，它会自动为您安装所有 Nuxt 依赖项和默认目录。另一种方法是仅使用`package.json`文件从头开始。让我们来了解如何做到这一点。

## 使用 create-nuxt-app

`create-nuxt-app`是 Nuxt 团队创建的一个脚手架工具，您可以使用它快速安装项目。您需要做的是在您喜欢的终端上使用`npx`来运行`create-nuxt-app`：

```js
$ npx create-nuxt-app <project-name>
```

npx 从 npm 5.2.0 开始默认安装，但您可以通过在终端上检查其版本来确保已安装：

```js
$ npx --version
6.14.5
```

在安装 Nuxt 项目的过程中，您将被要求回答一些问题，以便与 Nuxt 集成，如下所示：

+   选择一种编程语言：

```js
JavaScript 
TypeScript 
```

+   选择一个包管理器：

```js
Yarn 
Npm 
```

+   选择一个 UI 框架：

```js
None
Ant Design Vue  
Bootstrap Vue 
...
```

+   选择一个测试框架：

```js
None
Jest
AVA
WebdriverIO 
```

让我们使用 npx 创建您的第一个 Nuxt 应用程序，名为`first-nuxt`。因此，请选择您机器上的本地目录，在该目录上打开终端，并运行`npx create-nuxt-app first-nuxt`。在安装过程中遇到类似之前提到的问题时，请选择`JavaScript`作为编程语言，Npm 作为包管理器，以及`None`作为 UI 框架和测试框架。然后，跳过其余的问题（只是不要选择任何选项），以便我们在需要时稍后添加它们。您的终端上应该有一个类似以下问题的问题列表，以及我们建议的相同选项：

```js
**$ npx create-nuxt-app first-nuxt** 
create-nuxt-app v3.1.0
:: Generating Nuxt.js project in /path/to/your/project/first-nuxt 
? Project name: first-nuxt 
? Programming language: JavaScript 
? Package manager: Npm 
? UI framework: None 
? Nuxt.js modules: (Press <space> to select, <a> to toggle all, <i> to invert selection) 
? Linting tools: (Press <space> to select, <a> to toggle all, <i> to invert selection) 
? Testing framework: None 
? Rendering mode: Universal (SSR / SSG) 
? Deployment target: Server (Node.js hosting) 
? Development tools: (Press <space> to select, <a> to toggle all, <i> to invert selection) 
```

对于有关渲染模式的问题，您应该选择`Universal (SSR / SSG)`。我们将在第十五章中涵盖单页面应用程序（SPA）的选项，*使用 Nuxt 创建 SPA*。在本书中的所有示例应用程序中，除了第十五章中的示例之外，我们将使用 SSR。我们还将在本书中使用`npm`作为我们的包管理器，因此请确保您选择此选项。安装完成后，我们可以启动它：

```js
$ cd first-nuxt
$ npm run dev
```

该应用现在正在`localhost:3000`上运行。当您在您喜爱的浏览器中运行该地址时，您应该会看到 Nuxt 生成的默认索引页面。使用脚手架工具安装 Nuxt 项目是不是很容易？但有时您可能不需要像这样的完整安装；您可能只需要一个“最基本”的安装。如果是这样，请让我们在下一节中了解如何从头开始安装 Nuxt。

您可以在我们的 GitHub 存储库的`/nuxt-packt/chapter-2/scaffolding/`中找到此简单应用程序的源文件。

## 从头开始

如果您不想使用 Nuxt 脚手架工具，您可以使用`package.json`文件和`npm`为您安装 Nuxt 应用程序。让我们通过以下步骤了解如何操作：

1.  在您的根项目中创建一个`package.json`文件：

```js
{
  "name": "nuxt-app",
  "scripts": {
    "dev": "nuxt"
  }
}
```

1.  通过 npm 在项目中安装 Nuxt：

```js
$ npm i nuxt
```

1.  在您的根项目中创建一个`/pages/`目录，然后在其中创建一个`index.vue`页面：

```js
// pages/index.vue
<template>
  <h1>Hello world!</h1>
</template>
```

1.  使用 npm 启动项目：

```js
$ npm run dev
```

应用程序现在正在`localhost:3000`上运行。当你在你喜欢的浏览器中运行该地址时，你应该会看到你创建的带有`Hello world!`消息的索引页面。

然而，无论你选择“最基本”还是完整的堆栈选项，你都应该了解 Nuxt 运行应用程序所需的默认目录。因此，让我们在下一节中找出这些目录是什么。

你可以在我们的 GitHub 存储库的`/nuxt-packt/chapter-2/scratch/`中找到这个简单的应用程序。

# 理解目录结构

如果你成功使用`create-nuxt-app`脚手架工具安装了一个 Nuxt 项目，你应该在项目文件夹中得到以下默认目录和文件：

```js
-| your-app-name/
---| assets/
---| components/
---| layouts/
---| middleware/
---| node_modules/
---| pages/
---| plugins/
---| static/
---| store/
---| nuxt.config.js
---| package.json
---| README.md
```

让我们逐个了解它们，并在接下来的章节中理解它们的用途。

## 资源目录

`/assets/`目录用于包含项目的资源，例如图片、字体和 Less、Stylus 或 Sass 文件，这些文件将由 webpack 编译。例如，你可能有一个 Less 文件，如下所示：

```js
// assets/styles.less
@width: 10px;
@height: @width + 10px;

header {
  width: @width;
  height: @height;
}
```

webpack 将把前面的代码编译成你的应用程序的以下 CSS：

```js
header {
  width: 10px;
  height: 20px;
}
```

我们将在本章后面讨论在该目录中提供图像的好处，并在本书中生成静态页面时经常使用该目录。

## 静态目录

`/static/`目录用于包含不希望被 webpack 编译或无法被编译的文件，例如 favicon 文件。如果你不想在`/assets/`目录中提供你的资源，比如图片、字体和样式，你可以将它们放在`/static/`目录中。该目录中的所有文件都直接映射到服务器根目录，因此可以直接在根 URL 下访问。例如，`/static/1.jpg`被映射为`/1.jpg`，因此可以通过以下方式访问它：

```js
http://localhost:3000/1.jpg
```

我们将在本章后面讨论在`/assets/`和`/static/`目录之间提供图像的区别。请注意，当你使用 Nuxt 脚手架工具时，默认情况下会在该目录中得到一个`favicon.ico`文件，但你可以创建自己的 favicon 文件来替换它。

## 页面目录

`/pages/`目录用于包含应用程序的视图和路由。Nuxt 将读取并转换该目录内的所有`.vue`文件，并为你自动生成应用程序路由。例如，看下面的例子：

```js
/pages/about.vue
/pages/contact.vue
```

Nuxt 将采用前面的文件名（不带`.vue`扩展名）并为你的应用程序创建以下路由：

```js
localhost:3000/about
localhost:3000/contact
```

如果您通过`create-nuxt-app`安装 Nuxt，将会自动为您创建一个`index.vue`文件，并且您可以在`localhost:3000`上看到这个页面。

我们将在第四章中更详细地查看这个目录，*添加视图、路由和过渡*。

## 布局目录

`/layouts/`目录用于包含应用程序的布局。当您使用 Nuxt 脚手架工具时，默认情况下会得到一个名为`default.vue`的布局。您可以修改这个默认布局或者在这个目录中添加新的布局。

我们将在第四章中更详细地查看这个目录，*添加视图、路由和过渡*。

## 组件目录

`/components/`目录用于包含 Vue 组件。当您使用 Nuxt 脚手架工具时，默认情况下会得到一个名为`Logo.vue`的组件。这个目录中的`.vue`文件与`/pages/`目录中的文件的明显和重要区别在于，您不能为这个目录中的组件使用`asyncData`方法；但是，如果需要，您可以使用`fetch`方法来设置它们。您应该将小型和可重用的组件放在这个目录中。

我们将在第五章中更详细地查看这个目录，*添加 Vue 组件*。

## 插件目录

`/plugins/`目录用于包含 JavaScript 函数，比如您想要在根 Vue 实例实例化之前运行的全局函数。例如，您可能想要创建一个新的`axios`实例，专门发送 API 请求到[`jsonplaceholder.typicode.com`](https://jsonplaceholder.typicode.com)，并且您可能希望在全局范围内使用这个实例，而不是每次导入`axios`并创建一个新实例。您可以创建一个插件，将其注入和插入到 Nuxt 上下文中，如下所示：

```js
// plugins/axios-typicode.js
import axios from 'axios'

const instance = axios.create({
  baseURL: 'https://jsonplaceholder.typicode.com'
})

export default (ctx, inject) => {
  ctx.$axiosTypicode = instance
  inject('axiosTypicode', instance)
}
```

然后，您可以通过调用`$axiosTypicode`在任何页面上使用这个`axios`实例，如下所示：

```js
// pages/users/index.vue
export default {
  async asyncData ({ $axiosTypicode, error }) {
    let { data } = await $axiosTypicode.get('/users')
  }
}
```

我们将在第六章中更详细地查看这个目录，*编写插件和模块*。

请注意，`axios`是一个我们在本书中经常使用的 HTTP 客户端。在导入前，您需要在项目目录中安装它。有关这个 Node.js 包的更多信息，请访问[`github.com/axios/axios`](https://github.com/axios/axios)。

## 存储目录

`/store/`目录用于包含 Vuex 存储文件。您不需要在 Nuxt 中安装 Vuex，因为它已经与 Nuxt 一起提供。它默认情况下是禁用的，您只需在此目录中添加一个`index.js`文件即可启用它。例如，如果您想要一个名为`auth`的属性，可以在整个应用程序中访问。

您将在`index.js`文件中将该属性存储在`state`变量中，如下所示：

```js
// store/index.js:
export const state = () => ({
  auth: null
})
```

我们将在第十章中更详细地查看此目录，*添加 Vuex 存储*。

## 中间件目录

`/middleware/`目录用于包含中间件文件，这些文件是在渲染页面或一组页面之前运行的 JavaScript 函数。例如，您可能希望有一个只有在用户经过身份验证时才能访问的秘密页面。您可以使用 Vuex 存储来存储经过身份验证的数据，并创建一个中间件，如果`state`存储中的`auth`属性为空，则抛出`403`错误：

```js
// middleware/auth.js
export default function ({ store, error }) {
  if (!store.state.auth) {
    error({
      message: 'You are not connected',
      statusCode: 403
    })
  }
}
```

我们将在第十一章中更详细地查看此目录，*编写路由中间件和服务器中间件*。

## package.json 文件

`package.json`文件用于包含 Nuxt 应用程序的依赖项和脚本。例如，如果您使用 Nuxt 脚手架工具，则在此文件中会获得以下默认脚本和依赖项：

```js
// package.json
{
  "scripts": {
    "dev": "nuxt",
    "build": "nuxt build",
    "start": "nuxt start",
    "generate": "nuxt generate"
  },
  "dependencies": {
    "nuxt": "².14.0"
  }
}
```

我们将在第八章中大量使用此文件，*添加服务器端框架*，以及在第十四章中，*使用检查器、格式化程序和部署命令*。

## nuxt.config.js 文件

`nuxt.config.js`文件用于包含应用程序特定的自定义配置。例如，当您使用 Nuxt 脚手架工具时，默认情况下会为 HTML 的`<head>`块获取这些自定义的元标记、标题和链接：

```js
export default {
  head: {
    title: process.env.npm_package_name || '',
    meta: [
      { charset: 'utf-8' },
      { name: 'viewport', content: 'width=device-width, initial-scale=1' },
      { hid: 'description', name: 'description', content: 
        process.env.npm_package_description || '' }
    ],
    link: [
      { rel: 'icon', type: 'image/x-icon', href: '/favicon.ico' }
    ]
  }
}
```

我们可以修改前面的自定义头部块。您将在第四章中学习如何做到这一点，*添加视图、路由和转换*。除了`head`之外，还有其他关键属性可用于进行自定义配置，我们将在接下来的部分中介绍。

## 别名

在 Nuxt 中，`~` 或 `@` 别名用于与 `srcDir` 属性关联，`~~` 或 `@@` 别名用于与 `rootDir` 属性关联。例如，如果您想将图像链接到 `/assets/` 目录，可以使用 `~` 别名，如下所示：

```js
<template>
  <img src="~/assets/sample-1.jpg"/>
</template>
```

另一方面，如果您想将图像链接到 `/static/` 目录，可以使用 `~` 别名，如下所示：

```js
<template>
  <img src="~/static/sample-1.jpg"/>
</template>
```

请注意，您也可以在不使用这些别名的情况下链接到 `/static/` 目录中的资源：

```js
<template>
  <img src="/sample-1.jpg"/>
</template>
```

`srcDir` 的值默认与 `rootDir` 的值相同，即 `process.cwd()`。我们将在下一节中介绍这两个选项，您将学习如何更改它们的默认值。因此，让我们探讨如何在项目中自定义配置。

# 理解自定义配置

您可以通过在项目的根目录中添加一个 `nuxt.config.js` 文件（本书中将其称为**Nuxt 配置文件**）来配置您的 Nuxt 应用以适应您的项目。如果您使用 Nuxt 脚手架工具，默认情况下会得到这个文件。当您打开此文件时，应该会得到以下选项（或属性）：

```js
// nuxt.config.js
export default {
  mode: 'universal',
  target: 'server',
  head: { ... },
  css: [],
  plugins: [],
  components: true,
  buildModules: [],
  modules: [],
  build: {}
}
```

其中大多数为空，除了 `mode`、`target`、`head` 和 `components`。您可以通过这些选项定制 Nuxt 以适应您的项目。让我们逐个了解它们，然后再看看其他选项，看看您可以如何使用它们。

## `mode` 选项

`mode` 选项用于定义应用的“性质” - 无论是通用应用还是单页应用。其默认值为 *universal*。如果您正在使用 Nuxt 开发单页应用，那么将此值更改为 `spa`。在本书的即将到来的章节中，我们将专注于通用模式，除了 第十五章 *使用 Nuxt 创建单页应用*。

## `target` 选项

`target` 选项用于设置应用的部署目标 - 无论是作为服务器端渲染应用还是静态生成应用进行部署。其默认值为服务器端渲染部署的 `server`。本书中大多数示例应用的部署目标是服务器端渲染。在最后一章 - 第十八章 *使用 CMS 和 GraphQL 创建 Nuxt 应用* 中，我们也会针对静态生成部署进行目标设置。

## `head` 选项

`head`选项用于在我们应用程序的`<head>`块中定义所有默认的元标签。如果您使用 Nuxt 脚手架工具，您将在 Nuxt 配置文件中获得以下自定义`head`配置：

```js
// nuxt.config.js
export default {
  head: {
    title: process.env.npm_package_name || '',
    meta: [
      { charset: 'utf-8' },
      { name: 'viewport', content: 'width=device-width, initial-scale=1' },
      { hid: 'description', name: 'description', content: 
        process.env.npm_package_description || '' }
    ],
    link: [
      { rel: 'icon', type: 'image/x-icon', href: '/favicon.ico' }
    ]
  }
}
```

您可以修改上述配置或添加更多自定义配置 - 例如，添加一些对项目所需的 JavaScript 和 CSS 库：

```js
// nuxt.config.js
export default {
  head: {
    titleTemplate: '%s - Nuxt App',
    meta: [
      //...
    ],
    script: [
      { src: 'https://cdnjs.cloudflare.com/.../jquery.min.js' },
      { src: 'https://cdn.jsdelivr.net/.../foundation.min.js' },
    ],
    link: [
      { rel: 'stylesheet', href: 
      'https://cdn.jsdelivr.net/.../foundation.min.css' },
    ]
  }
}
```

我们将在第三章的*添加 UI 框架*和第四章的*添加视图、路由和转换*中更详细地介绍这个选项。请注意，jQuery 是 Foundation（Zurb）的核心依赖项，我们将在第三章的*添加 UI 框架*中进行探讨。因此，目前需要在项目中安装 jQuery 才能使用 Foundation。这在未来的版本中可能会变成可选项。

## css 选项

`css`选项用于添加全局 CSS 文件。这些可以是`.css`、`.less`或`.scss`文件。它们也可以是直接从项目中的 Node.js `/node_modules/`目录加载的模块和库。例如，看下面的例子：

```js
// nuxt.config.js
export default {
  css: [
    'jquery-ui-bundle/jquery-ui.min.css',
    '@/assets/less/styles.less',
    '@/assets/scss/styles.scss'
  ]
}
```

在上述配置中，我们从安装在`/node_modules/`目录中的 jQuery UI 模块加载 CSS 文件，以及存储在`/assets/`目录中的 Less 和 Sass 文件。请注意，如果您使用`.less`和`.scss`文件编写样式，您需要安装 Less 和 Sass 模块以及它们的 webpack 加载器，如下所示：

```js
$ npm i less less-loader --save-dev
$ npm i node-sass --save-dev
$ npm i sass-loader --save-dev
```

我们将在第三章的*添加 UI 框架*和[第四章](https://cdp.packtpub.com/hands_on_web_development_with_nuxt_js_2_0/wp-admin/post.php?post=27&action=edit)的*添加视图、路由和转换*中更多地使用这个选项。

## 插件选项

`plugins`选项用于添加在根 Vue 实例之前运行的 JavaScript 插件。例如，看下面的例子：

```js
// nuxt.config.js
export default {
  plugins: ['~/plugins/vue-notifications']
}
```

我们经常与前面的章节中介绍的`/plugins/`目录一起使用这个选项。我们将在第六章的*编写插件和模块*中大量使用这个选项。

## 组件选项

components 选项用于设置/components/目录中的组件是否应该自动导入。如果你有大量组件需要导入到布局或页面中，这个选项非常有用。如果将此选项设置为 true，则无需手动导入它们。它的默认值为 false。我们在本书中为所有应用程序将此选项设置为 true。

有关此选项的更多信息和（高级）用法，请访问 https://github.com/nuxt/components。

## buildModules 选项

buildModules 选项用于注册仅构建的模块 - 仅在开发和构建时需要的模块。在本书中，请注意我们将仅利用 Nuxt 社区中的一些模块，并创建在 Node.js 运行时需要的自定义模块。但是，有关 buildModules 选项和仅构建时需要的模块的更多信息，请访问 https://nuxtjs.org/guide/modules#build-only-modules。

## 模块选项

modules 选项用于向项目添加 Nuxt 模块。例如，可以使用以下内容：

```js
// nuxt.config.js
export default {
  modules: [
    '@nuxtjs/axios',
    '~/modules/example.js'
  ]
}
```

我们还可以直接使用此选项创建内联模块：

```js
// nuxt.config.js
export default {
  modules: [
    function () {
      //...
    }
  ]
}
```

Nuxt 模块本质上是 JavaScript 函数，就像插件一样。我们将在第六章《编写插件和模块》中讨论它们之间的区别。就像经常与/plugins/目录一起使用的 plugins 选项一样，modules 选项经常与/modules/目录一起使用。我们将在第六章《编写插件和模块》中经常使用这个选项。

## 构建选项

build 选项用于自定义 webpack 配置，以便按照您喜欢的方式构建 Nuxt 应用程序。例如，您可能希望在项目中全局安装 jQuery，这样每次需要时就不必使用 import。您可以使用 webpack 的 ProvidePlugin 函数自动加载 jQuery，如下所示：

```js
// nuxt.config.js
import webpack from 'webpack'

export default {
  build: {
    plugins: [
      new webpack.ProvidePlugin({
        $: "jquery"
      })
    ]
  }
}
```

我们将在[第四章](https://cdp.packtpub.com/hands_on_web_development_with_nuxt_js_2_0/wp-admin/post.php?post=27&action=edit)中再次使用`build`选项，*添加视图、路由和转换*，在第六章中，*编写插件和模块*，以及在[第十四章](https://cdp.packtpub.com/hands_on_web_development_with_nuxt_js_2_0/wp-admin/post.php?post=37&action=edit)中，*使用 Linter、格式化程序和部署命令*。

有关你的 Nuxt 应用可以使用这个选项做些什么的更多细节和示例，请访问[`nuxtjs.org/api/configuration-build`](https://nuxtjs.org/api/configuration-build)。有关 webpack 的`ProvidePlugin`函数的更多信息，请访问[`webpack.js.org/plugins/provide-plugin/`](https://webpack.js.org/plugins/provide-plugin/)。如果你是 webpack 的新手，我们鼓励你访问并从[`webpack.js.org/guides/`](https://webpack.js.org/guides/)学习。

以下部分概述了一些额外的选项，可以用来进一步和更具体地定制你的 Nuxt 应用。让我们探索一些在你的项目中可能有用的选项。其中一些在本书中经常使用。所以，让我们开始吧！

## dev 选项

`dev`选项用于定义你的应用的`开发`或`生产`模式。它不会被添加到 Nuxt 配置文件中，但当你需要时可以手动添加。它只接受布尔类型，其默认值设置为`true`。它总是被`nuxt`命令强制为`true`，并且总是被`nuxt build`、`nuxt start`和`nuxt generate`命令强制为`false`。

因此，从技术上讲，你*不能*自定义它，但你可以在 Nuxt 模块中使用这个选项，如下所示：

```js
// modules/sample.js
export default function (moduleOptions) {
  console.log(this.options.dev)
}
```

你将得到`true`或`false`，取决于你使用哪个 Nuxt 命令。我们将在[第六章](https://cdp.packtpub.com/hands_on_web_development_with_nuxt_js_2_0/wp-admin/post.php?post=29&action=edit)中介绍这个模块，*编写插件和模块*。或者，你可以在将 Nuxt 作为包导入服务器端框架时使用这个选项，如下所示：

```js
// server/index.js
import { Nuxt, Builder } from 'nuxt'
import config from './nuxt.config.js'

const nuxt = new Nuxt(config)

if (nuxt.options.dev) {
  new Builder(nuxt).build()
}
```

当`dev`选项为`true`时，`new Builder(nuxt).build()`行将被运行。我们将在[第八章](https://cdp.packtpub.com/hands_on_web_development_with_nuxt_js_2_0/wp-admin/post.php?post=31&action=edit)中介绍服务器端框架，*添加服务器端框架*。

您可以在我们的 GitHub 存储库的`/chapter-2/configuration/dev/`中找到此选项的示例应用程序。

## rootDir 选项

`rootDir`选项用于定义 Nuxt 应用程序的工作空间。例如，假设您的项目位于以下位置：

```js
/var/www/html/my-project/
```

然后，您的项目的`rootDir`选项的默认值为`/var/www/html/my-project/`。但是，您可以按以下方式在`package.json`文件中使用 Nuxt 命令更改它：

```js
// my-project/package.json
{
  "scripts": {
    "dev": "nuxt ./app/"
  }
}
```

现在，您的 Nuxt 应用程序的工作空间位于`/var/www/html/my-project/app/`，您的应用程序结构已变为以下内容：

```js
-| my-project/
---| node_modules/
---| app/
------| nuxt.config.js
------| pages/
------| components/
------| ...
---| package.json
```

请注意，现在 Nuxt 配置文件必须放在`/app/`目录中。我们将在[第十四章](https://cdp.packtpub.com/hands_on_web_development_with_nuxt_js_2_0/wp-admin/post.php?post=37&action=edit)中介绍 Nuxt 命令，*使用 Linter、Formatter 和部署命令*。

您可以在我们的 GitHub 存储库的`/chapter-2/configuration/rooDir/`中找到此选项的示例应用程序。

## srcDir 选项

`srcDir`选项用于定义 Nuxt 应用程序的源目录。`srcDir`的默认值是`rootDir`的值。您可以按以下方式更改它：

```js
// nuxt.config.js
export default {
  srcDir: 'src/'
}
```

现在，您的应用程序结构已变为以下内容：

```js
-| my-project/
---| node_modules/
---| src/
------| pages/
------| components/
------| ...
---| nuxt.config.js
---| package.json
```

请注意，Nuxt 配置文件位于`/src/`目录之外。

您可以在我们的 GitHub 存储库的`/chapter-2/configuration/srcDir/`中找到此选项的示例应用程序。

## 服务器选项

`server`选项用于配置 Nuxt 应用程序的服务器连接变量。它具有以下默认服务器连接详细信息：

```js
export default {
  server: {
    port: 3000,
    host: 'localhost',
    socket: undefined,
    https: false,
    timing: false
  }
}
```

您可以按以下方式更改它们：

```js
export default {
  server: {
    port: 8080,
    host: '0.0.0.0'
  }
}
```

现在，您的应用程序正在`0.0.0.0:8080`上运行。

您可以在我们的 GitHub 存储库的`/chapter-2/configuration/server/`中找到此选项的示例应用程序。

## env 选项

`env`选项用于为 Nuxt 应用程序的客户端和服务器端设置环境变量。此选项的默认值为空对象`{}`。当您在项目中使用`axios`时，此选项非常有用。

采用以下示例：

```js
// nuxt.config.js
export default {
  env: {
    baseUrl: process.env.BASE_URL || 'http://localhost:3000'
  }
}
```

然后，您可以按以下方式在`axios`插件中设置`env`属性：

```js
// plugins/axios.js
import axios from 'axios'

export default axios.create({
  baseURL: process.env.baseUrl
})
```

现在，`baseURL`选项设置为`localhost:3000`，或者如果定义了`BASE_URL`，则为`BASE_URL`。我们可以在`package.json`中设置`BASE_URL`，如下所示：

```js
// package.json
"scripts": {
  "start": "cross-env BASE_URL=https://your-domain-name.com nuxt start"
}
```

您需要在 Windows 上安装`cross-env`才能使上述示例工作：

```js
$ npm i cross-env --save-dev
```

我们将在[第六章](https://cdp.packtpub.com/hands_on_web_development_with_nuxt_js_2_0/wp-admin/post.php?post=29&action=edit)中介绍插件，*编写插件和模块*。在创建跨域应用程序时，我们将在本书中经常使用`env`选项。

您可以在我们的 GitHub 存储库的`/chapter-2/configuration/env/`中找到此选项的示例应用程序。

## 路由器选项

`router`选项用于覆盖 Vue 路由器上的默认 Nuxt 配置。默认 Vue 路由器配置如下：

```js
{
  mode: 'history',
  base: '/',
  routes: [],
  routeNameSplitter: '-',
  middleware: [],
  linkActiveClass: 'nuxt-link-active',
  linkExactActiveClass: 'nuxt-link-exact-active',
  linkPrefetchedClass: false,
  extendRoutes: null,
  scrollBehavior: null,
  parseQuery: false,
  stringifyQuery: false,
  fallback: false,
  prefetchLinks: true
}
```

您可以按以下方式更改此配置：

```js
// nuxt.config.js
export default {
  router: {
    base: '/app/'
  }
}
```

现在，您的应用正在`localhost:3000/app/`上运行。

有关此属性及其余配置的更多信息，请访问[`nuxtjs.org/api/configuration-router`](https://nuxtjs.org/api/configuration-router)。

您可以在我们的 GitHub 存储库的`/chapter-2/configuration/router/`中找到此选项的示例应用程序。

## dir 选项

`dir`选项用于定义 Nuxt 应用中的自定义目录。默认目录如下：

```js
{
  assets: 'assets',
  layouts: 'layouts',
  middleware: 'middleware',
  pages: 'pages',
  static: 'static',
  store: 'store'
}
```

您可以按以下方式更改它们：

```js
// nuxt.config.js
export default {
  dir: {
    assets: 'nuxt-assets',
    layouts: 'nuxt-layouts',
    middleware: 'nuxt-middleware',
    pages: 'nuxt-pages',
    static: 'nuxt-static',
    store: 'nuxt-store'
  }
}
```

现在，您可以按以下方式使用前面的自定义目录：

```js
-| app/
---| nuxt-assets/
---| components/
---| nuxt-layouts/
---| nuxt-middleware/
---| node_modules/
---| nuxt-pages/
---| plugins/
---| modules/
---| nuxt-static/
---| nuxt-store/
---| nuxt.config.js
---| package.json
---| README.md
```

您可以在我们的 GitHub 存储库的`/chapter-2/configuration/dir/`中找到此选项的示例应用程序。

## 加载选项

`loading`选项用于自定义 Nuxt 应用中的默认加载组件。如果您不想使用这个默认加载组件，可以将其设置为`false`，如下所示：

```js
// nuxt.config.js
export default {
  loading: false
}
```

我们将在[第四章](https://cdp.packtpub.com/hands_on_web_development_with_nuxt_js_2_0/wp-admin/post.php?post=27&action=edit)中更详细地介绍这个选项，*添加视图、路由和转换*。

## 页面转换和布局转换选项

`pageTransition`和`layoutTransition`选项用于自定义 Nuxt 应用中页面和布局转换的默认属性。页面转换的默认属性设置如下：

```js
{
  name: 'page',
  mode: 'out-in',
  appear: false,
  appearClass: 'appear',
  appearActiveClass: 'appear-active',
  appearToClass: 'appear-to'
}
```

**布局**转换的默认属性设置如下：

```js
{
  name: 'layout',
  mode: 'out-in'
}
```

您可以按以下方式更改它们：

```js
// nuxt.config.js
export default {
  pageTransition: {
    name: 'fade'
  },
  layoutTransition: {
    name: 'fade-layout'
  }
}
```

我们将在[第四章](https://cdp.packtpub.com/hands_on_web_development_with_nuxt_js_2_0/wp-admin/post.php?post=27&action=edit)中更详细地介绍这些选项，*添加视图、路由和转换*。

## 生成选项

`generate`选项用于告诉 Nuxt 如何为静态 Web 应用程序生成动态路由。动态路由是通过在 Nuxt 中使用下划线创建的路由。我们将在[第四章](https://cdp.packtpub.com/hands_on_web_development_with_nuxt_js_2_0/wp-admin/post.php?post=27&action=edit) *添加视图、路由和过渡*中介绍这种类型的路由。如果我们希望将 Nuxt 应用导出为静态 Web 应用程序或 SPA，而不是将 Nuxt 用作通用应用程序（SSR），则使用`generate`选项来处理动态路由，这些动态路由*无法被 Nuxt 爬虫自动检测到*。例如，如果爬虫无法检测到您的应用中的以下动态路由（分页）：

```js
/posts/pages/1
/posts/pages/2
/posts/pages/3
```

然后，您可以使用此`generate`选项将每个路由的内容生成和转换为 HTML 文件，如下所示：

```js
// nuxt.config.js
export default {
  generate: {
    routes: [
      '/posts/pages/1',
      '/posts/pages/2',
      '/posts/pages/3'
    ]
  }
}
```

我们将向您展示如何使用此选项来生成路由，如果爬虫无法检测到它们，可以在第十五章 *创建 Nuxt SPA*和第十八章 *使用 CMS 和 GraphQL 创建 Nuxt 应用*中找到。

有关此`generate`选项的更多信息和更高级的用法，请访问[`nuxtjs.org/api/configuration-generate`](https://nuxtjs.org/api/configuration-generate)。

随着我们的学习，我们将在接下来的章节中涵盖和发现其他配置选项。然而，这些是您现在应该了解的基本自定义配置选项。现在，让我们在下一个主题中进一步探索 webpack 中的资源服务。

# 了解资源服务

Nuxt 使用`vue-loader`、`file-loader`和`url-loader`webpack 加载程序来提供应用程序中的资产。首先，Nuxt 将使用`vue-loader`处理`<template>`和`<style>`块，使用`css-loader`和`vue-template-compiler`来编译这些块中的元素，例如`<img src="...">`、`background-image: URL(...)`和这些块中的 CSS `@import`为模块依赖项。举个例子：

```js
// pages/index.vue
<template>
  <img src="~/assets/sample-1.jpg">
</template>

<style>
.container {
  background-image: url("~assets/sample-2.jpg");
}
</style>
```

在前述`<template>`和`<style>`块中的图像元素和资产将被编译和转换为以下代码和模块依赖项：

```js
createElement('img', { attrs: { src: require('~/assets/sample-1.jpg') }})
require('~/assets/sample-2.jpg')
```

请注意，从 Nuxt 2.0 开始，`~/`别名在样式中将无法正确解析，因此请改用`~assets`或`@/`别名。

在前面的编译和翻译之后，Nuxt 将使用`file-loader`来解析`import/require`模块依赖关系为 URL，并将资产发射（复制并粘贴）到输出目录 - 或者，使用`url-loader`将资产转换为 Base64 URI，如果资产小于 1KB。然而，如果资产大于 1KB 的阈值，它将退回到`file-loader`。这意味着任何小于 1KB 的文件将被`url-loader`内联为 Base64 数据 URL，如下所示：

```js
<img src="data:image/png;base64,iVBO...">
```

这可以让您更好地控制应用程序向服务器发出的 HTTP 请求的数量。内联资产会减少 HTTP 请求，而任何超过 1KB 的文件都将被复制并粘贴到输出目标，并以版本哈希命名以获得更好的缓存。例如，前述`<template>`和`<style>`块中的图像将被发射如下（通过`npm run build`）：

```js
img/04983cb.jpg 67.3 KiB [emitted]
img/cc6fc31.jpg 85.8 KiB [emitted]
```

您将在浏览器的前端看到以下图像：

```js
<div class="links">
  <img src="/_nuxt/img/04983cb.jpg">
</div>
```

以下是这两个 webpack 加载程序（`url-loader`和`file-loader`）的默认配置：

```js
[
  {
    test: /\.(png|jpe?g|gif|svg|webp)$/i,
    use: [{
      loader: 'url-loader',
      options: Object.assign(
        this.loaders.imgUrl,
        { name: this.getFileName('img') }
      )
    }]
  },
  {
    test: /\.(woff2?|eot|ttf|otf)(\?.)?$/i,
    use: [{
      loader: 'url-loader',
      options: Object.assign(
        this.loaders.fontUrl,
        { name: this.getFileName('font') }
      )
    }]
  },
  {
    test: /\.(webm|mp4|ogv)$/i,
    use: [{
      loader: 'file-loader',
      options: Object.assign(
        this.loaders.file,
        { name: this.getFileName('video') }
      )
    }]
  }
]
```

您可以像我们在前面的主题中所做的那样使用 webpack 配置的`build`选项来自定义此默认配置。

有关`file-loader`和`url-loader`的更多信息，请访问[`webpack.js.org/loaders/file-loader/`](https://webpack.js.org/loaders/file-loader/)和[`webpack.js.org/loaders/url-loader/`](https://webpack.js.org/loaders/url-loader/)。

有关`vue-loader`和`vue-template-compiler`的更多信息，请访问[`vue-loader.vuejs.org/`](https://vue-loader.vuejs.org/)和[`www.npmjs.com/package/vue-template-compiler`](https://www.npmjs.com/package/vue-template-compiler)。

如果您对 webpack 不熟悉，请访问[`webpack.js.org/concepts/`](https://webpack.js.org/concepts/)。另请访问[`webpack.js.org/guides/asset-management/`](https://webpack.js.org/guides/asset-management/)了解其资产管理指南。简而言之，webpack 是 JavaScript 应用程序的静态模块打包工具。它的主要目的是捆绑 JavaScript 文件，但也可以用于转换 HTML、CSS、图像和字体等资产。如果您不想以 webpack 为您提供的方式提供资产，您也可以使用`/static/`目录用于静态资产，就像我们在前一节“理解目录结构”中提到的那样。然而，使用 webpack 提供资产也有好处。让我们在下一节中了解它们是什么。

## webpack 资产与静态资产

使用 webpack 提供资产的好处之一是它会为生产进行优化，无论是图像、字体还是预处理样式，如 Less、Sass 或 Stylus。webpack 可以将 Less、Sass 和 Stylus 转换为通用 CSS，而静态文件夹只是一个放置所有静态资产的地方，这些资产将*永远*不会被 webpack 触及。在 Nuxt 中，如果您不想为项目使用`/assets/`目录中的 webpack 资产，可以使用`/static/`目录代替。

例如，我们可以从`/static/`目录中使用静态图像，如下所示：

```js
// pages/index.vue
<template>
  <img src="/sample-1.jpg"/>
</template>
```

另一个很好的例子是 Nuxt 配置文件中的 favicon 文件：

```js
// nuxt.config.js
export default {
  head: {
    link: [
      { rel: 'icon', type: 'image/x-icon', href: '/favicon.ico' }
    ]
  }
}
```

请注意，如果您使用`~`别名来链接`/static/`目录中的资产，webpack *将*处理这些资产，就像`/assets/`目录中的资产一样，如下所示：

```js
// pages/index.vue
<template>
  <img src="~/static/sample-1.jpg"/>
</template>
```

我们将在[第三章](https://cdp.packtpub.com/hands_on_web_development_with_nuxt_js_2_0/wp-admin/post.php?post=26&action=edit)中大量使用`/assets/`目录来提供资产，*添加 UI 框架*，以及在[第四章](https://cdp.packtpub.com/hands_on_web_development_with_nuxt_js_2_0/wp-admin/post.php?post=27&action=edit)中，*添加视图、路由和转换*，以及在[第五章](https://cdp.packtpub.com/hands_on_web_development_with_nuxt_js_2_0/wp-admin/post.php?post=28&action=edit)中，*添加 Vue 组件*，以动态方式提供资产。现在，让我们总结一下您在本章中学到的内容。

您可以在我们的 GitHub 存储库的`/chapter-2/assets/`中找到一个用于从这两个目录提供资产和文件的示例应用程序。

# 总结

在本章中，您学会了如何使用`create-nuxt-app`安装 Nuxt，以及如何从头开始安装它，以及 Nuxt 脚手架工具安装的默认目录。您还学会了如何使用`nuxt.config.js`文件来自定义您的应用程序。最后，您学会了了解 Nuxt 中资产的工作方式以及使用 webpack 和`/static/`文件夹进行资产提供之间的区别。

在即将到来的章节中，您将学习如何为您的应用程序安装自定义 UI 框架、库和工具，例如 Zurb Foundation、Motion UI、jQuery UI 和 Less CSS。您将编写一些基本代码来为您的首页添加样式并为其添加一些动画。您还将开始使用我们在本章中刚刚介绍的一些目录，如`/assets/`、`/plugins/`和`/pages/`目录，来开发您的 Nuxt 应用程序。
