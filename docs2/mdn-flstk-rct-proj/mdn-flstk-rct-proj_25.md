# 20

# 深入全栈开发

在学习如何构建和部署 Next.js 应用后，我们就完成了使用 React 的全栈开发之旅。在本章的最后，我想为您概述并简要介绍本书中未涉及的各种高级主题。这包括维护大型项目、优化包大小、**用户界面**（**UI**）库概述以及高级状态管理解决方案等概念。

在本章中，我们将介绍以下主要内容：

+   其他全栈框架概述

+   UI 库概述

+   高级状态管理解决方案概述

+   维护大型项目的要点

注意

由于本章仅概述了全栈开发中的高级主题并提供进一步阅读的链接，因此没有代码示例，因此本章也没有技术要求。

# 其他全栈框架概述

在本书中，我们学习了 Next.js，这是最受欢迎的 React 全栈框架。然而，其他全栈框架可能对您也很有兴趣，每个框架都有其自身的优缺点。

在我们比较框架之前，让我们回顾一下 React 中不同的渲染方法：

+   **客户端渲染**（**CSR**）：在浏览器中渲染组件

+   **服务器端渲染**（**SSR**）：在服务器上渲染组件并提供服务结果

+   **静态站点生成**（**SSG**）：在服务器上渲染组件并将它们存储为静态 HTML，然后提供静态 HTML

+   **增量静态生成**（**ISR**）：动态执行 SSG 并缓存结果一段时间

+   **延迟站点生成**（**DSG**）：在构建时间和页面重新渲染时缓存所有数据，并利用这些缓存数据

此外，许多框架（和云服务提供商）支持**Edge 运行时**，这是标准 Web API 的一个子集，用于在“边缘”运行代码。这里的“边缘”指的是可以部署在尽可能靠近客户的位置的无服务器计算环境。例如，如果有人从奥地利访问您的网站，代码将在奥地利或德国的最近的服务器上运行。然而，对于来自美国的人来说，代码将在美国的某个服务器上运行。这减少了网络延迟并使我们的应用加载更快。

现在，让我们来看看不同的全栈框架。

## Next.js

我们在本章中已经学习了 Next.js——它是撰写本书时最受欢迎的全栈 Web 框架，支持 CSR、SSR、SSG 和 ISR。最近，Next.js 默认使用 SSG 以保持应用尽可能高效，但它仍然提供重新验证缓存页面并在必要时提供 SSR 的能力。

Next.js 也支持 Edge 运行时，但您必须明确选择使用它而不是（默认的）Node.js 运行时。某些功能在 Edge 运行时也不可用。

你可以在这里查看 Next.js：[`nextjs.org/`](https://nextjs.org/)。

## Remix

Remix 是一个全栈框架，专注于 Web 标准。

与 Next.js 不同，它不提供静态站点生成（SSG），而是专注于提高动态渲染性能和通过服务器端渲染（SSR）与 Web 基础设施的集成。由于 Remix 完全基于 Web 标准，它不需要 Node.js 来运行，因此可以在边缘运行时（如 Cloudflare Workers）本地运行。

目前，Remix 不支持 **React 服务器组件**（**RSCs**），但它有自己的模式，可以带来与使用 RSCs 相同的优势。

与 Next.js 类似，它支持嵌套路由（带有嵌套布局）、动态路由和服务器端并行渲染。Remix 路由基于 React Router，如果你已经使用过 React Router，那么它很容易理解。它还支持加载/错误状态，以及一种服务器端操作的形式。

总体而言，Remix 是 Next.js 的一个非常好的替代品，特别是如果你更喜欢使用标准 Web API 并关心边缘运行时支持。它的目标是尽可能依赖标准，使 Web 开发再次变得简单。

你可以在这里查看 Remix：[`remix.run/`](https://remix.run/)。

## Gatsby

Gatsby 主要关注 SSG。虽然现在它也可以进行 SSR，但框架作者鼓励尽可能多地使用 SSG。而不是增量静态化（ISR），Gatsby 提供了数据同步生成（DSG），在大网站上使数据更加一致，但代价是可能提供过时的数据。

最近，Gatsby 开始提供 RSC 支持，并支持边缘运行时。然而，与 Next.js 一样，它也依赖于 Node.js API，因此只为边缘运行时提供其功能的一个子集。

Gatsby 的一个优点是其庞大的插件生态系统，允许开发者轻松集成新功能。

然而，Gatsby 的一个缺点是它不支持带有嵌套布局的嵌套路由。

虽然 Next.js 和 Remix 都提供了对 REST 和 GraphQL 的支持，但 Gatsby 主要关注 GraphQL，仅将 REST API 作为第二类公民支持。然而，这使 Gatsby 能够提供易于集成各种数据源的插件。

总体而言，如果你主要想使用 SSG、集成来自各种来源的数据以及一个易于学习的工具，Gatsby 可以成为一个很好的框架。Gatsby 不是一次性向你展示框架的所有复杂性，而是通过其插件生态系统逐步揭示复杂性。

你可以在这里查看 Gatsby：[`www.gatsbyjs.com/`](https://www.gatsbyjs.com/)。

接下来，我们将概述一些选定的 UI 库。

# UI 库概述

在这本书中，我故意省略了 UI 库，因为它们具有强烈的个人观点，不断变化，并且会使代码示例显著变长。在本节中，我想提供一些选定 UI 库的概述。请随意自行探索，并关注该领域的其他选项和新版本！

## Material UI (MUI)

MUI 是 React 最受欢迎的组件库之一。它支持广泛的组件，包括数据表等复杂组件。它还拥有一个非常可扩展的主题系统，允许你调整它以适应你的风格。然而，截至写作时，它的样式引擎与 RSC 不兼容，这是未来版本中将得到改进的地方。如果你通常喜欢它的风格但想自定义颜色、字体和间距以使其成为你自己的，请使用 MUI。

你可以在这里查看 MUI：[`mui.com/`](https://mui.com/)。

## Tailwind CSS

Tailwind CSS 是一个以实用工具为主的 CSS 框架，不需要 React。然而，它与 React 协作良好，允许你轻松地为自定义组件添加样式。由于它仅使用 CSS，你可以根据需要精确地调整 React 组件。这也意味着 RSC 完全受支持，因为 Tailwind 只是一组 CSS 类。如果你想快速简单地实现应用的完全自定义样式，而不是直接使用 CSS，请使用 Tailwind。

你可以在这里查看 Tailwind CSS：[`tailwindcss.com/`](https://tailwindcss.com/)。

### Tailwind UI

Tailwind CSS 的制作者还提供了一套使用 Tailwind CSS 的预制作仅样式组件，称为 Tailwind UI。如果你需要灵感来创建使用 Tailwind 的组件，请查看它：[`tailwindui.com/`](https://tailwindui.com/)。

## React Aria

React Aria 是一套具有出色的可访问性和国际化支持的简单组件。默认情况下，组件是无样式的，允许你构建自定义设计。你还可以将其与 Tailwind 结合使用。如果你想创建一个设计系统但又不想处理创建可访问性组件的挑战，请使用 React Aria。

你可以在这里查看 React Aria：[`react-spectrum.adobe.com/react-aria/`](https://react-spectrum.adobe.com/react-aria/)。

## NextUI

NextUI 是一个使用 Vercel（Next.js 背后的公司）风格的即将推出的 UI 库。它建立在 Tailwind CSS 之上，但提供了基于 React Aria 的各种组件，确保了一流的可访问性支持。像 MUI 一样，它也提供了许多组件，并且可以通过主题进行高度定制。此外，它支持 RSC，因为它基于 Tailwind CSS。如果你喜欢这种风格并想稍作定制，尤其是如果你正在使用具有 RSC 支持的框架进行开发，请使用 NextUI。

你可以在这里查看 NextUI：[`nextui.org/`](https://nextui.org/)。

接下来，我们将提供高级状态管理解决方案的概述。

# 高级状态管理解决方案概述

在这本书中，我们专注于 React 中的简单状态管理解决方案，如`useState`和上下文。然而，在大型项目中，使用高级状态管理库来处理复杂状态可能是有意义的。在这里，我将概述一些选定的状态管理库，但请记住，还有许多其他库，所以请随意查看它们并决定哪个最适合你的项目。

## Recoil

Recoil 是一个由 Facebook Open Source 构建的 React 状态管理库。因此，它共享了许多 React 的原则。它是一个非常简单但强大的系统，其中状态存储在原子中，然后通过选择器派生。这使得我们能够，例如，仅在原子中存储表单的用户输入，并在选择器中存储结果的有效负载，该选择器从原子中派生其状态。

你可以在这里查看 Recoil：[`recoiljs.org/`](https://recoiljs.org/).

## Jotai

Jotai 采取与 Recoil 类似的方法，但通过消除选择器并仅处理原子来简化系统。原子可以从其他原子派生状态。如果你需要一个仍然简单但比`useState`更强大的状态管理解决方案，Jotai 是一个很好的选择。

你可以在这里查看 Jotai：[`jotai.org`](https://jotai.org).

## Redux

Redux 采取了一种不同的方法，提供了一个中心存储，其中包含你所有的状态，然后只允许你通过操作来更改它。这确保了你的应用程序表现一致，并且相同的用户操作总是导致相同的状态变化。Redux 对于操作至关重要且需要撤销/重做功能的应用程序来说非常出色（例如某些编辑器）。

你可以在这里查看 Redux：[`redux.js.org`](https://redux.js.org).

## MobX

MobX 是一个基于信号的州管理库，它使用可观察性来跟踪状态变化。当一个值被设置为可观察的，它可以被直接修改，就像一个常规的 JavaScript 变量一样，但对其的所有更改都会触发状态观察者的执行和组件的重渲染。

你可以在这里查看 MobX：[`mobx.js.org/`](https://mobx.js.org/).

## xstate

xstate 是一个状态机库，当你有复杂用户界面和需要跟踪的各种状态时，它非常有用。

你可以在这里查看 xstate：[`stately.ai/docs/xstate`](https://stately.ai/docs/xstate).

## Zustand

Zustand 是一个小型状态管理库，具有基于 hook 的 API，它结合了存储中的值和更改这些值的函数。每个存储都暴露一个 hook，其中可以使用其值和函数。

你可以在这里查看 Zustand：[`docs.pmnd.rs/zustand/getting-started/introduction`](https://docs.pmnd.rs/zustand/getting-started/introduction).

现在，让我们通过学习一些维护大型项目的要点来结束。

# 维护大型项目的要点

为了使这本书尽可能简短、直接，并且面向更广泛的受众，我故意省略了一些主题和技术。然而，当维护大规模项目时，了解这些内容仍然非常重要，因此我想在这里简要介绍它们。

## 使用 TypeScript

TypeScript 是在 JavaScript 中扩展了类型语法的语言。类型系统可以在早期捕获错误，并在重构大型代码库时提供信心。虽然适应所有类型的输入可能需要一些时间，但当你意识到所有问题都作为类型错误出现在你的代码编辑器中，而不是用户的运行时错误时，它就会变成一种祝福。

我建议所有新的项目都使用 TypeScript。当你已经了解 JavaScript 时，它很容易学习，并且与 Next.js 等框架很好地集成。

你可以在这里了解更多关于 TypeScript 的信息：[`www.typescriptlang.org`](https://www.typescriptlang.org).

## 设置 Monorepo

在这本书中，我们一直是在处理单个应用程序。然而，大规模项目通常由多个应用程序组成，它们之间可能共享多个内部库。例如，你可能有两个应用程序，它们在公共 UI 库中共享 UI 组件。将这些库和应用程序放在单独的 git 仓库中，往往会导致组织上的开销。

为了保持简单，开发团队通常会决定设置一个 Monorepo，其中包含所有应用程序和库。这也使得保持代码库的一致性和跟踪大规模重构变得更加容易。

你可以在这里了解更多关于 Monorepos 的信息：[`monorepo.tools`](https://monorepo.tools).

要设置 Monorepo，请使用支持工作区的包管理器，例如 pnpm ([`pnpm.io`](https://pnpm.io)) 或 yarn ([`yarnpkg.com`](https://yarnpkg.com))。某些工具使创建和维护 Monorepos 更加容易，例如 Turborepo。查看 [`turbo.build`](https://turbo.build) 上的指南，了解如何使用 Turborepo 设置 Monorepo。

## 优化包大小

随着你的项目增长，发送到浏览器的 JavaScript 包也会增长。这可能会对连接速度较慢或处理器较慢的设备造成问题。有时，某些依赖项会增加包的大小，因此定期检查项目中的更改如何影响包大小是一个好主意。

对于 Vite，你可以使用 `vite-bundle-visualizer` 来找出哪些依赖项增加了你的包大小：[`github.com/KusStar/vite-bundle-visualizer`](https://github.com/KusStar/vite-bundle-visualizer).

对于 Next.js，你可以使用官方的 `@next/bundle-analyzer` 插件：[`nextjs.org/docs/app/building-your-application/optimizing/bundle-analyzer`](https://nextjs.org/docs/app/building-your-application/optimizing/bundle-analyzer).

# 摘要

在这本书中，我们以成为全栈开发者的动机开始。然后，我们搭建了我们的开发环境，并学习了使我们的生活更轻松的工具。接下来，我们了解了 Node.js 和 MongoDB，作为后端开发者迈出了第一步。然后，我们使用 Express 和 Mongoose 为博客应用实现了后端，并使用 Jest 为其编写了单元测试。之后，我们使用 React 和 TanStack Query 将前端与后端集成，从而创建了我们的第一个全栈 Web 应用。接下来，我们学习了如何使用 Docker 部署我们的应用，以及如何设置 CI/CD。然后，我们使用 JWT 为我们的应用添加了身份验证。我们学习了如何使用 SSR 提高我们应用的加载时间，并在过程中开发了自己的（简单的）SSR 解决方案。然后，我们学习了搜索引擎的工作原理，以及如何通过促进 SEO 和为社交媒体嵌入提供元数据，确保客户可以找到我们的 Web 应用。接下来，我们使用 Playwright 实现了端到端测试，确保我们的应用始终按预期工作。然后，我们学习了如何使用 MongoDB 和 Victory 聚合和可视化统计数据。

之后，我们偏离了 REST API，使用 GraphQL API 开发了一个后端，学习了 GraphQL 是什么以及它的好处。然后，我们开发了一个前端来消费这个 GraphQL API。接下来，我们暂时放下了博客应用，使用 Socket.IO 构建了一个基于事件的聊天应用。在这个过程中，我们学习了如何创建后端和前端，以及如何在基于事件的范式下添加持久性。在本书的最后几章中，我们学习了 Next.js，一个全栈 Web 开发框架。我们介绍了应用路由，这是一种新的应用程序结构方式，以及 RSCs，它允许我们将后端和前端合并得更紧密，减少了创建 API 的样板代码需求，并允许我们直接从 RSCs 内部的数据层访问代码。我们还学习了 Next.js 中的高级概念和优化，如缓存、SEO 和优化的字体和图像加载。最后，我们学习了如何使用 Vercel 部署 Next.js 应用，Vercel 是由 Next.js 的制作者提供的云平台，我们还创建了一个自定义的部署设置，使用 Docker，这样我们就可以在任何其他云服务提供商（或我们自己的服务器）上部署我们的应用。

这已经是一条漫长的道路。然而，正如我们在本章中看到的，还有更多的话题需要我们深入研究，Web 开发生态系统也在快速发展。新技术不断涌现，尤其是在 RSCs 和 Server Actions 方面，在撰写本文时，这些技术仍然新颖且正在兴起。我预计这个领域将推出更多功能，所以请密切关注 React 世界中的突破性公告！

“求知若饥，虚心若愚。永远不要放弃对新想法、新体验和新冒险的渴望。” —— 史蒂夫·乔布斯
