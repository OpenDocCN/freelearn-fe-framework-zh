# 3

# 部署目标、适配器和栈

在运行时，Remix 在底层 Web 服务器上运行并处理传入的 HTTP 请求。启动一个新的 Remix 项目也意味着选择一个 Web 服务器和 JavaScript 运行时。Remix 团队及其社区为许多流行的部署目标维护了入门模板和适配器。在本章中，我们将回顾不同的部署目标、模板和 Remix Stacks。

我们将涵盖以下主题：

+   选择部署目标

+   在适配器之间切换

+   使用 Remix Stacks

+   与 BeeRich 合作

首先，我们将概述流行的模板、部署目标、JavaScript 运行时和托管环境。接下来，我们将练习切换适配器，并介绍 Remix Stacks。在本章结束时，我们将使用本书的定制模板启动一个新的 Remix 应用程序。

在完成本章学习后，你将了解在选择新的 Remix 应用程序的部署目标和模板时需要考虑的因素。你还将了解更多关于不同的 JavaScript 运行时，并理解长期运行的服务器、无服务器和边缘环境之间的主要区别。此外，你将获得在适配器之间切换的实践经验，并学习如何与 Remix Stacks 一起工作。

# 技术要求

为了完成本章，你需要一台可以运行 Node.js 的计算机。所有流行的操作系统都足够使用。请在您的机器上安装 Node.js 和 npm。推荐使用 VS Code 等编辑器。

你可以从这里下载 Node.js 和 npm：[`nodejs.org/en/download/`](https://nodejs.org/en/download/).

本章的代码可以在以下链接找到：[`github.com/PacktPublishing/Full-Stack-Web-Development-with-Remix/tree/main/03-deployment-targets-adapters-and-stacks`](https://github.com/PacktPublishing/Full-Stack-Web-Development-with-Remix/tree/main/03-deployment-targets-adapters-and-stacks).

# 选择部署目标

Remix 最显著的特点之一是其灵活性。Remix 支持许多不同的 Web 服务器和运行时环境，包括无服务器和边缘环境。选择模板也意味着选择 JavaScript 运行时、托管环境（服务器、无服务器、边缘）以及可能的主机提供商平台。为了决定为项目选择哪个模板，我们需要了解不同部署目标的优缺点，以便做出明智的决定。在本节中，我们概述了流行的模板和适配器，并回顾了它们之间的区别。

在上一章中，我们使用`create-remix` CLI 脚本来创建一个新的 Remix 应用程序。在这个过程中，我们必须指定一个模板或使用 Remix 的基本模板。Remix 团队和社区为许多不同的部署目标维护了适配器和模板。一个模板会使用所需的 Web 服务器代码启动一个新的 Remix 应用程序。Web 服务器代码不是 Remix 框架的一部分，但它与 Remix 应用程序一起分发，以简化开发过程。

在以下内容中，我们提供了 Remix 常见部署目标的概述。*表 3.1* 列出了由 Remix 团队和社区或托管提供商公司维护的模板。如果一个模板没有为特定的托管提供商设置，那么**托管提供商**列将显示**通用**。

所有引用的模板也列在 Remix 文档中：[`remix.run/docs/en/dev/other-api/adapter`](https://remix.run/docs/en/dev/other-api/adapter)。此外，你还可以在 GitHub 上的 Remix 仓库中找到 Remix 团队维护的所有模板：[`github.com/remix-run/remix/tree/main/templates`](https://github.com/remix-run/remix/tree/main/templates)。

| **部署目标** | **托管提供商** | **JavaScript 运行时** | **托管环境** |
| --- | --- | --- | --- |
| Architect (Arc) | AWS | Node.js | Serverless |
| Cloudflare Pages | Cloudflare | **Workers runtime** | Edge **Isolate** |
| Cloudflare Workers | Cloudflare | **Workers runtime** | Edge **Isolate** |
| Deno | **Generic** | Deno | Edge **Isolate** |
| Express.js | **Generic** | Node.js | Server |
| Fastify | **Generic** | Node.js | Server |
| Fastly Compute@Edge | Fastly | Wasmtime | Edge **Isolate** |
| Fly.io | Fly.io | Node.js | **Distributed Server** |
| Netlify | Netlify | Node.js | Serverless |
| Remix App Server | **Generic** | Node.js | Server |
| Vercel | Vercel | Node.js | Serverless |

表 3.1 – Remix 的部署目标

如前表所示，许多部署目标都与特定的托管提供商相关联。托管服务特定适配器的优点是部署简单。你会发现，你可以在几分钟内启动并运行这些服务中的大多数。 

通用适配器的优势在于额外的灵活性。例如，选择 Express.js 部署目标将创建一个 Express.js 服务器，可以在任何 Node.js 服务器可以运行的地方托管。这包括 AWS、Azure 和 Google Cloud Platform 等云计算平台以及 Railway.app 和 Render.com 等流行的托管平台。

注意，*表 3.1* 并不完整，因为可用的部署目标、模板和适配器的数量会随时间变化。本章的目标是通过学习如何分类和描述它们，帮助你有效地选择部署目标。首先，让我们看看各种 JavaScript 运行时之间的区别。

## 选择 JavaScript 运行时

JavaScript 可以在不同的运行时环境中执行。在本节中，我们列出了流行的 JavaScript 运行时，并讨论了在选择运行时时应考虑的因素。

一些流行的 JavaScript 运行时环境如下：

+   浏览器环境

+   Node.js

+   Deno

+   Workers runtime

+   Bun

浏览器是 JavaScript 最原生环境。然而，不同的浏览器使用不同的引擎来执行 JavaScript。因此，它们在技术上构成了不同的运行时环境。幸运的是，它们大多遵循开放网络平台，并支持标准的 JavaScript API 和运行时行为。由于我们正在尝试选择服务器端部署目标，我们目前对服务器端运行时更感兴趣。

JavaScript 最突出的服务器端运行时是 Node.js。Node.js 拥有丰富的包和库生态系统，被广泛使用和支持。然而，也存在其他服务器端 JavaScript 运行时，如 Bun、Deno 和 Cloudflare 的 Workers 运行时（workerd）。

如*表 3.1*所示，一些部署目标在底层使用 Deno 或 workerd。了解这对你的应用程序开发意味着什么非常重要。如果你对使用 Deno 或 workerd 开始一个项目感兴趣，请确保首先熟悉底层运行时。

比较 JavaScript 运行时，我们必须确保它们支持我们应用程序所需的 API 和功能。例如，workerd 是一个边缘运行时，为 Cloudflare Workers 提供动力。workerd 不支持 Node.js 标准库的执行。因此，只有当这些包不使用 Node.js 标准库时，你才能使用 npm 包。相比之下，大多数 Node.js 包在 Deno 中都能工作，因为 Deno 旨在与 Node.js 兼容。你也将能够在 Deno 中使用大多数 Node.js 标准库。然而，这需要特殊的导入语法。

作为 Remix 开发者，了解一些部署目标基于与 Node.js 不同的 JavaScript 运行时非常重要。每个运行时都带有限制和考虑因素。接下来，我们将研究不同托管环境之间的差异。

## 选择托管环境

我们托管 Web 应用的方式随着时间的推移而改变。部署可以简单到指向 GitHub 仓库和分支或提供 Docker 镜像。我们很少再自己管理底层服务器基础设施。相反，托管提供商和云平台为我们管理基础设施，并提供我们的应用程序运行的环境。

云平台和托管提供商提供不同的托管环境。这些环境各有优缺点。如*表 3.1*所示，不同的部署目标适合以下三个托管环境类别之一：

+   服务器

+   无服务器

+   边缘

让我们看看三种不同的托管环境有何不同。以下表格概述了最重要的差异：

| **特性** | **服务器** | **无服务器** | **边缘** |
| --- | --- | --- | --- |
| 长运行 | 否 | 是 | 否 |
| 文件系统访问 | 是（视情况而定） | 否 | 否 |
| 独立请求 | 否 | 是 | 是 |
| 默认可扩展 | 否 | 是 | 是 |
| 默认分布式 | 否 | 否 | 是 |

表 3.2 – 不同托管环境的特征

Web 服务器被认为是长期运行的环境。Web 服务器通常只启动一次，只有在升级、重新部署或服务停用时才会停止运行。根据托管提供商的不同，Web 服务器可以访问文件系统。长期运行的服务器不会隔离单个请求。这意味着请求共享全局应用程序状态。

无服务器环境在本质上与长期运行的服务器不同。无服务器是一种云计算模型，其中应用程序被视为一个函数，并由云平台提供商进行编排。无服务器函数专门为传入请求启动。在指定超时后，无服务器函数被终止，并且任何应用程序上下文，如闭包、缓存的程序状态和全局变量，都会丢失。

无服务器环境通常提供按使用付费的定价。我们只为函数处理请求的时间付费。由于基础设施提供商可以在并行中生成多个重复的无服务器函数，因此无服务器本质上也是高度可扩展的。

需要注意的是，无服务器环境与 Web 服务器的要求非常不同。在长期运行的服务器上容易解决的问题在无服务器上可能需要更多的参与。然而，无服务器提供的解决方案旨在可扩展。

例如，Web 服务器在启动时创建数据库连接，并将这些连接共享给所有传入的请求。无服务器函数必须为每个传入请求创建一个新的数据库连接。创建新的数据库连接可能会成为传入请求的瓶颈，并可能导致延迟或超时。此外，数据库服务器一次只能支持有限数量的打开连接。为每个无服务器函数打开新的连接可能会超过最大连接数。

无服务器提供连接池作为解决这些问题的方法之一。连接池允许不同的函数在不同实例之间共享连接。这是无服务器中的一种常见模式。与长期运行的服务器一起工作的事物在无服务器中需要更多的考虑。无服务器旨在进行扩展，但扩展也带来了复杂性作为副产品。

扩展不可避免地会引入复杂性。一旦你需要考虑长期服务器的扩展，你可能会遇到许多无服务器已经为你解决的问题。例如，无服务器环境默认实现负载均衡。

服务器无服务器关注的是可扩展性，而边缘计算关注的是提供地理邻近性。大多数边缘环境都是无服务器的，并且共享相同的优势，如可扩展性和按使用付费定价。使边缘环境特殊的是它们与最终用户的邻近性。边缘函数是区域分布的——例如，在 CDN 的服务器上。这种创建的邻近性可以显著减少响应时间。

大多数服务器和无服务器环境不会自动将你的应用程序分布到不同的区域，至少不是在没有额外的配置开销和额外成本的情况下。边缘计算允许你“开箱即用”地将你的 Web 应用程序分布到全球各地。

边缘环境有其自身的局限性和考虑因素。一般来说，边缘函数在运行时能力方面是最受限制的。一个限制是，在边缘部署需要分布式数据库解决方案。否则，边缘函数与你的数据库服务器之间的通信将导致响应时间延迟。

不同的运行时环境也使用不同的容器技术来部署和运行你的代码。长期运行的服务器可以直接在物理或虚拟机上运行。然而，大多数云提供商使用轻量级的容器技术来运行你的应用程序。需要注意的是，容器领域是多样化的。有几种不同的容器技术和标准，例如 V8 隔离。

V8 是 Chrome 背后的 JavaScript 引擎，也被 Node.js 和 Deno 使用。V8 隔离是重用相同语言运行时的隔离 V8 实例，避免了在接收到的请求上启动新的语言运行时的缓慢冷启动。由于它们比大多数容器技术更轻量级，V8 隔离在计算相对于集中式云数据中心稀疏的边缘环境中被使用。

在选择托管提供商和运行时环境时，研究与其容器技术相关的限制。这些限制可能会影响你的应用程序的大小或超时前的最大运行时间。

考虑到权衡取舍，每个环境都伴随着其自身的考虑因素。接下来，我们将连接这些点，总结如何为特定用例选择正确的部署目标。

## 做出最终决定

在本节中，我们将总结如何根据我们对 JavaScript 运行时和不同托管环境（服务器、无服务器、边缘）的了解来选择部署目标。

根据我的经验，长期运行的服务器环境提供了大量的灵活性，并且比其替代方案引入的复杂性要少。话虽如此，像 Netlify 和 Vercel 这样的托管提供商为他们的无服务器和边缘服务提供了极佳的开发者体验和补充服务。如果你在寻找一个能够区域性地分配你的长期运行 Web 服务器和数据库的提供商，那么 Fly.io 可能是一个不错的选择。

无服务器提供了许多优势，但也引入了复杂性。边缘计算可以显著提高响应时间，但引入了更多的复杂性。无服务器和边缘环境的额外复杂性是规模和区域分布的副产品。一旦你考虑了长期运行的 Web 服务器的规模和区域分布，你将面临类似的挑战。

个人而言，选择正确的部署目标更多的是避免不必要的障碍，而不是其他任何事情。部署目标必须符合用例并满足系统要求。让我们来看一些例子。

如果你想要利用 Node.js 库或重用 Node.js 代码，你可能想坚持使用基于 Node 的环境。Express.js 的部署目标是显而易见的选择。但是，对于无服务器环境呢？如果你使用的是没有为无服务器环境提供内置解决方案的传统数据库，那么你将不得不为每个新的传入请求创建一个新的数据库连接。这可能会导致响应时间更长，甚至超时。然而，如果你已经使用了一个以无服务器为先的数据库，并且正在寻找可扩展性，那么选择一个无服务器环境可能是一个很好的主意。

如果你只有一个位于俄勒冈州的数据库服务器，那么将你的 Web 应用程序部署到世界各地并不会给你带来太多好处。你的应用程序的每个实例都将不得不从俄勒冈州的数据库服务器请求数据。在这种情况下，你应该选择一个尽可能靠近你的数据库的 Web 服务器或无服务器环境。

如果你想要建立一个博客，你很可能会寻找轻松访问你的博客文章。博客文章通常以文件的形式存储。使用允许你访问文件系统的托管提供商和环境，你可以将你的博客文章与你的代码一起托管。除非你遇到这个问题（任何方法都有缺点），你可能想从一个简单的 Web 服务器架构开始。在这种情况下，你可以选择 Express.js 模板。这将让你通过大多数托管提供商访问底层文件系统。

另一方面，你可能发现一些无服务器提供商提供了有吸引力的免费层和具有竞争力的按使用付费定价。选择正确的运行时取决于你的优先级和需求。选择一个没有文件系统访问权限的托管环境并不意味着你不能与文件一起工作。这仅仅意味着你必须将文件存储在其他地方——例如在文件存储服务中。因此，这更多的是关于做出明智的决定，以避免未来遇到障碍。

最后，决定部署目标也意味着选择一个托管提供商。每个部署提供商都有自己的优缺点。例如，Fly.io 托管长时间运行的 Node.js 服务器，但提供不同地区的分发。Fly.io 可能是一个很好的无服务器边缘部署的替代方案。另一方面，AWS 和 Cloudflare 各自提供丰富的服务生态系统，可以与他们的无服务器和边缘产品一起使用。对于复杂的应用程序，托管在 AWS 或 Cloudflare 可能是正确的选择。

长运行服务器、无服务器和边缘环境之间的界限模糊。不同的提供商进一步为无服务器和服务器环境提供地理分布，以实现类似边缘的用户接近度。一些可能提供两者的最佳结合。

选择部署目标在很大程度上取决于您的应用程序用例。幸运的是，Remix 足够灵活，可以支持各种不同的用例。毕竟，Remix 可以在任何可以执行 JavaScript 的地方运行。

幸运的是，如果需要，可以轻松地在 Remix 项目中交换底层的服务器环境和适配器。在下一节中，我们将练习如何这样做。

# 在适配器之间切换

我们有时必须从一个模板迁移到另一个模板。为此，我们必须切换 Remix 应用程序的设置。在本章中，我们将介绍切换模板和适配器的过程。

在模板和适配器之间切换的过程可以总结如下：

1.  定位您的 Remix 项目，并在编辑器或文件资源管理器中打开它。

1.  使用新的模板和适配器创建一个新的 Remix 项目。

1.  将新 Remix 项目与您的旧项目并排打开。

1.  将新项目中的`app`文件夹重命名为`temp`。将旧项目中的`app`文件夹移动到新项目中。

1.  将`app/entry.client.tsx`中的代码替换为`temp/entry.client.tsx`中的代码，并整合之前添加到文件中的任何自定义代码。

1.  将`app/entry.server.tsx`中的代码替换为`temp/entry.server.tsx`中的代码，并整合之前添加到文件中的任何自定义代码。

1.  对于您旧项目中根目录下的每个 Remix 特定文件和文件夹：

    1.  调查您是否进行了任何更改。

    1.  将任何更改复制粘贴到新项目中。

    1.  解决任何冲突（如果有）。

1.  按照新项目中的`README.md`文件中的说明来运行、构建和部署应用程序。

1.  使用*第二章*中描述的故障排除过程，即*创建新的* *Remix 应用程序*，来解决任何剩余的问题。

让我们使用我们在*第二章*中创建的"Hello World!" Remix 应用程序来练习描述的过程。如果您想挑战自己，看看您是否可以通过遵循之前列出的九个步骤自己解决这个问题。否则，让我们一起来完成它：

1.  在编辑器或文件资源管理器中打开您的“Hello World!” Remix 应用。您也可以在这里找到代码：[`github.com/PacktPublishing/Full-Stack-Web-Development-with-Remix/tree/main/02-creating-a-new-remix-app/hello-world`](https://github.com/PacktPublishing/Full-Stack-Web-Development-with-Remix/tree/main/02-creating-a-new-remix-app/hello-world)。

    在*第二章*，我们使用了基本模板，但这个过程也适用于其他模板。

1.  运行`npm install`和`npm run dev`以确保项目在本地运行且无任何问题。

1.  现在，使用`create-remix` CLI 脚本创建一个新的 Remix 项目。运行以下命令：

    ```js
    create-remix to bootstrap the new Remix app.
    ```

1.  完成创建`create-remix` CLI 脚本后，我们运行`npm install`和`npm run dev`以确保新项目可以在本地运行。

1.  在确认两个项目都能无错误运行后，我们在编辑器或文件资源管理器中打开新的 Remix 应用。同时打开两个编辑器/文件资源管理器是最方便的。

1.  让我们将新项目中的`app`文件夹重命名为`temp`。接下来，将旧“Hello World!”项目中的`app`文件夹复制到新项目中。这样我们可以并排检查`temp`和`app`文件夹。

1.  在新项目中运行`npm run dev`。Remix 将尝试使用“Hello World!”的`app`文件夹构建和运行项目：

    ```js
    @remix-run/node package cannot be resolved. Also, notice that the errors originate from app/entry.server.tsx.
    ```

1.  将`app/entry.server.tsx`中的代码与`temp/entry.server.tsx`中的代码进行比较。

    “Hello World!”应用中使用的 Express.js 模板使用 Remix 的 node 适配器(`@remix-run/node`)，而 Cloudflare Worker 模板使用`@remix-run/cloudflare`适配器。

    Cloudflare Workers 环境在 workerd 上运行，不支持 Node.js 库。要在 Node.js 和 workerd 上运行，Remix 需要设置两个不同的入口点实现并使用不同的适配器。

1.  由于我们在`entry.server.tsx`中没有自定义代码，我们将`app/entry.server.tsx`中的代码替换为`temp/entry.server.tsx`中的代码。

1.  当然，没有其他使用`@remix-run/node`的地方。在您的编辑器的代码搜索中搜索包名。

    您应该找到两个匹配项：`app/root.tsx`和`app/routes/_index.tsx`。

1.  确保找到并替换导入为`@remix-run/cloudflare`。

    Remix 通过不同的适配器包公开其服务器端类型和原语。当切换适配器时，我们必须找到并替换所有旧适配器的导入为新适配器。

1.  接下来，比较并替换`app/entry.client.tsx`中的代码，以及`temp/entry.client.tsx`中的代码。不同的模板可能使用不同的 React 版本或在客户端入口文件中实现额外的逻辑。

1.  再次运行`npm run dev`。现在它应该能够成功构建和运行新项目！

1.  让我们调查旧应用的根文件夹。是否有我们创建或修改的文件？我们应该将所有自定义文件和文件夹移动到新项目中。

1.  检查`package.json`文件。我们没有为我们的“Hello World！”应用程序安装任何额外的包，但通常，我们现在会将任何应用程序依赖项移动到我们的新项目中并安装它们。

    我们还应该比较两个`package.json`文件的脚本。我们必须确保任何自定义或修改过的脚本与我们的新项目的新脚本合并。在我们的案例中，我们不需要做任何事情。我们没有添加任何需要继承的自定义逻辑。

我们做到了！我们从**Remix 应用程序服务器**移动到了**Cloudflare Workers**——从长期运行的服务器到边缘环境！

`app`文件夹中的两个入口文件是由 Remix 启动的，并且针对每个部署目标进行了自定义。因此，我们必须确保更新这些文件中的代码。我们还必须将我们的自定义依赖项与新项目的依赖项合并。这需要我们手动审查`package.json`文件。最后，我们必须接管对项目中文件和文件夹所做的任何其他更改。

在本节中，我们成功地将一个适配器移动到另一个适配器。在下一节中，我们将学习关于 Remix 栈的内容，以及如何为生产就绪的 Remix 应用程序进行初始化。

# 使用 Remix 栈

在本节中，你将了解 Remix 栈。首先，我们将查看 Remix 的官方栈以及如何使用它们。接下来，你将学习如何使用社区模板。

## 与 Remix 官方栈一起工作

Remix 还提供了预配置的生产就绪模板。这些模板被称为 Remix 栈。截至目前，Remix 提供了三个官方栈：

| **栈** | **部署目标** | **包含** | **使用场景** |
| --- | --- | --- | --- |
| Blues | Fly.io | PostgreSQL 数据库 | 大规模应用程序和区域分布 |
| Indie | Express.js | SQLite 数据库 | 具有动态数据的小规模项目 |
| Grunge | Architect (AWS Lambda) | DynamoDB 数据库 | 在 AWS 基础设施上的大规模应用程序 |

表 3.3 – 官方 Remix 栈

栈是具有偏见的工程启动器。它们比 Remix 的基本模板更复杂，但同时也提供了更多功能。如*表 3.3*所示，启动器是根据不同的使用场景构建的，并利用不同的部署目标。

**Blues 栈**运行在 Fly.io 上。从*表 3.1*中，你可以推断出 Fly.io 托管长期运行的服务器。Fly.io 提供了长期运行服务器的区域分布，以实现边缘环境般的接近。Blues 栈还包含设置 PostgreSQL 数据库的代码。该栈旨在可扩展并能够开箱即用地实现区域分布。

**Indie 栈**使用 Express.js 适配器，不针对特定的托管提供商。它包含一个 SQLite 数据库。Indie 栈非常适合启动处理较小动态数据量的项目。从*表 3.2*中，你可以推断出这个栈——取决于托管提供商——可以访问文件系统，并且能够在不同的请求之间共享应用程序状态。

**Grunge 堆栈**服务于与 Blues 堆栈类似的使用案例，但使用的是基于 AWS Lambda 的框架 Architect。从 *表 3.1* 中，您可以得出结论，Architect 在无服务器环境中运行。AWS 基础设施旨在支持大规模应用程序。

让我们尝试运行一个 Remix Stacks。在终端中运行以下命令：

```js
npx create-remix@2 --template remix-run/indie-stack
```

我们再次使用 `create-remix` 来启动一个新的 Remix 应用程序。这次，我们引用了 Remix 的三个官方堆栈模板之一：Indie 堆栈。根据您的个人喜好，您也可以选择另一个堆栈。

注意，我们在 `@remix-run` GitHub 组织中指向了一个名为 `indie-stack` 的 GitHub 仓库。

调查启动文件夹结构，并参考 `README.md` 文件以了解所有使用的技术和包的概述。Indie 堆栈有几个值得注意的特点：

+   一个注册和登录设置 (`app/session.server.ts`)。

+   一个放置您的数据库逻辑的约定 (`app/models/`)。

+   一个用于健康检查的路由 (`app/routes/healthcheck.tsx`)。

Remix Stacks 默认实现样式、测试、身份验证和附加功能。Remix 的基本模板提供简单的服务器和适配器设置，而 Remix Stacks 是生产就绪的应用程序，附带了一个有见地的设置。除此之外，Remix 还支持创建自定义模板。在下一节中，我们将学习如何使用和创建自定义模板。

## 使用自定义模板

我们可以利用社区开发的模板或创建自己的模板，快速使用我们偏好的堆栈开始工作。自定义模板也是为组织开发有见地的模板的绝佳方式。

任何 Remix 项目都可以用作模板。唯一的限制是模板必须是一个有效的 Remix 项目，根目录中有一个 `package.json` 文件。访问模板的最简单方法是通过 GitHub，但 Remix 也可以通过 URL、您机器上的本地路径、GitHub 仓库的子文件夹或本地或远程的 tarball 访问模板。

让我们尝试本书 GitHub 仓库中的自定义模板。在终端中使用带有模板标志的 `create-remix` 命令：

```js
npx create-remix@2 --template PacktPublishing/Full-Stack-Web-Development-with-Remix/03-deployment-targets-adapters-and-stacks/bee-rich
```

确保选择 `remix.init` 脚本。

运行 `init` 脚本后，你应该在终端看到一个简短的消息：

```js
Running template's remix.init script...Hey there! Great job practicing using a custom stack! You're doing great!
```

这条消息源自项目根目录中的 `remix.init/index.js` 脚本。该脚本允许模板作者实现自定义设置步骤。您也可以通过在项目根目录中调用 `npx remix init` 再次运行该脚本。

顺便说一句，我刚刚偷偷地帮你设置了 BeeRich 应用程序，它将作为本书其余部分的演示应用程序。接下来，我们将用 BeeRich 开始我们的开发之旅。

# 使用 BeeRich

欢迎来到 BeeRich！BeeRich 是一个类似于仪表盘的应用程序，它模仿了个人和企业用例。BeeRich 是一个个人财务管理应用程序，帮助你管理好你的蜜蜂——请原谅我——账目。嗯，至少那是目标。目前还没有太多内容。在每一章中，我们将向这个应用程序添加更多代码。在本节中，我们将本地运行 BeeRich 并审查文件夹结构。

在上一节中，我们使用 `create-remix` 脚本启动了 BeeRich。BeeRich 仅仅是在 Remix 的 Express.js 模板上构建的一个简单骨架应用程序，我们在 *第二章*，*创建新的 Remix 应用程序* 中尝试了它。你还可以在本书的 GitHub 仓库中找到 BeeRich Remix 模板：[`github.com/PacktPublishing/Full-Stack-Web-Development-with-Remix`](https://github.com/PacktPublishing/Full-Stack-Web-Development-with-Remix)。

让我们本地运行 BeeRich。打开终端并导航到项目的根目录。然后在终端中执行以下命令：

```js
npm run dev
```

应用程序现在应该已经在端口 `3000` 上运行：`http://localhost:3000/`。欢迎来到 BeeRich！

`npm run dev` 命令启动 Remix 的开发服务器以构建和监视开发环境。你可以在 `package.json` 文件中找到所有可用脚本的列表。此外，请查看 BeeRich 项目的根目录下的 `README.md` 文件以获取有关 BeeRich 的更多信息。

我们已经包含了 Tailwind CSS，这样我们就可以快速添加一些样式，而无需离开我们的 JavaScript 模块。你还可以在 `app/components` 中找到一些样式化的可重用组件。访问 `/demo` 路由来检查一些可重用组件（[`localhost:3000/demo`](http://localhost:3000/demo)）。

随意偏离本书中的课程并探索替代方案。如果你遇到困难，你总是可以重置你的应用程序并使用 GitHub 上当前章节文件夹中的应用程序作为基准。

在下一章中，我们将向 BeeRich 添加页面和路由。

# 摘要

在本章中，你学习了在选择 Remix 应用程序的部署目标时需要考虑什么。你了解了不同的托管提供商、环境和运行时。你现在明白 Remix 可以在长期运行的服务器、无服务器环境和边缘运行时上运行。每个环境都有其优势和劣势，在选择模板和部署目标时你必须考虑这些因素。

Remix 的不同部署目标运行在不同的 JavaScript 运行时上，例如 Node.js、workerd 和 Deno。不同的 JavaScript 运行时支持不同的网络标准，并且可能支持或不支持 Node.js 标准库。在挑选模板时，你必须考虑你想要使用的 JavaScript 运行时。

本章向您介绍了一个九步过程，用于从一种适配器迁移到另一种适配器。更换适配器使您能够尝试不同的托管提供商和环境，并在您的需求随时间变化时保持敏捷。

Remix 为不同的部署目标提供了基本模板，用于设置 Remix 的适配器。然而，Remix 还提供了生产就绪的堆栈。在本章中，您练习了使用`create-remix`脚本来使用 Remix 的 Indie Stack 启动一个新的 Remix 应用程序。

接下来，我们使用自定义模板启动了演示应用程序 BeeRich，我们将在接下来的章节中使用它来练习我们的 Remix 技能。在下一章中，我们将学习 Remix 中的路由并添加 BeeRich 的页面。

# 进一步阅读

您可以在 Remix 文档中找到可用适配器的列表：[`remix.run/docs/en/2/other-api/adapter`](https://remix.run/docs/en/2/other-api/adapter)。

Remix 文档还提供了更多关于可用模板的解释，请在此处查看：[`remix.run/docs/en/2/discussion/runtimes`](https://remix.run/docs/en/2/discussion/runtimes)。

您可以在此处找到 Remix 堆栈的官方公告帖子：[`remix.run/blog/remix-stacks`](https://remix.run/blog/remix-stacks)。

听 Wes Bos 和 Scott Tolinski 在 Syntax.fm 上讨论无服务器限制：[`syntax.fm/show/542/serverless-limitations`](https://syntax.fm/show/542/serverless-limitations)。

在此处了解更多关于无服务器计算和容器之间区别的信息：[`www.cloudflare.com/learning/serverless/serverless-vs-containers/`](https://www.cloudflare.com/en-gb/learning/serverless/serverless-vs-containers/)。

您可以在此处找到有关 V8 隔离器的更多信息：[`developers.cloudflare.com/workers/learning/how-workers-works/#isolates`](https://developers.cloudflare.com/workers/learning/how-workers-works/#isolates)。
