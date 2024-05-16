介绍 Nuxt

欢迎来到您的*Nuxt.js Web 开发实践*之旅。在本章中，我们将深入了解 Nuxt，看看构成这个框架的是什么。我们将带您了解 Nuxt 的特性，您将了解 Nuxt 所属的不同类型应用程序的优缺点。最后但同样重要的是，您将发现使用 Nuxt 作为通用 SSR 应用程序、静态站点生成器和单页面应用程序的巨大潜力。

在本章中，我们将涵盖以下主题：

+   从 Vue 到 Nuxt

+   为什么使用 Nuxt？

+   应用程序类型

+   Nuxt 作为通用 SSR 应用程序

+   Nuxt 作为静态站点生成器

+   Nuxt 作为单页面应用程序

让我们开始吧！

# 第一章：从 Vue 到 Nuxt

Nuxt 是一个更高级的 Node.js Web 开发框架，用于创建可以以两种不同模式开发和部署的 Vue 应用程序：通用（SSR）或单页面应用程序（SPA）。此外，您可以在 Nuxt 中部署 SSR 和 SPA 作为静态生成的应用程序。尽管您可以选择 SPA 模式，但 Nuxt 的全部潜力在于其通用模式或用于构建通用应用程序的服务器端渲染（SSR）。通用应用程序用于描述可以在客户端和服务器端都执行的 JavaScript 代码。但是，如果您希望开发经典（或标准/传统）的 SPA，仅在客户端执行的 SPA，您可能希望考虑使用纯 Vue。

请注意，SPA 模式的 Nuxt 应用程序与经典 SPA 略有不同。您将在本书的后面和本章中简要了解更多信息。

Nuxt 是建立在 Vue 之上的，具有一些额外功能，如异步数据、中间件、布局、模块和插件，可以先在服务器端执行您的应用程序，然后再在客户端执行。这意味着应用程序通常比传统的服务器端（或多页面）应用程序渲染更快。

Nuxt 预装了以下软件包，因此您无需像在标准 Vue 应用程序中那样安装它们：

+   Vue（[`vuejs.org/`](https://vuejs.org/)）

+   Vue 路由器（[`router.vuejs.org/`](https://router.vuejs.org/)）

+   Vuex（[`vuex.vuejs.org/`](https://vuex.vuejs.org/)）

+   Vue 服务器渲染器（[`ssr.vuejs.org/`](https://ssr.vuejs.org/)）

+   Vue 元（[`vue-meta.nuxtjs.org/`](https://vue-meta.nuxtjs.org/)）

除此之外，Nuxt 使用 webpack 和 Babel 来编译和捆绑您的代码，使用以下 webpack 加载器：

+   Vue 加载器（[`vue-loader.vuejs.org/`](https://vue-loader.vuejs.org/)）

+   Babel Loader ([`webpack.js.org/loaders/babel-loader/`](https://webpack.js.org/loaders/babel-loader/))

简而言之，webpack 是一个模块打包工具，它将 JavaScript 应用程序中的所有脚本、样式、资产和图像捆绑在一起，而 Babel 是一个 JavaScript 编译器，它将下一代 JavaScript（ES2015+）编译或转译为浏览器兼容的 JavaScript（ES5），以便您可以在当前浏览器上运行您的代码。

有关 webpack 和 Babel 的更多信息，请分别访问[`webpack.js.org/`](https://webpack.js.org/)和[`babeljs.io/`](https://babeljs.io/)。

webpack 使用他们称之为加载器的东西来预处理您通过 JavaScript `import`语句或`require`方法导入的文件。您可以编写自己的加载器，但在编译 Vue 文件时，您无需这样做，因为它们已经由 Babel 社区和 Vue 团队为您创建。我们将在下一节中发现 Nuxt 带来的伟大功能以及这些加载器贡献的功能。

# 为什么使用 Nuxt？

由于传统 SPA 和**多页面应用**（**MPA**）的缺点，存在诸如 Nuxt 之类的框架。我们可以将 Nuxt 视为服务器端渲染 MPA 和传统 SPA 的混合体。因此，它被称为“通用”或“同构”。因此，能够进行服务器端渲染是 Nuxt 的定义特性。在本节中，我们将为您介绍 Nuxt 的其他突出特性，这将使您的应用开发变得简单而有趣。我们将首先介绍的功能允许您通过在文件中使用`.vue`扩展名来编写单文件 Vue 组件。

## 编写单文件组件

我们可以使用几种方法来创建 Vue 组件。全局 Vue 组件是通过使用`Vue.component`创建的，如下所示：

```js
Vue.component('todo-item', {...})
```

另一方面，可以使用普通 JavaScript 对象创建本地 Vue 组件，如下所示：

```js
const TodoItem = {...}
```

这两种方法在小型项目中使用 Vue 是可管理和可维护的，但是当你一次拥有大量具有不同模板、样式和 JavaScript 方法的组件时，对于大型项目来说，管理变得困难。

因此，单文件组件来拯救，我们只使用一个`.vue`文件来创建每个 Vue 组件。如果您的应用程序需要多个组件，只需将它们分开成多个`.vue`文件。在每个文件中，您可以只编写与该特定组件相关的模板、脚本和样式，如下所示：

```js
// pages/index.vue
<template>
  <p>{{ message }}</p>
</template>

<script>
export default {
  data () {
    return { message: 'Hello World' }
  }
}
</script>

<style scoped>
p {
  font-size: 2em;
  text-align: center;
}
</style>
```

在这里，您可以看到我们有一个 HTML 模板，它从 JavaScript 脚本中打印消息，并且描述模板的 CSS 样式，全部在一个`.vue`文件中。这使得您的代码更加结构化、可读和可组织。很棒，不是吗？这只能通过`vue-loader`和 webpack 实现。在 Nuxt 中，我们只在`.vue`文件中编写组件，无论它们是`/components/`、`/pages/`还是`/layouts/`目录中的组件。我们将在第二章中更详细地探讨这一点，*开始使用 Nuxt*。现在，我们将看一下 Nuxt 功能，它允许您直接编写 ES6 JavaScript。

## 编写 ES2015+

Nuxt 在不需要您担心配置和安装 Babel 在 webpack 的情况下，即可编译您的 ES6+代码。这意味着您可以立即编写 ES6+代码，并且您的代码将被编译为可以在旧版浏览器上运行的 JavaScript。例如，当使用`asyncData`方法时，您经常会看到以下解构赋值语法：

```js
// pages/about.vue
<script>
export default {
  async asyncData ({ params, error }) {
    //...
  }
}
</script>
```

在前面的代码中，使用解构赋值语法将 Nuxt 上下文中的属性解包到不同的变量中，以便我们可以在`asyncData`方法中使用这些变量进行逻辑处理。

有关 Nuxt 上下文和 ECMAScript 2015 功能的更多信息，请分别访问[`nuxtjs.org/api/context`](https://nuxtjs.org/api/context)和[`babeljs.io/docs/en/learn/`](https://babeljs.io/docs/en/learn)。

在 Nuxt 中编写 ES6 只能通过`babel-loader`和 webpack 实现。在 Nuxt 中，您可以编写更多内容，包括`async`函数、`await`运算符、`箭头`函数、`import`语句等。那么 CSS 预处理器呢？如果您使用 Sass、Less 或 Stylus 等流行的 CSS 预处理器编写 CSS 样式，但如果您是 Sass 用户而不是 Less 用户或 Stylus 用户，Nuxt 是否支持它们中的任何一个？简短的答案是是。我们将在下一节中找出这个问题的长答案。

## 使用预处理器编写 CSS

在 Nuxt 中，您可以选择喜欢的 CSS 预处理器来编写应用程序的样式，无论是 Sass、Less 还是 Stylus。它们已经在 Nuxt 中为您预配置。您可以在[`github.com/nuxt/nuxt.js/blob/dev/packages/webpack/src/config/base.js`](https://github.com/nuxt/nuxt.js/blob/dev/packages/webpack/src/config/base.js)查看它们的配置。因此，您只需要在 Nuxt 项目中安装预处理器及其 webpack 加载程序。例如，如果您想将 Less 作为 CSS 预处理器，只需在 Nuxt 项目中安装以下依赖项：

```js
$ npm i less --save-dev
$ npm i less-loader --save-dev
```

然后，您可以通过在`<style>`块中将`lang`属性设置为"less"来开始编写您的 Less 代码，如下所示：

```js
// pages/index.vue
<template>
  <p>Hello World</p>
</template>

<style scoped lang="less">
@align: center;
p {
  text-align: @align;
}
</style>
```

从这个例子中，您可以看到在 Nuxt 中编写现代 CSS 样式就像在 Nuxt 中编写现代 JavaScript 一样容易。您只需要安装您喜欢的 CSS 预处理器及其 webpack 加载程序。在本书的后续章节中，我们将使用 Less，但现在让我们找出 Nuxt 提供了哪些其他功能。

有关这些预处理器及其 webpack 加载程序的更多信息，请访问以下链接：

+   [`lesscss.org/`](http://lesscss.org) 用于 Less

+   [`webpack.js.org/loaders/less-loader/`](https://webpack.js.org/loaders/less-loader/) 用于 less-loader

+   [`sass-lang.com/`](https://sass-lang.com/) 用于 Sass

+   [`webpack.js.org/loaders/sass-loader/`](https://webpack.js.org/loaders/sass-loader/) 用于 sass-loader

+   [`stylus-lang.com/`](https://stylus-lang.com/) 用于 Stylus

+   [`github.com/shama/stylus-loader`](https://github.com/shama/stylus-loader) 用于 stylus-loader

尽管 PostCSS 不是预处理器，但如果您想在 Nuxt 项目中使用它，请访问提供的指南[`nuxtjs.org/faq/postcss-plugins`](https://nuxtjs.org/faq/postcss-plugins)。

## 使用模块和插件扩展 Nuxt

Nuxt 是建立在模块化架构之上的。这意味着您可以使用无数的模块和插件来扩展它，适用于您的应用程序或 Nuxt 社区。这也意味着您可以从 Nuxt 和 Vue 社区中选择大量的模块和插件，这样您就不必为您的应用程序重新发明它们。这些链接如下：

+   Nuxt.js 的精彩模块[`github.com/nuxt-community/awesome-nuxt#official`](https://github.com/nuxt-community/awesome-nuxt#official)

+   在[`github.com/vuejs/awesome-vue#components--libraries`](https://github.com/vuejs/awesome-vue#components--libraries)上查看令人敬畏的 Vue.js，用于 Vue 组件、库和插件

模块和插件只是 JavaScript 函数。现在不用担心它们之间的区别；我们将在第六章中讨论这个问题，*编写插件和模块*。

## 在路由之间添加过渡

与传统的 Vue 应用程序不同，在 Nuxt 中，您不必使用包装器`<transition>`元素来处理元素或组件上的 JavaScript 动画、CSS 动画和 CSS 过渡。例如，如果您想在导航到特定页面时应用`fade`过渡，您只需将过渡名称（例如`fade`）添加到该页面的`transition`属性中：

```js
// pages/about.vue
<script>
export default {
  transition: {
    name: 'fade'
  }
}
</script>
```

然后，你可以在`.css`文件中创建过渡样式：

```js
// assets/transitions.css
.fade-enter,
.fade-leave-to {
  opacity: 0;
}

.fade-leave,
.fade-enter-to {
  opacity: 1;
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 3s;
}
```

当导航到`/about`路由时，“fade”过渡将自动应用于`about`页面。很酷，不是吗？如果此时代码或类名看起来有点令人不知所措，不要担心；我们将在第四章中更详细地了解和探索这个过渡特性。

## 管理`<head>`元素

此外，与传统的 Vue 应用程序不同，您可以直接管理应用程序的`<head>`块，而无需安装额外处理它的 Vue 包`vue-meta`。您只需通过`head`属性向任何页面添加所需的`<title>`、`<meta>`和`<link>`数据。例如，您可以通过应用程序的 Nuxt 配置文件管理全局`<head>`元素：

```js
// nuxt.config.js
export default {
  head: {
    title: 'My Nuxt App',
    meta: [
      { charset: 'utf-8' },
      { name: 'viewport', content: 'width=device-width, initial-scale=1' },
      { hid: 'description', name: 'description', content: 'My Nuxt app is 
       about...' }
    ],
    link: [
      { rel: 'icon', type: 'image/x-icon', href: '/favicon.ico' }
    ]
  }
}
```

Nuxt 将为您将此数据转换为 HTML 标记。同样，我们将在第四章中更详细地了解和探索此功能，*添加视图、路由和过渡*。

## 使用 webpack 捆绑和拆分代码

Nuxt 使用 webpack 将您的代码捆绑、缩小并拆分为可以加快应用程序加载时间的块。例如，在一个简单的 Nuxt 应用程序中有两个页面，index/home 和 about，您将为客户端获得类似的块：

```js
Hash: 0e9b10c17829e996ef30 
Version: webpack 4.43.0 
Time: 4913ms 
Built at: 06/07/2020 21:02:26 
                         Asset       Size  Chunks                         Chunk Names 
../server/client.manifest.json   7.77 KiB          [emitted]               
                      LICENSES  389 bytes          [emitted]               
                app.3d81a84.js   51.2 KiB       0  [emitted] [immutable]  app 
        commons/app.9498a8c.js    155 KiB       1  [emitted] [immutable]  commons/app 
commons/pages/index.8dfce35.js   13.3 KiB       2  [emitted] [immutable]  commons/pages/index 
        pages/about.c6ca234.js  357 bytes       3  [emitted] [immutable]  pages/about 
        pages/index.f83939d.js   1.21 KiB       4  [emitted] [immutable]  pages/index 
            runtime.3d677ca.js   2.38 KiB       5  [emitted] [immutable]  runtime 
 + 2 hidden assets 
Entrypoint app = runtime.3d677ca.js commons/app.9498a8c.js app.3d81a84.js 
```

您将为服务器端获取的块如下所示：

```js
Hash: 8af8db87175486cd8e06 
Version: webpack 4.43.0 
Time: 525ms 
Built at: 06/07/2020 21:02:27 
               Asset       Size  Chunks             Chunk Names 
      pages/about.js   1.23 KiB       1  [emitted]  pages/about 
      pages/index.js   6.06 KiB       2  [emitted]  pages/index 
           server.js   80.9 KiB       0  [emitted]  app 
server.manifest.json  291 bytes          [emitted]   
 + 3 hidden assets 
Entrypoint app = server.js server.js.map 
```

这些块和构建信息是在使用 Nuxt `npm run build` 命令构建应用以进行部署时生成的。我们将在第十四章中更详细地了解这一点，*使用 Linters、Formatters 和部署命令*。

除此之外，Nuxt 还使用了 webpack 的其他出色功能和插件，比如静态文件和资源服务（资源管理），热模块替换，CSS 提取（`extract-css-chunks-webpack-plugin`），构建和监视时的进度条（webpackbar）等等。更多信息，请访问以下链接：

+   [`webpack.js.org/guides/code-splitting/`](https://webpack.js.org/guides/code-splitting/) 用于代码拆分

+   [`webpack.js.org/concepts/manifest/`](https://webpack.js.org/concepts/manifest/) 用于清单

+   [`webpack.js.org/guides/asset-management/`](https://webpack.js.org/guides/asset-management/) 用于资源管理

+   [`webpack.js.org/concepts/hot-module-replacement/`](https://webpack.js.org/concepts/hot-module-replacement/) 用于热模块替换

+   [`webpack.js.org/plugins/mini-css-extract-plugin/`](https://webpack.js.org/plugins/mini-css-extract-plugin/) 用于 CSS 提取

+   [`github.com/nuxt/webpackbar`](https://github.com/nuxt/webpackbar) 用于 `webpackbar`（Nuxt 核心团队开发的插件）

来自 webpack、Babel 和 Nuxt 本身的这些出色功能将使您的现代项目开发变得有趣且轻松。现在，让我们看看各种应用类型，看看在构建下一个 web 应用时，为什么应该或不应该使用 Nuxt。

# 应用类型

今天的 web 应用与几十年前的应用非常不同。在那些日子里，我们的选择和解决方案更少。今天，它们正在蓬勃发展。无论我们称它们为“应用”还是“应用程序”，它们都是一样的。在本书中，我们将称它们为“应用”。因此，我们可以将我们当前的 web 应用分类如下：

+   传统的服务器端渲染应用

+   传统的单页应用

+   通用 SSR 应用

+   静态生成的应用

让我们逐个了解它们，并了解其利弊。我们首先来看最古老的应用类型 - 传统的服务器端渲染应用。

## 传统的服务器端渲染应用

服务器端呈现是向浏览器客户端传递数据和 HTML 的最常见方法。在网络行业刚刚开始时，这是唯一的做事方式。在传统的服务器呈现的应用程序或动态网站中，每个请求都需要从服务器重新呈现新页面到浏览器。这意味着您将在每次发送请求到服务器时重新加载所有脚本、样式和模板。重新加载和重新呈现的想法一点也不吸引人。尽管如今可以通过使用 AJAX 来减轻一些重新加载和重新呈现的负担，但这会给应用程序增加更多复杂性。

让我们来看看这些类型应用程序的优缺点。

**优势**：

+   **更好的 SEO 性能：**因为客户端（浏览器）得到了包含所有数据和 HTML 标记的完成页面，特别是属于页面的元标记，搜索引擎可以爬取页面并对其进行索引。

+   **更快的初始加载时间：**因为页面和内容是由服务器端脚本语言（如 PHP）在发送到客户端浏览器之前在服务器端呈现的，所以我们在客户端很快就能得到呈现的页面。此外，无需像传统的单页应用程序那样在 JavaScript 文件中编译网页和内容，因此应用程序在浏览器上加载更快。

**缺点**：

+   **用户体验较差：**因为每个页面都必须重新呈现，这个过程在服务器上需要时间，用户必须等待直到在浏览器上重新加载所有内容，这可能会影响用户体验。大多数情况下，我们只希望在提供新请求时获得新数据；我们不需要重新生成 HTML 基础，例如导航栏和页脚，但仍然会重新呈现这些基本元素。我们可以利用 AJAX 来仅呈现特定组件，但这会使开发变得更加困难和复杂。

+   后端和前端逻辑的紧密耦合：视图和数据通常在同一个应用程序中处理。例如，在典型的 PHP 框架应用程序中，如 Laravel，您可以在路由中使用模板引擎（如 Laravel Pug）渲染视图。或者，如果您正在为传统的服务器端渲染应用程序使用 Express，您可以使用模板引擎（如 Pug 或 vuexpress）来渲染视图。在这两个框架中，视图与后端逻辑耦合在一起，即使我们可以使用模板引擎提取视图层。后端开发人员必须知道每个特定路由或控制器要使用的视图（例如`home.pug`）。另一方面，前端开发人员必须在与后端开发人员相同的框架中处理视图。这给项目增加了更多复杂性。

## 传统的单页面应用程序（SPA）

与服务器端渲染应用程序相反，SPA 是客户端渲染（CSR）应用程序，它使用 JavaScript 在浏览器中渲染内容，而不需要在使用过程中重新加载新页面。因此，您不会将内容呈现到 HTML 文档中，而是从服务器获取基本的 HTML，然后在浏览器中使用 JavaScript 加载内容。

```js
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <title>Vue App</title>
</head>
<body>
  <div id="app"></div>
  <script src="https://unpkg.com/vue/dist/vue.js" type="text/javascript"></script>
  <script src="/path/to/app.js"type="text/javascript"></script>
</body>
</html>
```

这是一个非常简单的 Vue 应用程序，其中您有一个容器`<div>`，只有`app`作为其 ID，里面没有其他内容，然后是两个`<script>`元素。第一个`<script>`元素将加载 Vue.js 库，而第二个将加载渲染应用程序内容的 Vue 实例。

```js
// path/to/app.js
const app = new Vue({
  data: {
    greeting:'Hello World!'
  },
  template: '<p>{{ greeting }}</p>'
}).$mount('#app')
```

让我们来看看这种类型应用程序的优缺点。

优点：

+   更好的用户体验：SPA 在初始加载后渲染内容时非常快速。大多数资源，如 CSS 样式、JavaScript 代码和 HTML 模板，在应用程序的整个生命周期中只加载一次。之后只是来回发送数据；基本的 HTML 和布局保持不变，从而提供了流畅和更好的用户体验。

+   **开发和部署更容易：** 单页应用程序的开发相对容易，无需服务器和服务器端脚本语言。您可以从本地机器上简单地启动开发，使用`file://URI`。部署也更容易，因为它由 HTML、JavaScript 和 CSS 文件组成；您只需将它们放到远程服务器上，立即上线。

**缺点：**

+   **搜索引擎性能差：** 单页应用程序通常是裸骨的单个 HTML 页面，大多数情况下没有标题和段落标签供搜索引擎爬虫爬取。SPA 内容是通过 JavaScript 加载的，而爬虫通常无法执行 JavaScript，因此 SPA 在搜索引擎优化方面通常表现不佳。

+   **初始加载时间慢：** 大多数资源，如 CSS 样式、JavaScript 代码和 HTML 模板，在应用程序的整个生命周期中只加载一次，因此我们需要在开始时一次性加载大量这些资源文件。通过这样做，应用程序通常在初始加载时间方面变慢，特别是在大型单页应用程序中。

## 通用服务器端渲染应用（SSR）

正如我们在前一节中所学到的，传统的服务器端渲染应用程序和单页应用程序都有优点和缺点。编写单页应用程序有好处，但也有一些你会失去的东西：网络爬虫遍历您的应用程序的能力以及应用程序初始加载时的性能较慢。这与编写传统的服务器端渲染应用程序相反，还有一些你没有的东西，比如更好的用户体验和单页应用程序中客户端开发的乐趣。理想情况下，客户端和服务器端渲染可以平衡用户体验和性能。这就是通用服务器端渲染（SSR）的用武之地。

自从 2009 年 Node.js 发布以来，JavaScript 已经成为一种等同的语言。通过等同，我们指的是代码可以在客户端和服务器端都运行。等同（通用）JavaScript 可以被定义为客户端和服务器端应用程序的混合体。这是网页应用程序的一种新方法，以弥补传统 SSR 应用程序和传统 SPA 的缺点。这就是 Nuxt 所属的类别。

在通用 SSR 中，您的应用程序将首先在服务器端预加载，预渲染页面，并在切换到客户端操作其余寿命之前将呈现的 HTML 发送到浏览器。从头开始构建通用 SSR 应用程序可能会很繁琐，因为在实际开发过程开始之前需要大量的配置。这就是 Nuxt 的目标，它通过为您预设创建 SSR Vue 应用程序所需的所有配置来轻松实现。

尽管通用 SSR 应用程序在我们现代的 Web 开发中是一个很好的解决方案，但这些类型的应用程序仍然有优点和缺点。让我们来看看它们。

**优点**：

+   **更快的初始加载时间：**在通用 SSR 中，JavaScript 和 CSS 被分割成块，资源被优化，并且页面在服务器端呈现后再提供给客户端浏览器。所有这些选项都有助于加快初始加载时间。

+   **更好的 SEO 支持：**由于所有页面在服务器端呈现时都带有适当的元内容、标题和段落，然后再提供给客户端，搜索引擎爬虫可以遍历页面，以提高应用程序的 SEO 性能。

+   **更好的用户体验：**通用 SSR 应用程序在初始加载后的工作方式类似于传统的 SPA，因为页面和路由之间的转换是无缝的。只有数据来回传输，而不重新渲染 HTML 内容持有者。所有这些功能都有助于提供更好的用户体验。

**缺点**：

+   **需要 Node.js 服务器：**在服务器端运行 JavaScript 需要一个 Node.js 服务器，因此在使用 Nuxt 和编写应用程序之前必须设置服务器。

+   **复杂的开发：**在通用 SSR 应用程序中运行 JavaScript 代码可能会令人困惑，因为一些 JavaScript 插件和库只能在客户端运行，比如用于样式和 DOM 操作的 Bootstrap 和 Zurb Foundation。

## 静态生成的应用程序

静态生成的应用程序是通过静态站点生成器预先生成的，并存储为静态 HTML 页面在托管服务器上。Nuxt 带有一个`nuxt generate`命令，可以为您从您在 Nuxt 中开发的通用 SSR 或 SPA 应用程序生成**静态页面**。它在构建过程中为每个路由预渲染 HTML 页面到生成的`/dist/`文件夹中，如下所示：

```js
-| dist/
----| index.html
----| favicon.ico
----| about/
------| index.html
----| contact/
------| index.html
----| _nuxt/
------| 2d3427ee2a5aa9ed16c9.js
------| ...
```

您可以将这些静态文件部署到静态托管服务器，而无需 Node.js 或任何服务器端支持。因此，当应用程序最初在浏览器上加载时 - 无论您请求的是哪个路由 - 您都将立即获得完整的内容（如果它是从通用 SSR 应用程序中导出的），之后应用程序将像传统的单页面应用程序一样运行。

让我们来看看这些类型应用程序的优势和劣势。

**优势：**

+   **快速的初始加载时间：** 由于每个路由都被预先生成为具有自己内容的静态 HTML 页面，因此在浏览器上加载速度很快。

+   **有利于 SEO：** 静态生成的 Web 应用程序允许您的 JavaScript 应用程序被搜索引擎完美地抓取，就像传统的服务器端渲染应用程序一样。

+   **部署更容易：** 因为静态生成的 Web 应用程序只是静态文件，这使它们易于部署到静态托管服务器，如 GitHub Pages。

**劣势：**

+   **没有服务器端支持：** 因为静态生成的 Web 应用程序只是静态 HTML 页面，并且仅在客户端上运行，这意味着没有运行时支持 Nuxt 的`nuxtServerInit`动作方法和 Node.js HTTP 请求和响应对象，这些仅在服务器端可用。所有数据将在构建步骤中预先呈现。

+   **没有实时渲染：** 静态生成的 Web 应用程序适用于只提供**静态页面**的应用程序，这些页面在**构建时**预先呈现。如果您正在开发一个需要从服务器实时渲染的复杂应用程序，那么您应该使用通用 SSR 来充分利用 Nuxt 的全部功能。

从这些类别中，你可能已经发现 Nuxt 符合通用 SSR 应用程序和静态生成的应用程序。除此之外，它也符合单页面应用程序，但与传统的单页面应用程序不同，你将在第十五章“使用 Nuxt 创建单页面应用程序”中了解更多信息。

现在，让我们更好地了解 Nuxt 在本书中将要创建的应用程序类型。我们将从 Nuxt 作为通用 SSR 应用程序开始。

# Nuxt 作为通用 SSR 应用程序

许多年前，我们有服务器端脚本语言，如 ASP、Java、服务器端 JavaScript、PHP 和 Python 来创建具有模板引擎的传统服务器端应用程序来渲染我们应用程序的视图。这导致了我们在前一节中经历的紧耦合的缺点。

因此，随着 Nuxt、Next（[`nextjs.org/`](https://nextjs.org/)）和 Angular Universal（[`angular.io/guide/universal`](https://angular.io/guide/universal)）等通用 SSR 框架的兴起，我们可以充分利用它们的力量，通过替换模板引擎（如 Pug（[`pugjs.org/`](https://pugjs.org/)）、Handlebars（[`handlebarsjs.com/`](https://handlebarsjs.com/)）、Twig（[`twig.symfony.com/`](https://twig.symfony.com/)）等）来彻底解耦视图和服务器端脚本应用。如果我们将 Nuxt 视为**前端服务器端**应用程序，Express（或其他）视为**后端服务器端**应用程序，我们可以看到它们如何完美地互补。例如，我们可以使用 Express 在 API 路由（例如`/`）上创建一个**后端服务器端**应用程序，以 JSON 格式提供数据，位于`localhost:4000`上。

```js
{
  "message": "Hello World"
}
```

然后，在前端服务器端，我们可以使用 Nuxt 作为一个通用的 SSR 应用程序，在`localhost:3000`上运行，通过从 Nuxt 应用程序中的页面发送 HTTP 请求来消耗上述数据，如下所示：

```js
// pages/index.vue
async asyncData ({ $http }) {
  const { message } = await $http.$get('http://127.0.0.1:4000')
  return { message }
}
```

现在，我们将 Nuxt 作为服务器和客户端来处理我们应用的视图和模板，而 Express 只处理我们的服务器端逻辑。我们不再需要模板引擎来呈现我们的内容。因此，也许我们不需要学习那么多模板引擎，也不需要担心它们之间的竞争，因为现在我们有了通用的 Nuxt。

我们将向您展示如何使用 Nuxt 和 Koa（另一个类似于 Express 的 Node.js 服务器端框架）创建跨域应用程序，详见第十二章，*创建用户登录和 API 身份验证*。

请注意，在上述代码中，我们使用了 Nuxt HTTP 模块来发出 HTTP 请求。然而，在本书中，我们将主要使用原始的 Axios 或 Nuxt Axios 模块来进行 HTTP 请求。有关 Nuxt HTTP 模块的更多信息，请访问[`http.nuxtjs.org/`](https://http.nuxtjs.org/)。

您还可以使用 Nuxt Content 模块作为无头 CMS，以便从 Markdown、JSON、YAML、XML 和 CSV 文件中提供应用程序内容，这些文件可以“本地”存储在您的 Nuxt 项目中。但是，在本书中，我们将使用和创建外部 API 来提供我们的内容，以避免我们在传统服务器端应用程序中发现的紧密耦合问题。有关 Nuxt Content 模块的更多信息，请访问[`content.nuxtjs.org/`](https://content.nuxtjs.org/)。

# Nuxt 作为静态站点生成器

尽管服务器端渲染是 Nuxt 的主要特性，但它也是一个静态站点生成器，可以在静态站点中预渲染您的 Nuxt 应用程序，就像静态生成的应用程序类别中提供的示例一样。它可能是传统单页面应用程序和服务器端渲染应用程序之间最好的结合。通过静态 HTML 内容获益，以获得更好的 SEO，您不再需要来自 Node.js 和 Nuxt 的运行时支持。但是，您的应用程序仍将像 SPA 一样运行。

更重要的是，在静态生成期间，Nuxt 具有一个爬虫，用于爬取应用程序中的链接以生成动态路由，并将它们的数据从远程 API 保存为`payload.js`文件，存储在`/dist/`文件夹内的`/static/`文件夹中。然后使用这些负载来提供最初从 API 请求的数据。这意味着您不再调用 API。这可以保护您的 API 免受公众和可能的攻击者的侵害。

您将学习如何在第十四章中使用远程 API 从 Nuxt 生成静态站点，以及在本书的最后一章第十八章中创建具有 CMS 和 GraphQL 的 Nuxt 应用程序。

# Nuxt 作为单页面应用程序

如果您有任何原因不希望将 Nuxt 用作服务器端渲染应用程序，那么 Nuxt 非常适合开发单页面应用程序。正如我们在本章开头提到的，Nuxt 有两种开发应用程序的模式：`universal`和`spa`。这意味着您只需在项目配置的`mode`属性中指定`spa`，我们将在下一章中更详细地探讨这一点。

因此，你可能会想，如果我们可以使用 Nuxt 开发 SPA，那为什么还要费心使用 Vue 呢？事实上，你从 Nuxt 开发的 SPA 与从 Vue 开发的 SPA 略有不同。你从 Vue 构建的 SPA 是传统的 SPA，而从 Nuxt 构建的 SPA 是“静态”SPA（让我们称之为 Nuxt SPA）——你的应用页面在构建时进行了预渲染。这意味着部署 Nuxt SPA 在技术上与静态生成 Nuxt 通用 SSR 应用是相同的——两者都需要相同的 Nuxt 命令：`nuxt generate`。

这可能会让人感到困惑，你可能想问静态生成的 SSR 应用和静态生成的 SPA 之间有什么区别？区别非常明显——与静态生成的 SSR 应用相比，静态生成的 SPA 没有页面内容。静态生成的 SPA 是使用你的应用页面和“空”HTML 预渲染的，就像传统的 SPA 一样——没有页面内容。这很令人困惑，但请放心，我们将在本书的后续章节中弄清楚这一切。特别是，你将了解在 Nuxt 中开发 SPA 的权衡以及如何克服它们。

你将学习如何开发 Nuxt SPA，并在第十五章中使用远程 API 生成静态 Nuxt SPA，*使用 Nuxt 创建 SPA*。

# 总结

干得好！你已经完成了进入 Nuxt 的旅程的第一章。在本章中，你了解了 Nuxt 框架的组成部分；即 Vue（Nuxt 的起源）、webpack 和 Babel。你了解了 Nuxt 提供的各种功能，比如你可以编写 Vue 单文件组件（`.vue`文件）、ES2015+ JavaScript（ES6）、使用预处理器的 CSS（Sass、Less、Stylus）。你还可以通过模块和插件扩展你的应用，为应用的路由添加过渡效果，管理`<head>`元素和应用中每个路由或页面的元内容。除此之外，你还涵盖了从 webpack 和 Babel 导入的大量出色功能，比如打包、压缩和代码分割。你还了解到，你可以从 Nuxt 社区获取大量插件和模块用于你的 Nuxt 项目。

除了这些强大的功能之外，您还了解了每种可用应用类型的优缺点：传统的服务器端渲染应用程序、传统的单页面应用（SPA）、通用服务器端渲染应用程序（SSR）和静态生成应用程序。您还了解到 Nuxt 应用实际上符合通用 SSR 应用程序和静态生成应用程序的类别。然后，您了解到 Nuxt 也符合单页面应用的类别，但与传统的 SPA 不同。最后，您还了解了如何在本书中更多地了解如何使用 Nuxt 来进行通用 SSR 应用程序、静态生成应用程序和单页面应用。

在下一章中，您将学习如何安装 Nuxt 并创建一个简单的 Nuxt 应用程序，并了解 Nuxt 脚手架工具提供的默认目录结构。您还将学习如何自定义您的 Nuxt 应用程序，并了解 Nuxt 中提供的资源。所以，请继续关注！
