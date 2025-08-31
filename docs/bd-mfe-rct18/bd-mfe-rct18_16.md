

# 第十一章：微前端的最新趋势

前端工程的世界不断演变，当我们根据目前可用的工具、方法和最佳实践构建微前端时，关注这个领域正在演变的最新的趋势，并不断探索和实验它们，以了解它们如何帮助我们提高效率并构建更好的应用程序，这一点非常重要。

在本章中，我们将探讨一些可能影响我们未来构建微前端趋势。我们将探讨的一些趋势如下：

+   术语*微前端*本身以及更好的术语是什么

+   混合静态内容和动态内容的孤岛模式

+   查看 Webpack 之外的构建工具

+   WebAssembly

+   云或边缘函数

+   生成式 AI 如何影响我们的工作

到本章结束时，我们将了解影响我们构建微前端的前端工程领域的最新趋势。

# 微前端 – 解耦模块化前端

术语*微前端*显然已经变得非常流行，整本书都在使用它，但说实话，我一直觉得这个术语用得不好，而且不幸的是，它已经在社区中根深蒂固。正如提到过几次，*微前端*这个词导致了很多误解，导致了不良的架构模式，反而比好的影响更大。已经提出了一个新的建议，开始称它们为**可组合解耦前端**([`microfrontend.dev/`](https://microfrontend.dev/))，我认为这是恰当的，并且清楚地解释了我们正在构建的内容的意图和目的。我真心希望社区开始采用这个术语，并且我们共同开始转向构建和称呼微前端为它们真正是的样子，并定义它们真正应该做什么。

我相信你们中的许多人会想知道仅仅改变名称如何有帮助，以及名称中真正包含的是什么；然而，我觉得在这种情况下，一个清楚地阐述架构模式的名称可以大大减少由错误架构的系统引起的误解、误释和复杂性。正如你们通过本书的学习过程所意识到的那样，这全部关于构建模块化应用程序，这些应用程序相互解耦，因此它们应该正确地被称为**解耦** **模块化前端**。

# 孤岛模式

静态生成的页面越来越受欢迎，因为它们几乎不包含 JavaScript；然而，它们的挑战始终在于如何提供动态内容。

**孤岛模式**旨在解决这个问题。它由 Astro 构建框架推广，其中我们的应用程序被发布为一系列静态生成的 HTML 页面，页面的动态部分作为孤岛导入。

这里有一个使用 Astro 的例子，Astro 是一个用于构建静态网站的流行框架，展示如何实现这一点。

你可以在 [`docs.astro.build/en/concepts/islands/`](https://docs.astro.build/en/concepts/islands/) 上了解更多关于此的信息。

```js
//index file
---
// Example: Use a dynamic React component on the page.
import MyReactComponent from '../components/MyReactComponent.jsx';
---
<!-- This component is now interactive on the page!
     The rest of your website remains static and zero JS. -->
<MyReactComponent client:load />
```

运行 Astro 构建命令，在本地测试应用，并查看你的 `Inspect` 命令；你会注意到，尽管页面的其余部分是带有少量或没有 JavaScript 的纯 HTML，但 `MyReactComponent` 是一个小的 JavaScript 元素，并在客户端执行。

如你所见，使用岛屿模式，我们可以在静态和动态内容之间获得清晰的区分，并且可能带来不将所有应用部分锁定到单个框架的额外好处。

话虽如此，岛屿模式和微前端之间有一些差异，包括以下内容：

+   Astro 中的岛屿是客户端的水合/渲染组件，而微前端是具有自己的代码库、路由和后端的独立应用。微前端更加隔离和松耦合。

+   Astro 在构建时构建整个应用和岛屿。微前端是独立构建和部署的。Astro 有统一的构建，而微前端可以有独立的构建。

+   Astro 中的路由发生在外壳中，而每个微前端管理自己的路由。Astro 岛屿没有独立的路由。

+   Astro 岛屿可以通过 Astro 集成相互通信，而微前端通常通过定义良好的 API 和事件进行通信。岛屿与 Astro 应用有更紧密的耦合和集成。

# 超越 Webpack 与 ES 模块

随着基于 JavaScript 的框架的兴起，Webpack 的受欢迎程度上升，并成为所有 JavaScript 框架的事实上的模块打包器。然而，使用 Webpack 打包/编译大型应用可能会非常慢，手动配置以高效打包应用非常复杂。最近，一种利用 ES 模块的新一代打包工具在前端世界掀起了一场风暴，承诺编译速度比 Webpack 快 20 倍以上。

ES 模块是定义和导入 JavaScript 模块的标准方式。它们允许模块化代码组织，这可以使开发大型应用更容易，也更容易维护。ES 模块还提供了清晰和明确的语法来导入和导出代码，使得推理不同模块之间的依赖关系更容易。

我们可以将每个微应用导出为 ES 模块，并通过使用动态导入，将它们嵌入到我们的宿主应用中。

整个微前端应用可以使用基于 ES 构建的模块打包器，如 Vite ([`vitejs.dev/`](https://vitejs.dev/)) 进行打包。Monorepo 框架如 Nx 允许你轻松配置使用 Vite 作为模块打包器。

我们可以使用 Vite 搭建一个 React 应用，如下所示：

```js
pnpm create vite microfrontend-app --template react
```

这里有一个如何实现这一点的粗略示例：

```js
// Catalog App
function CatalogApp() { 
  return <h1>Hello World</h1>;
}
export default CatalogApp;
```

在宿主应用中，我们使用经典的 React `suspense` 和 `lazy` 函数在运行时加载 `CatalogApp`：

```js
// Host App
import React, { lazy, Suspense } from 'react';
const CatalogApp = lazy(() => import('./catalog'));
function App() {
  return (
    <>
      <Suspense fallback={<div>Loading...</div>}>
        <CatalogApp />
      </Suspense>
    </>
  );
}
```

正如你所注意到的，我们已经设法在不使用 Webpack 或 Webpack 的模块联邦的情况下使我们的应用工作，我相信你也会注意到你做出任何更改后应用构建的速度有多快。

我们相信 ES 模块和 ES 构建系统将很快取代 Webpack，成为构建所有现代前端的事实上首选工具。值得注意的是，虽然 React 的 `lazy` 和 `suspense` 函数通常被认为是性能优化技术，但我们利用它们实时加载模块的能力来构建微前端。

# 使用 WebAssembly 模块

**WebAssembly** (**Wasm**) 已经存在很多年了。尽管它在性能和低包大小方面具有巨大的优势，但它并没有获得太多的普及，主要是因为开发者构建 WASM 模块并不容易。然而，现在随着人们开始使用 Rust 等工具，使用 Rust 构建 WebAssembly 模块变得相当容易。我们预计，当构建需要在浏览器上进行高度计算的应用时，WebAssembly 将成为主流。

WASM 模块在微前端架构中可以工作得非常好，其中关键的计算密集型模块是用 WASM 封装作为微应用构建的，并导入到微前端架构中，在该架构中，微前端中的其余微应用使用标准的 React 构建。

这里有一个大致的方法，你可以用它来设置你的模块联邦 Next.js 应用。使用我们来自 *第六章* 的模块联邦代码。首先，在 `/rust` 文件夹内使用 `wasm_bindgen` 构建一个 Rust 应用。

要将 Rust 应用编译为 wasm，我们需要安装 `wasm-pack-plugin`，使用 `pnpm install @wasm-tool/wasm-pack-plugin` 安装它，并在 `next.config.js` 配置中使用它，如下所示：

```js
const NextFederationPlugin = require("@module-federation/nextjs-mf");
const WasmPackPlugin = require('@wasm-tool/wasm-pack-plugin');
const path= require("path")
const remotes = (isServer) => {
  const location = isServer ? "ssr" : "chunks";
  return {
    catalog: `catalog@http://localhost:3001/_next/static/${location}/remoteEntry.js`,
  };
};
module.exports = {
  webpack(config, options) {
    config.plugins.push(
      new WasmPackPlugin({
        crateDirectory: ('./rust'),
    }),
      new NextFederationPlugin({
        name: "catalog",
        filename: "static/chunks/remoteEntry.js",
        exposes: {
          "./Module": "./pages/index.tsx",
        },
        remotes: remotes(options.isServer),
        shared: {},
        extraOptions: {
          automaticAsyncBoundary: true,
        },

      })
    );
    config.experiments = {
      syncWebAssembly: true,
    };
    config.module.rules.push({
      test: /\.wasm$/,
      type: 'webassembly/sync',
    });
    return config;
  },
};
```

然后使用动态导入，将 wasm 模块导入远程应用的索引页面。最后，使用我们在 *第六章* 中使用的方法将远程应用导入主机应用。

WASM 已经被用于一些非常流行的基于网络的工具中，如 Figma、AutoCAD、Google Earth、Unity 游戏引擎等。将 WebAssembly 模块与微前端结合使用，有助于将两者的优点结合起来：WASM 的力量和性能，以及微前端的易用性和模块化。

# Edge 函数或云函数

Edge 函数越来越受欢迎，因为它们提供了在边缘进行计算的能力。想象一下，它们就像一个 **内容分发网络** (**CDN**)，但具有运行计算的能力和力量。

Edge 函数的主要好处是它们提供了非常低的延迟，这极大地帮助提高了性能，并且它们使用自动分布式部署，这有助于减轻单点故障并提高可扩展性。

边缘函数和微前端配合得相当好，你可以将每个微应用部署在云函数中；这自动允许模块化部署，每个团队可以独立管理其云函数。

Cloudflare 是最受欢迎的云函数提供商之一。Cloudflare Workers 和最近推出的 Cloudflare Pages 支持边缘计算。以下是如何使用 Edge Runtime 在 Cloudflare Pages 上部署 Next.js 应用的示例。

1.  从我们构建的任何现有 Next.js 应用开始。

    ```js
    npm install --save-dev @cloudflare/next-on-pages
    ```

1.  提交你的更改并将它们推送到 Git 仓库。

1.  登录 Cloudflare 仪表板，转到**Workers & Pages | 创建应用 | Pages | 连接到 Git**。

1.  4. 选择你推送代码的仓库，在设置构建和部署中，选择 Next.js 作为你的框架。其余的设置保持默认。

接下来，我们需要设置兼容性标志，这通过进入`nodejs_compat`来实现。

从部署详情部分进入**管理部署**，从下拉菜单中选择**重试部署**。

由于成本低廉和部署简便，我们相信在 Vercel、Cloudflare、Fastly 等平台上部署所有前端应用，无论它们是否是微前端，都具有巨大的潜力。

大多数边缘函数提供商对 JavaScript 生态系统都有非常好的支持；然而，重要的是要记住，根据你正在工作的供应商/平台，可能会有某些限制。例如，Cloudflare 限制每个 worker 的大小不超过 1 MB，或者它明确支持与更广泛的 Node.js 运行时环境兼容的包版本。对于 Cloudflare，你可以在这里了解更多关于 Node.js 兼容性的信息：[`developers.cloudflare.com/pages/framework-guides/`](https://developers.cloudflare.com/pages/framework-guides/)。

# 生成式 AI 和微前端

生成式 AI 显然已经席卷了整个世界。我们看到许多令人惊叹的例子，生成式 AI 能够生成完整的端到端应用。

当谈到构建微前端时，看到事情如何发展将非常有趣。虽然我相信生成式 AI 不能取代开发者的工作，但我确实看到了生成式 AI 如何与微前端携手共建独特客户体验的有趣用例。

生成式 AI 可以被用来动态生成和组装 Web 应用的各个部分。通过智能分析用户行为、偏好和实时上下文，AI 可以创建为单个用户量身定制的微前端，从而实现高度个性化和优化的用户体验。这种方法还通过允许开发者专注于创建模块化、可组合的微应用，同时 AI 系统负责整个 Web 应用的组装和渲染，从而简化了开发过程。

新的 AI 工具，如 GPT-Engineer、smol-ai 和 Auto-GPT 正在出现，这些工具允许开发者使用纯文本或 Markdown 描述应用程序需求。然后，这些工具根据开发者的规格构建和生成整个应用程序的代码。这消除了手动编写所有代码的需要，相反，让 AI 处理大部分初始设置。这类 AI 开发者助手还处于相当初级的阶段；开发者需要学习如何制作有效的提示，以从 AI 中获得最一致和最准确的结果，但 AI 显著增强和加速开发工作流程的潜力是存在的。关键在于继续提高 AI 的代码生成能力，同时帮助开发者提供正确的输入和指导。

在微前端中使用人工智能可以导致更有效的资源利用和性能提升，因为系统可以根据用户交互和需求自适应地加载和卸载组件。这种人工智能与微前端的创新集成有可能彻底改变网络应用的设计、开发和交付给用户的方式。

# 摘要

有了这些，我们已经到达了本章和本书的结尾。我们真心希望您已经享受了这次旅程。

在本章中，我们探讨了几个将影响我们构建和部署微前端的新趋势。我们看到了如何使用岛屿模式帮助将动态内容块交织在静态生成的多页应用中。我们看到了基于新 Rust 打包器的工具可以比 Webpack 快许多倍。我们了解了 WebAssembly 及其在微前端中的应用，最后，我们探讨了云函数，它们有可能成为部署所有现代前端应用的默认解决方案。

我对技术发展之快以及它如何影响我们构建应用程序的方式感到非常兴奋。我迫不及待地想看到您走出家门，构建出让这个世界变得更美好的事物。

最后，我们必须记住，微前端的世界，就像我们动态的数字景观一样，始终处于不断演变的状态。我们在这次旅程中揭示的概念、技术和方法，例如模块联邦和将微前端部署到云端的引人入胜实践，只是这个不断演变的画卷的起点。它们为我们提供了构建高性能、可扩展和可维护的前端架构的基石。然而，未来充满了新的趋势和进步的承诺，这些将继续重新定义我们的视野。

我鼓励你们，新一代的开发者，踏进这个激动人心的旅程，并在此基础上构建这本书所尝试提供的知识基础。挑战现状，尝试最新的趋势，并将它们塑造成适合你们项目独特需求的形式。现在是成为一名前端工程师的伟大时刻，世界等待着你们使用 React 和微前端创造的革新性解决方案。记住，你写的每一行代码都是一个改进、创新和启发的机会。所以，勇往直前，为未来而构建。
