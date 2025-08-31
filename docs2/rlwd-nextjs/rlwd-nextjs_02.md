# *第一章*：Next.js 简明介绍

Next.js 是一个开源的 JavaScript 网络框架，为 React 提供了一套丰富的内置功能，例如服务器端渲染、静态站点生成和增量静态再生。这些只是使 Next.js 成为既适用于企业级应用又适用于小型网站的框架的许多内置组件和插件中的少数几个。

本书旨在在构建真实世界的应用和用例（如电子商务网站和博客平台）的同时，向您展示该框架的全部潜力。您将学习 Next.js 的基础知识，如何在不同渲染策略和部署方法之间进行选择，以及如何使您的网络应用既可扩展又易于维护的技巧和方法。

在本章中，我们将涵盖以下主题：

+   Next.js 框架简介

+   将 Next.js 与其他流行的替代方案进行比较

+   Next.js 与客户端 React 之间的区别

+   默认 Next.js 项目的结构

+   如何使用 TypeScript 开发 Next.js 应用

+   如何自定义 Babel 和 webpack 配置

# 技术要求

要开始使用 Next.js，您需要在您的机器上安装一些依赖项。

首先，您需要安装**Node.js**和**npm**。如果您需要安装它们的详细指南，请参阅这篇博客文章：[`www.nodejsdesignpatterns.com/blog/5-ways-to-install-node-js`](https://www.nodejsdesignpatterns.com/blog/5-ways-to-install-node-js)。

如果您不想在本地机器上安装 Node.js，一些在线平台将允许您免费使用在线 IDE 跟随本书中的代码示例，例如[`codesandbox.io`](https://codesandbox.io)和 https://repl.it。

一旦您安装了 Node.js 和 npm（或者您正在使用在线环境），您只需遵循本书每个部分显示的说明，使用 npm 安装所需的项目特定依赖项。

您可以在以下 GitHub 仓库中找到完整的代码示例：[`github.com/PacktPublishing/Real-World-Next.js`](https://github.com/PacktPublishing/Real-World-Next.js)。请随意分支、克隆和编辑此仓库，以进行任何 Next.js 的实验。

# 介绍 Next.js

在过去的几年里，网络开发发生了很大的变化。在现代 JavaScript 框架出现之前，创建动态网络应用非常复杂，需要许多不同的库和配置才能按预期工作。

Angular、React、Vue 以及其他框架使网络发展非常迅速，并为前端网络开发带来了许多非常创新的想法。

React 最初是由 *乔丹·沃尔克* 在 Facebook 创建的，并受到了 *XHP Hack Library* 的强烈影响。XHP 允许 Facebook 的 PHP 和 Hack 开发者为其应用程序的前端创建可重用组件。该 JavaScript 库于 2013 年开源，并永远改变了我们构建网站、网络应用、原生应用（后来是 **React Native**）以及甚至 VR 体验（后来是 **React VR**）的方式。因此，React 迅速成为最受欢迎和流行的 JavaScript 库之一，数百万个网站在生产中使用它用于各种不同的目的。

只有一个问题：默认情况下，React 在 **客户端** 运行（这意味着它在网页浏览器上运行），因此完全使用该库编写的网络应用可能会对 **搜索引擎优化**（**SEO**）和初始加载性能产生负面影响，因为它需要一些时间才能正确渲染到屏幕上。实际上，为了显示完整的网络应用，浏览器必须下载整个应用包，解析其内容，然后执行它并在浏览器中渲染结果，这可能需要几秒钟（对于非常大的应用）。

许多公司和开发者开始研究如何在服务器端预渲染应用，让浏览器显示渲染后的 React 应用作为纯 HTML，一旦 JavaScript 包被传输到客户端，它就可以立即交互。

然后，**Vercel** 推出了 Next.js，它最终成为了一个颠覆性的产品。

自从首次发布以来，该框架已经提供了一些开箱即用的创新功能，例如自动代码拆分、服务器端渲染、基于文件的路由系统、路由预取等等。Next.js 通过允许开发者编写用于客户端和服务器两端的可重用代码，以及使非常复杂的工作（如代码拆分和服务器端渲染）变得容易实现，展示了编写通用网络应用应该是多么简单。

今天，Next.js 提供了大量的新功能，例如以下内容：

+   静态站点生成

+   渐进式静态生成

+   原生 TypeScript 支持

+   自动填充

+   图片优化

+   国际化支持

+   性能分析

所有这些，再加上本书后面我们将深入探讨的许多其他优秀功能。

今天，Next.js 被 Netflix、Twitch、TikTok、Hulu、Nike、Uber、Elastic 等顶级公司用于生产。如果你感兴趣，可以阅读完整的列表，网址为 https://nextjs.org/showcase。

Next.js 展示了 React 在构建任何规模的不同应用时的多功能性，它被大公司和小型初创公司使用并不令人惊讶。顺便说一下，它并不是唯一允许你在服务器端渲染 JavaScript 的框架，正如我们将在下一节中看到的。

# 将 Next.js 与其他替代方案进行比较

如你所想，Next.js 并不是服务器端渲染 JavaScript 世界中的唯一玩家。然而，根据项目的最终目的，可能会考虑其他替代方案。

## Gatsby

一个流行的替代方案是 Gatsby。如果你想要构建静态网站，你可能需要考虑这个框架。与 Next.js 不同，Gatsby 只支持静态网站生成，并且做得非常出色。每个页面在构建时都会预先渲染，并且可以作为静态资源在任何 **内容分发网络** (**CDN**) 上提供服务，这使得性能与动态服务器端渲染的替代方案相比具有极高的竞争力。使用 Gatsby 而不是 Next.js 的最大缺点是，你将失去动态服务器端渲染的能力，这对于构建更动态、数据驱动和复杂的网站来说是一个重要的功能。

## Razzle

相比 Next.js，Razzle 的知名度较低，它是一个用于创建服务器端渲染 JavaScript 应用的工具。它的目标是保持 `create-react-app` 的易用性，同时抽象出在服务器和客户端渲染应用所需的所有复杂配置。使用 Razzle 而不是 Next.js（或以下替代方案）的最显著优势是它不依赖于任何框架。你可以选择你喜欢的前端框架（或语言），例如 React、Vue、Angular、Elm 或 Reason-React…由你选择。

## Nuxt.js

如果你熟悉 Vue，那么 Nuxt.js 可以成为 Next.js 的一个有效竞争对手。它们都提供了服务器端渲染、静态网站生成、渐进式 Web 应用管理等功能，在性能、SEO 或开发速度方面没有显著差异。虽然 Nuxt.js 和 Next.js 都服务于相同的目的，但 Nuxt.js 需要更多的配置，这有时并不是一件坏事。在你的 Nuxt.js 配置文件中，你可以定义布局、全局插件和组件、路由等，而使用 Next.js，你需要按照 *React 方式* 来做。除此之外，它们共享许多功能，但最显著的区别是底层的库。也就是说，如果你已经有一个 Vue 组件库，你可以考虑使用 Nuxt.js 来进行服务器端渲染。

## Angular Universal

当然，Angular 也已经进入了 JavaScript 服务器端渲染的领域，并提出了 Angular Universal 作为官方的 Angular 应用服务器端渲染方式。它支持静态网站生成和服务器端渲染，与 Nuxt.js 和 Next.js 不同，它是由世界上最大的公司之一 Google 开发的。所以如果你从事 Angular 开发并且已经使用该库编写了一些组件，Angular Universal 可以是 Nuxt.js、Next.js 和其他类似框架的自然替代方案。

## 那么，为什么选择 Next.js 呢？

我们现在已经看到了一些 Next.js 的流行替代方案，以及它们的优缺点。

我建议使用 Next.js 而不是其他任何框架的主要原因是因为它令人难以置信的功能集。使用 Next.js，你将获得所有你需要的东西，直接从盒子里出来，我不仅指的是组件、配置和部署选项，尽管它们可能是我所见过的最完整的。

此外，Next.js 拥有一个非常友好和活跃的社区，随时准备在你构建应用程序的每一步提供支持。我会把这看作是一个巨大的加分项，因为一旦你在代码库中遇到问题，你将能够从遍布许多不同平台的庞大社区中获得帮助，包括 StackOverflow 和 GitHub，Vercel 团队也经常参与讨论和支持请求。

现在你已经知道了 Next.js 如何与其他类似框架竞争，让我们看看默认客户端 React 应用程序和具有完整功能的用于动态渲染你的 JavaScript 代码库的每个请求以及构建时的静态环境的区别。

# 从 React 迁移到 Next.js

如果你已经有一些 React 的经验，你会发现构建你的第一个 Next.js 网站非常容易。它的哲学与 React 非常接近，并为大多数设置提供了一种 *约定优于配置* 的方法，所以如果你想利用特定的 Next.js 功能，你将很容易找到官方的做法来做，而不需要任何复杂的配置。一个例子？在一个 Next.js 应用中，你可以在构建时指定哪些页面应该进行服务器端渲染，哪些应该进行静态生成，而不需要编写任何配置文件或其他类似的东西。你只需要从你的页面导出一个特定的函数，让 Next.js 做它的魔法（我们将在 *第二章**，探索不同的渲染策略*中看到）。

React 和 Next.js 之间最显著的区别在于，虽然 React 只是一个 JavaScript 库，但 Next.js 是一个用于在客户端和服务器端构建丰富和完整用户体验的框架，增加了大量极其有用的功能。每个服务器端渲染或静态生成的页面都将运行在 Node.js 上，因此你将失去访问一些浏览器特定的全局对象，例如 `fetch`、`window` 和 `document`，以及一些 HTML 元素，如 `canvas`。当你编写 Next.js 页面时，你始终需要记住这一点，即使框架提供了处理必须使用此类全局变量和 HTML 元素的组件的自己的方式，正如我们将在*第二章**，探索不同的渲染策略*中看到的那样。

另一方面，可能会有时候你想使用 Node.js 特定的库或 API，例如`fs`或`child_process`，Next.js 允许你在将数据发送到客户端之前，通过在每个请求或构建时间（取决于你如何选择渲染你的页面）运行你的服务器端代码来使用它们。

即使你想创建一个客户端渲染的应用程序，Next.js 也可以成为知名`create-react-app`的一个很好的替代品。实际上，Next.js 可以轻松地用作编写渐进式和离线优先 Web 应用的框架，利用其惊人的内置组件和优化。那么，让我们开始使用 Next.js 吧。

# 开始使用 Next.js

现在我们对 Next.js 的使用案例和客户端 React 与其他框架之间的区别有了基本的了解，是时候看看代码了。我们将从创建一个新的 Next.js 应用并自定义其默认 webpack 和 Babel 配置开始。我们还将了解如何将 TypeScript 作为开发 Next.js 应用的主要语言。

## 默认项目结构

开始使用 Next.js 极其简单。唯一的要求是在你的机器（或开发环境）上安装 Node.js 和`npm`。Vercel 团队创建并发布了一个简单但强大的工具，名为`create-next-app`，用于生成基本 Next.js 应用的脚手架代码。你可以在终端中输入以下命令来使用它：

```js
npx create-next-app <app-name>
```

它将安装所有所需的依赖项并创建几个默认页面。此时，你只需运行`npm run dev`，开发服务器就会在端口`3000`上启动，显示一个着陆页。

如果你的机器上安装了 Yarn 包管理器，Next.js 将使用 Yarn 初始化你的项目。你可以通过传递一个标志来覆盖此选项，告诉`create-next-app`使用`npm`：

```js
npx create-next-app <app-name> --use-npm
```

你也可以要求`create-next-app`通过从 Next.js GitHub 仓库下载脚手架代码来初始化一个新的 Next.js 项目。实际上，在 Next.js 仓库中有一个`examples`文件夹，里面包含大量关于如何使用 Next.js 与不同技术结合的精彩示例。

假设你想在 Docker 上使用 Next.js 做一些实验——你只需将`--example`标志传递给脚手架代码生成器：

```js
npx create-next-app <app-name> --example with-docker
```

`create-next-app`将从[`github.com/vercel/next.js/tree/canary/examples/with-docker`](https://github.com/vercel/next.js/tree/canary/examples/with-docker)下载代码，并为你安装所需的依赖项。此时，你只需编辑下载的文件，进行定制，就可以开始了。

你可以在 https://github.com/vercel/next.js/tree/canary/examples 找到其他精彩示例。如果你已经熟悉 Next.js，请随意探索 Next.js 如何与不同的服务和工具包集成（我们将在本书的后续部分更详细地介绍一些）。

现在，让我们暂时回到默认的 `create-next-app` 安装。让我们打开终端，一起生成一个新的 Next.js 应用程序：

```js
npx create-next-app my-first-next-app --use-npm
```

几秒钟后，模板生成将成功完成，你将找到一个名为 `my-first-next-app` 的新文件夹，其结构如下：

```js
- README.md
- next.config.js
- node_modules/
- package-lock.json
- package.json
- pages/
  - _app.js
  - api/
    - hello.js
  - index.js
- public/
  - favicon.ico
  - vercel.svg
- styles/
  - Home.module.css
  - globals.css
```

如果你来自 React，你可能已经习惯了 `pages/` 目录。实际上，`pages/` 目录中的每个 JavaScript 文件都将是一个公开页面，所以如果你尝试复制 `index.js` 页面并将其重命名为 `about.js`，你将能够访问 `http://localhost:3000/about` 并看到你主页的精确副本。我们将在下一章详细探讨 Next.js 如何处理客户端和服务器端路由；现在，让我们只是把 `pages/` 目录看作是公开页面的容器。

`public/` 文件夹包含你网站中使用的所有公开和静态资产。例如，你可以将你的图片、编译后的 CSS 样式表、编译后的 JavaScript 文件、字体等放在那里。

默认情况下，你还会看到一个 `styles/` 目录；虽然这对于组织你的应用程序样式表非常有用，但对于 Next.js 项目来说并不是严格必需的。唯一必需且保留的目录是 `public/` 和 `pages/`，所以请确保不要删除或用于其他目的。

话虽如此，你可以在项目根目录中自由添加更多目录和文件，因为它不会对 Next.js 的构建或开发过程产生负面影响。如果你想将组件组织在 `components/` 目录下，将实用工具组织在 `utilities/` 目录下，请随意在你的项目中添加这些文件夹。

如果你不喜欢模板生成器，你可以通过添加所有必需的依赖（如之前所列）以及我们刚刚看到的基目录结构到现有的 React 应用程序中，来启动一个新的 Next.js 应用程序，这样它就可以直接工作，无需其他配置。

## TypeScript 集成

Next.js 的源代码是用 TypeScript 编写的，并在项目根目录中提供了高质量的 `tsconfig.json`。如果你尝试运行 `npm run dev`，你会看到以下输出：

```js
It looks like you're trying to use TypeScript but do not have the required package(s) installed.
Please install typescript and @types/react by running:
     npm install --save typescript @types/react
     If you are not trying to use TypeScript, please remove
     the tsconfig.json file from your package root (and any 
     TypeScript files in your pages directory).
```

正如你所见，Next.js 已经正确地检测到你正在尝试使用 TypeScript，并要求你安装所有作为项目主要语言所需依赖。因此，你现在只需将你的 JavaScript 文件转换为 TypeScript，就可以开始使用了。

你可能会注意到，即使你创建了一个空的 `tsconfig.json` 文件，在安装了必需的依赖并重新运行项目后，Next.js 也会用它的默认配置填充它。当然，你总是可以自定义该文件中的 TypeScript 选项，但请记住，Next.js 使用 Babel 来处理 TypeScript 文件（通过 `@babel/plugin-transform-typescript`），并且有一些注意事项，包括以下内容：

+   `@babel/plugin-transform-typescript` 插件不支持 TypeScript 中常用的 `const enum`，为了支持它，请确保将 `babel-plugin-const-enum` 添加到 Babel 配置中（我们将在 *自定义 Babel 和 webpack 配置* 部分看到如何操作）。

+   既不支持 `export =` 也不支持 `import =`，因为它们无法编译成有效的 ECMAScript 代码。你应该安装 `babel-plugin-replace-ts-export-assignment`，或者将你的导入和导出转换为有效的 ECMAScript 指令，例如 `import x, {y} from 'some-package'` 和 `export default x`。

还有其他注意事项，我建议你在进一步使用 TypeScript 作为 Next.js 应用程序的主要开发语言之前阅读它们：[`babeljs.io/docs/en/babel-plugin-transform-typescript#caveats`](https://babeljs.io/docs/en/babel-plugin-transform-typescript#caveats)。

此外，一些编译器选项可能与默认的 TypeScript 选项略有不同；再次建议你阅读官方的 Babel 文档，它将始终是最新的：[`babeljs.io/docs/en/babel-plugin-transform-typescript#typescript-compiler-options`](https://babeljs.io/docs/en/babel-plugin-transform-typescript#typescript-compiler-options)。

Next.js 还会在你的项目根目录下创建一个 `next-env.d.ts` 文件；如果你需要，可以自由编辑它，但请确保不要删除它。

## 自定义 Babel 和 webpack 配置

如同在 *TypeScript 集成* 部分已提到的，我们可以自定义 **Babel** 和 **webpack** 配置。

我们可能有很多原因想要自定义 Babel 配置。如果你不太熟悉它，让我快速解释一下我在说什么。Babel 是一个 JavaScript 转换编译器，主要用于将现代 JavaScript 代码转换为向后兼容的脚本，这样它就可以在任何浏览器上无问题地运行。

如果你正在编写一个必须支持旧版浏览器（如 **Internet Explorer** （**IE**）10 或 Internet Explorer 11）的 Web 应用程序，Babel 会对你有很大帮助。它允许你使用现代 ES6/ESNext 功能，并在构建时将它们转换为与 IE 兼容的代码，让你在几乎不妥协的情况下保持良好的开发者体验。

此外，JavaScript 语言（在 ECMAScript 规范下标准化）正在快速发展。因此，尽管一些出色的功能已经宣布，但你可能需要等待数年才能在浏览器和 Node.js 环境中使用它们。这是因为 ECMA 委员会接受这些功能后，开发 Web 浏览器的公司和在 Node.js 项目上工作的社区必须制定一个路线图，以添加对这些增强功能的支持。Babel 通过将现代代码转换为适用于今天环境的兼容脚本来解决此问题。

例如，你可能熟悉以下代码：

```js
export default function() {
  console.log("Hello, World!");
};
```

但如果您尝试在 Node.js 中运行它，它将抛出一个语法错误，因为 JavaScript 引擎不会识别 `export default` 关键字。

Babel 将将前面的代码转换为等效的 ECMAScript 代码，至少直到 Node.js 支持 `export default` 语法：

```js
"use strict";
Object.defineProperty(exports, "__esModule", {
  value: true
});
exports.default = _default;
function _default() {
  console.log("Hello, World!");
};
```

这使得在 Node.js 上运行此代码没有任何问题。

您可以通过在项目根目录下创建一个名为 `.babelrc` 的新文件来自定义 Next.js 的默认 Babel 配置。您会注意到，如果您将其留空，Next.js 的构建/开发过程将抛出错误，因此请确保至少添加以下代码：

```js
{
  "presets": ["next/babel"]
}
```

这是 Vercel 团队专门为构建和开发 Next.js 应用程序而创建的 Babel 预设。假设我们正在构建一个应用程序，并且我们想使用实验性的 ECMAScript 功能，例如管道操作符；如果您不熟悉它，它基本上允许您将此代码重写如下：

```js
console.log(Math.random() * 10);
// written using the pipeline operator becomes:
Math.random()
  |> x => x * 10
  |> console.log
```

此操作符尚未被 **TC39**（ECMAScript 规范背后的技术委员会）官方接受，但您可以通过 Babel 开始使用它。

要在您的 Next.js 应用中提供对这个操作符的支持，您只需要使用 `npm` 安装 Babel 插件：

```js
npm install --save-dev @babel/plugin-proposal-pipeline-operator @babel/core
```

然后按照以下方式更新您的自定义 `.babelrc` 文件：

```js
{
  "presets": ["next/babel"],
  "plugins": [
    [
      "@babel/plugin-proposal-pipeline-operator",
      { "proposal": "fsharp" }
    ]
  ]
}
```

您现在可以重新启动开发服务器并使用这个实验性功能。

如果您有兴趣将 TypeScript 作为 Next.js 应用的主要开发语言，您可以遵循相同的步骤将所有 TypeScript 特定插件添加到您的 Babel 配置中。在您的 Next.js 开发过程中，您可能还希望自定义默认的 webpack 配置。

虽然 Babel 只接受现代代码作为输入并生成向后兼容的脚本作为输出，但 webpack 会创建包含特定库、页面或功能的编译代码的包。例如，如果您创建了一个包含来自三个不同库的三个组件的页面，webpack 将将所有内容合并成一个单独的包发送到客户端。简单来说，我们可以将 webpack 视为为每个网络资源（JavaScript 文件、CSS、SVG 等）的不同编译、打包和压缩任务进行编排的基础设施。

如果您想使用 **SASS** 或 **LESS** 等 CSS 预处理器来创建您的应用程序样式，您需要自定义默认的 webpack 配置以解析 **SASS**/**LESS** 文件并生成纯 CSS 作为输出。当然，对于使用 Babel 作为转换器的 JavaScript 代码也是如此。

我们将在后续章节中更详细地讨论 CSS 预处理器，但就目前而言，我们只需要记住 Next.js 提供了一种简单的方式来自定义默认的 webpack 配置。

如我们之前所见，Next.js 提供了一种基于约定的配置方法，因此你不需要为构建实际应用而自定义其大部分设置；你只需遵循一些代码约定即可。

但如果你真的需要构建一些自定义的东西，你通常可以通过编辑`next.config.js`文件来修改默认设置。你可以在项目的根目录下创建这个文件。默认情况下，它应该导出一个对象，其中它的属性将覆盖 Next.js 的默认配置：

```js
module.exports = {
  // custom settings here
};
```

你可以通过在这个对象中创建一个新的属性来自定义默认的 webpack 配置，称为`webpack`。假设我们想要添加一个名为`my-custom-loader`的新虚拟 webpack 加载器；我们可以按照以下步骤进行：

```js
module.exports = {
  webpack: (config, options) => {
    config.module.rules.push({
      test: /\.js/,
      use: [
        options.defaultLoaders.babel,
        // This is just an example
        //don't try to run this as it won't work
        {
          loader: "my-custom-loader", // Set your loader
          options: loaderOptions, // Set your loader 
           options
        },
      ],
    });
    return config;
  },
};
```

因此，正如你所见，我们正在编写一个合适的 webpack 配置，稍后它将与 Next.js 的默认设置合并。这将允许我们扩展、覆盖或甚至删除默认配置中的任何设置，尽管删除默认设置通常不是一个好主意，但在某些情况下你可能需要这样做（如果你足够勇敢的话！）。

# 摘要

在本章中，你已经看到了默认和客户端 React 应用以及 Next.js 之间的主要区别，以及 Next.js 与其他知名替代方案的比较。你还学习了如何通过编辑 Babel 和 webpack 配置以及添加 TypeScript 作为 JavaScript 的替代品来定制默认的 Next.js 项目。

在下一章中，我们将更详细地探讨三种不同的渲染策略：客户端渲染、服务器端渲染和静态站点生成。
