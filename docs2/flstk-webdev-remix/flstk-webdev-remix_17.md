

# 第十七章：迁移和升级策略

在本书中，我们探讨了使用 Remix 的许多网络开发方面。您学习了如何使用 Remix 来释放网络平台的全部潜力，并通过构建 BeeRich 完成了全栈应用程序的开发实践。在本章的最后，我们将讨论迁移和升级策略。

本章分为两个部分：

+   迁移到 Remix

+   保持 Remix 应用程序更新

首先，我们将讨论如何迁移到 Remix。不同的应用程序可能需要不同的迁移策略，工作量也各不相同。我们将查看非 React、React 和 React Router 应用程序，并为每个创建一个迁移策略。接下来，我们将学习 Remix 中主要版本升级的推出方式。我们将向您介绍 Remix 的未来标志，并讨论未来标志如何使我们能够逐步升级 Remix 应用程序。

在阅读本章之后，您将了解 Remix 的不同迁移策略。您将了解如何与现有的遗留应用程序并行运行 Remix，以及如何使用 React Router 为迁移准备代码库。此外，您将了解 Remix 如何集成到更广泛的系统架构中。最后，您将学习如何通过未来的标志逐步升级您的 Remix 应用程序。

# 迁移到 Remix

迁移永远不会容易。将现有代码库迁移到新框架会带来困难，可能涉及大量的重构。Remix 也不例外，但某些策略可能会根据现有应用程序架构使迁移不那么痛苦。在本节中，我们将讨论 Remix 的不同迁移策略。让我们首先回顾从非 React 应用程序迁移的案例。

## 将非 React 应用程序迁移到 Remix

从非 React 应用程序迁移到 Remix 是一项具有挑战性的任务，可能非常耗时，具体取决于现有应用程序的大小。迁移的复杂性通常随着持续的功能开发而增加。大多数时候，我们可能无法在迁移时冻结功能开发和错误修复。这导致在迁移现有代码和功能的同时，还必须在旧的和新的应用程序中实现新功能。

一种解决方案可能是并行运行新旧应用程序。通过这样做，我们可以在提高 Remix 应用程序的同时保持我们的遗留应用程序活跃。逐步地，我们可能能够将越来越多的代码迁移到 Remix。

例如，我们可以在子域上托管新的 Remix 应用程序，并在 Remix 中实现新的页面和流程。使用子域，我们可以在两个应用程序之间共享现有的 cookie。

迁移过程可能看起来像这样：

1.  创建一个新的 Remix 应用程序。

1.  在子域上注册 Remix 应用程序以共享 cookie。

1.  在 React 中重新实现可重用组件。

1.  在 Remix 中重新创建页面布局、页脚和导航栏。

1.  在 Remix 中开发新的页面和流程。

1.  将现有页面逐步迁移到 Remix。

通过在 Remix 中开发新页面，我们避免了在旧应用和新应用中实现新功能的需求。相反，我们可以通过两个应用之间路由用户来回。我们可以使用 cookie 和 URL 来共享应用程序状态。

同时运行两个应用仍需要我们在前期做一些工作，例如在 React 中重新实现可重用组件和页面布局，但我们可以避免在能够在生产环境中运行 Remix 之前进行完全切换。

如果我们已经在使用 React，那么迁移应该会更容易。

## 从 React 应用迁移

如果我们维护 React 应用，我们可以重用现有代码库的更大部分。然而，如果我们目前使用的是不同的 React 框架，例如 Gatsby 或 Next.js，那么迁移可能仍然需要在生产环境中同时运行遗留应用和 Remix 应用。

### 从另一个 React 元框架迁移

不同的 React 框架使用不同的路由约定、原语和组件 API。从另一个元框架迁移可能允许我们重用现有的 React 组件，但仍可能需要重构。

从不同的 React 框架迁移的过程可能如下所示：

1.  创建一个新的 Remix 应用程序。

1.  在子域上注册 Remix 应用程序以共享 cookie。

1.  复制、粘贴并适应可重用组件。

1.  复制、粘贴并适应页面布局、页脚和导航栏。

1.  在 Remix 中开发新页面和流程。

1.  将现有页面逐步迁移到 Remix。

我们可能需要重构现有组件以使用 Remix 的原语和实用工具。例如，我们希望重构现有的锚点标签以使用 Remix 的 `Link` 和 `NavLink` 组件。最终，最好是将代码复制到 Remix 中并从那里进行重构。这要求我们在遗留应用和 Remix 应用之间维护重复的代码。

如果我们运行没有框架的仅客户端 React 应用程序，那么会更容易。让我们回顾如何将仅客户端的 React 应用迁移到 Remix。

### 从仅客户端的 React 应用迁移

如果我们维护 Create React App 或 Vite React 应用（仅客户端），我们可能更容易迁移到 Remix，特别是如果应用程序已经使用了 React Router。

在客户端，Remix 运行客户端 React 应用程序，并且大多数 React 代码和客户端请求将像之前一样在 Remix 中工作。因此，我们可以在 Remix 内部运行现有应用。从那里，我们可以逐步重构客户端仅应用的部分到 Remix 路由。

从仅客户端的 React 应用迁移的过程可能如下所示：

1.  创建一个新的 Remix 应用程序。

1.  将现有应用移入新的 Remix 应用程序中。

1.  在 `index` 路由中渲染现有应用。

1.  复制并适应页面布局、页脚和导航栏。

1.  在 Remix 中开发新页面和流程。

1.  将现有页面逐步迁移到 Remix。

我们可能仍然需要复制和粘贴现有的组件来创建与 Remix 兼容的版本。然而，至少目前，我们可以在同一个代码库中这样做。

如果我们使用 React Router 作为客户端路由解决方案，迁移将变得容易得多。

## 从 React Router 迁移

Remix 是由 Michael Jackson 和 Ryan Florence 创建的，他们是 React Router 的创造者。多年来，Remix 一直受到 React Router 开发和维护的深刻影响和启发。

React Router 是一个用于 React 客户端路由的库。自从 Remix 开发以来，Remix 团队还致力于发布 React Router 6 版本，使 React Router 的 API 与 Remix 保持一致。自那时起，Remix 和 React Router 都已重构，以建立在相同的基线路由包之上。

当查看 React Router 6 版本的 API 文档时，你可能会注意到许多熟悉的概念，如 `loader` 和 `action` 函数，许多熟悉的钩子，如 `useLoaderData`、`useActionData`、`useNavigation`、`useSearchParams`、`useFetcher` 和 `useLocation`，以及熟悉的组件，如 `Form` 和 `Link`。

由于 React Router 是一个客户端路由解决方案，因此其 `loader` 和 `action` 函数在客户端执行，而不是在服务器上。然而，React Router 使用与 Remix 相同的导航、数据加载和重新验证流程，这允许我们以相同的思维模型、约定和原语构建 React Router 应用程序。这使得从 React Router 6 版本迁移到 Remix 更加容易。

我们可以为仅使用 React 的应用程序推导出以下迁移过程：

1.  迁移到 React Router 6 版本。

1.  逐步重构代码以使用 React Router 的原语和约定，最重要的是 `loader` 和 `action` 函数。

1.  从 React Router 6 版本迁移到 Remix。

首先，我们需要迁移到 React Router 6 版本。我们可以遵循 React Router 文档中的现有迁移指南。

一旦我们使用 React Router 6 版本，我们就可以随着时间的推移迭代地重构代码。我们将重构现有的 fetch 请求到 React Router 的 `loader` 和 `action` 函数，并利用 React Router 的 `Link` 和 `Form` 组件来实现导航和突变——就像在 Remix 中一样。这也允许我们利用 React Router 的生命周期钩子，如 `useNavigation` 和 `useFetcher`，来实现挂起状态和乐观 UI。

与 Remix 相比，React Router 不使用基于文件的路由约定。如果我们想利用 Remix 的基于文件的路由约定——或者任何其他路由约定——那么我们可能需要在客户端应用程序中开始定义它。例如，将路由组件移动到新的 `routes/` 文件夹，并将 `loader` 和 `action` 函数与 React Router 路由组件一起放置，以匹配 Remix 的路由文件约定。

在某个时候，我们不得不切换并迁移应用程序到 Remix。我们将应用程序与 Remix 的路由约定和数据流越接近，效果就越好。然而，在迁移之前，没有必要将所有内容重构为 `loader` 和 `action` 函数，尽管这样做可能会有所帮助。

我们可以在 Remix 中渲染客户端 React Router 路由，正如前文所述。自然地，这并不像将路由迁移到 Remix 那样有效，但对于大型应用程序来说，这可能是一个有效的选项，以确保及时迁移。

你可以在 Remix 文档中了解更多关于从 React Router 版本 6 到 Remix 的增量迁移信息：[`remix.run/docs/en/main/guides/migrating-react-router-app`](https://remix.run/docs/en/main/guides/migrating-react-router-app)。

既然我们已经讨论了迁移客户端代码的策略，让我们回顾一下后端代码。

## 与后端应用程序一起工作

Remix 的 `loader` 和 `action` 函数在服务器上运行。我们可以使用它们直接从数据库中读取和写入，并使用资源路由实现 webhooks 和服务器端发送事件端点。我们可以使用 Remix 来实现不需要额外后端应用程序的独立全栈应用程序。在本节中，我们将讨论 Remix 如何适应更大的系统架构，以及当存在下游后端应用程序时如何利用 Remix。

在大型应用程序架构中，可能存在更多系统在客户端应用程序和数据库之间。在这种情况下，Remix 将作为我们前端的服务器。

让我们回顾一下 *第一章* 中的代码示例：

```js
export async function action({ request }) {  const userId = await requireUserSession(request);
  const form = await request.formData();
  const title = form.get("title");
  return createExpense({ userId, title });
}
export async function loader({ request }) {
  const userId = await requireUserSession(request);
  return getExpenses(userId);
}
export default function ExpensesPage() {
  const expenses = useLoaderData();
  const { state } = useTransition();
  const isSubmitting = state === "submitting";
return ( <>
      <h1>Expenses</h1>
      {expenses.map((project) => (
        <Link to={expense.id}>{expense.title}</Link>
      ))}
      <h2>Add expense</h2>
      <Form method="post">
        <input name="title" />
        <button type="submit" disabled={isSubmitting}>
          {isSubmitting ? "Adding..." : "Add"}
        </button>
</Form> </>
); }
```

在接收到的请求中，`loader` 函数获取一个支出列表。路由组件渲染支出列表和支出表单，提交时将数据发送到同一路由的 `action` 函数。

注意我们在 `loader` 和 `action` 函数中如何调用 `createExpense` 和 `getExpense` 辅助函数。我们可以实现这些函数以从数据库中读取和写入。然而，我们也可以实现这些函数以从下游后端服务中 `fetch`。

同样，我们可以实现 `requireUserSession` 来向下游认证服务发送请求，而不是在我们的 Remix 应用程序中实现认证代码。最终，Remix 也可以用来将请求转发到后端应用程序并实现 **Backend for Frontend** （**BFF**）模式。

Backend for Frontend

BFF 模式指定了一种软件架构，其中每个前端都有一个专用的后端，用于为前端应用程序的特定需求定制内容。然后后端将请求转发或协调到更通用的下游服务。

我们不需要将我们的后端应用程序与前端应用程序同时迁移到 Remix。相反，我们可以将前端请求转发到遗留的后端应用程序。然后我们可以逐步将后端代码迁移到 Remix 的`loader`和`action`函数中。或者，我们也可以与 Remix 应用程序一起维护后端应用程序。在更大的系统架构中，可能希望仅使用 Remix 作为 Web 服务器，并使用通用的后端服务来实现可跨不同客户端重用的 REST API。

在本节中，你学习了如何将不同的应用程序迁移到 Remix。你现在理解了如何将 Remix 用作 BFF。在下一节中，你将学习如何保持你的 Remix 应用程序更新。

# 保持 Remix 应用程序更新

Remix，就像每个框架一样，经历着持续的维护和开发。较大的更新以包含重大更改的主要版本的形式引入。升级到较新的主要版本可能需要重构，特别是对于大型应用程序，这可能是一项痛苦的任务。Remix 旨在使升级到主要版本尽可能无痛。在本节中，我们将了解如何在 Remix 中逐步迁移到较新的主要版本。

就像大多数开源项目一样，Remix 使用语义版本控制来表示其补丁和更新。语义版本控制提供了一种在确定性的层次结构中记录三种不同类型更改的方法：

1.  `2.x.x`：增加第一个数字的更改是包含重大更改的主要版本。

1.  `x.1.x`：增加中间数字的更改是引入新功能但保持向后兼容的次要版本。

1.  `x.x.1`：增加最后一个数字的更改是向后兼容的错误修复和依赖项补丁。

一个新的主要版本破坏了向后兼容性，这意味着你必须更新现有代码才能升级到该主要版本。这可能是一个痛苦的过程。幸运的是，Remix 团队提供了未来标志来避免一次性升级过程。

未来标志是可以在`remix.config.js`文件中指定的布尔标志：

```js
/** @type {import('@remix-run/dev').AppConfig} */module.exports = {
  future: {
    v2_errorBoundary: true,
    v2_meta: true,
    v2_routeConvention: true,
  },
};
```

当 Remix 团队完成新主要版本的某个功能时，它也会在之前的主要版本中发布该功能，但隐藏在未来的标志后面。这意味着我们可以在下一个主要版本发布之前开始使用之前版本的新功能。通过利用未来标志，我们可以逐步（逐个功能）重构我们的代码。

Remix 团队区分两种类型的未来标志：

+   不稳定标志

+   版本标志

不稳定的未来标志（`unstable_`）用于 API 仍在积极开发且可能发生变化的特性。这些特性是不稳定的，API 可能在未来的版本中被移除或更改。

一旦一个不稳定的功能变得稳定，该功能可能被引入到小版本更新中，或者转换为一个版本未来标志（`vX_`）。基于版本的特性标志允许在当前的 Remix 版本中实现稳定的 API 更改。启用基于版本的特性标志允许开发者为下一个主要版本更新做准备。例如，`v2_meta`未来标志用于在 Remix v1 中启用 Remix v2 的更新元函数 API。

未来标志允许 Remix 团队对 Remix 的原语和约定进行迭代，并逐个发布新功能，在当前的主要版本中。这也允许团队尽早收到反馈，并尽早识别潜在的问题和错误。

未来标志并不消除在现有更改中对现有代码进行重构的需要，但它们允许逐步重构，这些重构可以随着时间的推移而扩展。

# 摘要

在本章中，我们讨论了 Remix 的不同迁移策略。你学习了将非 React、React 和 React Router 应用程序迁移到 Remix 的策略。

对于更大的迁移，你可以在生产环境中并行运行新的 Remix 应用程序和旧的遗留应用程序。你可以在 Remix 中构建新页面，同时逐步将功能从旧应用程序迁移到 Remix。使用子域名为你新的 Remix 应用程序，你可以使用 cookies 共享 UI 状态。

现在，你理解了 React Router 和 Remix 使用相同的基线路由实现。因此，从 React Router 应用程序迁移到 Remix 更容易，因为你可以通过利用共享的原语和约定来逐步准备你的 React Router 应用程序。这允许你在 React Router 和 Remix 应用程序之间重用大量代码，而无需进一步重构。

在阅读本章之后，你现在理解了如何将 Remix 用作 BFF（后端前端）来转发和编排对下游服务的请求。你知道 Remix 可以独立使用，也可以作为更广泛系统架构的一部分。在迁移到 Remix 时，你可以专注于迁移你的前端代码，同时将所有请求从 Remix 的`action`和`loader`函数转发到现有的后端应用程序。

最后，你学习了 Remix 的未来标志系统。Remix 提供未来标志来解锁当前版本中即将到来的主要版本的功能。这允许基于每个功能的逐步升级，并避免了需要一次性更新所有代码的痛苦迁移。

在过去的 17 章中，你学习了构建全栈应用程序所需的许多概念，以使用 Remix。作为 React 开发者，Remix 提供了许多优秀的原语、约定和杠杆，让你能够释放 Web 平台的全潜能。由于 Remix 拥抱 Web 平台的理念，你不仅练习了如何使用 Remix，还了解了许多 Web 标准和概念，例如 Web Fetch API、渐进增强、HTTP 缓存头和 HTTP cookies。

Remix 确实是一个全栈 Web 框架，通过遵循本书中的练习，你了解了全栈 Web 开发的许多方面，例如请求-响应流程、用户认证、会话管理、数据验证，以及实现渐进式、乐观式和实时 UI。我很期待看到你接下来会构建什么。快乐编码！

# 进一步阅读

Remix 文档还包括一篇关于如何从 React Router 迁移到 Remix 的指南：[`remix.run/docs/en/main/guides/migrating-react-router-app`](https://remix.run/docs/en/main/guides/migrating-react-router-app).

Remix 文档还包括 Pedro Cattori 撰写的一篇文章，记录了如何从 webpack 迁移到 Remix 的过程：[`remix.run/blog/migrate-from-webpack`](https://remix.run/blog/migrate-from-webpack).

通过查看 Remix 的发布日志，你可以了解 Remix 的最新发布情况：[`github.com/remix-run/remix/releases`](https://github.com/remix-run/remix/releases).

Sergio Xalambrí撰写了一篇文章，介绍了如何在同一服务器上同时运行 Next.js 和 Remix 以进行增量迁移：[`sergiodxa.com/articles/run-next-and-remix-on-the-same-server`](https://sergiodxa.com/articles/run-next-and-remix-on-the-same-server).

你可以在 GitHub 上找到 Remix 的路线图：[`github.com/orgs/remix-run/projects/5`](https://github.com/orgs/remix-run/projects/5)。你还可以在 YouTube 上找到路线图规划会议：[`www.youtube.com/c/Remix-Run/videos`](https://www.youtube.com/c/Remix-Run/videos).

你可以在这里找到有关语义化版本控制的信息：[`semver.org/`](https://semver.org/).

在 Matt Brophy 的这篇博客文章中了解更多关于 Remix 未来标志的方法：[`remix.run/blog/future-flags`](https://remix.run/blog/future-flags).

你可以在 Remix 文档中了解更多关于 Remix 作为 BFF 的信息：[`remix.run/docs/en/main/guides/bff`](https://remix.run/docs/en/main/guides/bff).
