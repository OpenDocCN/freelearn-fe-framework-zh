# 1

# 全栈网络框架时代

“生活中唯一不变的是变化。”

– 赫拉克利特

我们正处于网络开发的激动人心时期。这个领域的变革速度令人惊叹。从未有过如此多的技术可供选择来开发网络应用。感觉每隔一个月就会有一个新的框架或库发布。现有的框架和库发布带有新功能、破坏性更改和新约定的新主要版本。行业的快速步伐可能让人感到压倒，但看到如此高的创新水平也是令人着迷的。

网络开发的大众群体比前沿技术的步伐要慢得多。大多数企业和开发者都在等待看到哪些技术能够持续存在，然后再采用它们——创造出我们可以认为是行业标准的东西。我会把 React 视为这一标准的一部分。然而，快速行动并采用新技术如果能够大幅推动进步，可以成为一种竞争优势。我相信 Remix 就是这样一种技术。

自从您购买这本书以来，您已经决定尝试 Remix——太棒了！作为一个 React 开发者，Remix 为您提供了许多好处，例如以下这些：

+   为您的前端提供后端环境

+   全栈数据突变故事

+   声明式错误处理方法

+   简化的客户端状态管理

+   服务器端渲染

+   React 的最新进展，例如流式传输

+   一个可以在任何地方运行的应用程序运行时，甚至在边缘

+   通过拥抱网络标准实现渐进式增强

使用 Remix，你可以充分利用网络的全功能。这本书将引导你通过这个过程，从基础知识开始，逐步过渡到更高级的技术。在本章的第一部分，我们将讨论以下主题：

+   介绍 Remix

+   Remix 背后的哲学

+   基本元素、约定和杠杆

+   Remix 幕后

首先，我们将介绍 Remix 作为全栈网络框架。然后，我们将研究 Remix 背后的哲学，并介绍一个心智模型来分类 Remix 提供的不同工具。最后，我们将深入了解 Remix 承担的不同责任。

本章的目标是向您介绍 Remix。最重要的是，我们希望展示 Remix 在 React 开发中的优势。我们希望这能激励您开始使用。因此，本章涉及了几个高级概念。但请放心；本书后面将详细研究所有提到的内容。

# 介绍 Remix

虽然 Remix 可以被视为另一个 React 框架，但 Remix 背后的团队强调 Remix 不是一个 React 框架，而是一个**全栈网络框架**——这是一个重要的区别。

在本节中，我们将总结 Remix 作为全栈网络框架的含义。首先，我们将查看网络框架部分，并解释为什么 Remix 真正是网络框架。之后，我们将强调为什么 Remix 是全栈的。

## Remix 是一个 Web 框架

Remix 之所以是 Web 框架，主要原因是它对 Web 平台的深度拥抱。Remix 旨在通过使用 Web 标准来实现“快速、流畅且弹性良好的用户体验”。HTML 表单和锚点标签、URL、cookies、元标签、HTTP 头和**Web Fetch API**都是 Remix 中的第一公民。Remix 的约定、杠杆和原语是精心设计的现有 Web API 和标准的抽象层。这使得 Remix 与其他感觉更脱离 Web 平台的流行框架有所不同。

**开放 Web 平台**是由**万维网联盟**（**W3C**）定义的一系列标准。这包括 JavaScript Web API、HTML 和 CSS、无障碍性指南和 HTTP。Web 标准的推进速度比行业标准慢得多。新的 Web 标准需要很长时间，并且要经过多次迭代才能发布，而且所有浏览器支持它们的时间还要更长。

作为 Web 开发者，您的资源有限。为了最大限度地利用您的时间和精力，关注学习 Web 的核心原则至关重要，这些原则无论您选择什么工具都将适用。学习 Web 的基础知识是可转移的知识，无论您使用什么框架和库都将受益。当使用 Remix 时，您通常会参考 MDN Web 文档而不是 Remix 文档。学习 Remix 意味着学习标准 Web API。

React 在 Remix 中扮演着至关重要的角色。当有道理时，Remix 利用 React 的最新功能。随着 React 18 的推出，React 变得越来越复杂。React 的最新功能更适合框架作者而不是应用程序开发者。Remix 提供了必要的抽象，以利用这些最新进展。

当与 React 配合使用时，Remix 利用客户端路由和数据获取，创建类似于使用 React 构建**单页应用程序**（**SPAs**）的体验。然而，Remix 的范畴比 React 更广，解决了 Web 开发中的其他问题，如缓存、用户会话和数据变更。这使得 Remix 成为一个 Web 框架。

## Remix 是一个全栈框架

让我们看看为什么 Remix 是全栈的。Remix 采用了 Web 平台的客户端/服务器模型。它协调了您的 Web 应用程序的前端和后端：

+   在服务器上，Remix 充当 HTTP 请求处理器

+   在客户端，Remix 协调一个服务器端渲染的 React 应用程序

Remix 充当前端和后端框架。这两个框架是独立的部分，在不同的环境中执行（浏览器和服务器环境）。在您的应用程序运行时，这两个框架通过网络进行通信。

在开发过程中，您创建一个 Remix 应用程序，其中客户端和服务器代码在一个`/app`目录中很好地集中。我们甚至可以在同一文件中编写客户端和服务器代码。

以下代码示例展示了 Remix 中页面/路由文件的样子：

```js
// The route's server-side HTTP action handlerexport async function action({ request }) {
  // Use the web Fetch API Request object (web standard)
  const userId = await requireUserSession(request);
  const form = await request.formData();
  const title = form.get("title");
  return createExpense({ userId, title });
}
// The route's server-side HTTP GET request data loader
export async function loader({ request }) {
  const userId = await requireUserSession(request);
  return getExpenses(userId);
}
// The route's React component
export default function ExpensesPage() {
  // Access the loaded data in React
  const expenses = useLoaderData();
  // Simplify state management with Remix's hooks
  const { state } = useNavigation();
  const isSubmitting = state === "submitting";
  return (
    <>
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
      </Form>
    </>
  );
}
```

在代码示例中，我们使用 Remix 的路由模块 API 定义了一个用于服务器端数据加载的`loader`函数、一个用于数据突变的`action`函数以及用于渲染 UI 的路由的 React 组件。

Remix 将服务器端请求处理程序和应用程序的路由组件本地化。这使得在客户端和服务器之间共享代码变得容易得多，并提供了对应用程序的前端和后端如何协同工作的全面可见性和控制。

在服务器上，我们使用 Remix 的`action`和`loader`函数处理传入的 HTTP 请求并准备响应。在代码示例中，服务器端请求处理程序管理用户会话、加载数据并进行数据突变。我们使用 Web 标准，如 Fetch API、`FormData`和 HTTP cookies。

在路由组件中，我们使用 Remix 的 React 钩子访问服务器端加载的数据并读取过渡状态。我们进一步使用 Remix 的`Form`组件声明式地定义数据突变。在客户端，Remix 运行 JavaScript 来增强浏览器的默认体验。这就是我们利用 React 的力量来构建动态 UI 的地方。

Remix 是一个让你充分利用全栈的 Web 框架。Remix 真正地让你能够释放 Web 平台在 React 开发中的全部潜力。

现在我们已经介绍了 Remix，让我们深入了解其哲学。

# 理解 Remix 背后的哲学

Remix 的使命是让你构建快速、流畅且具有弹性的用户体验。愿景是让你交付人们喜爱的软件。在本节中，我们将更深入地了解 Remix 背后的哲学。你将了解 Remix 的创建以及团队所倡导的价值观。

Remix 是由 Ryan Florence 和 Michael Jackson（在 Twitter 和 GitHub 上的`@ryanflorance`和`@mjackson`）创建的。Ryan 和 Michael 是 React 的老兵，也是 React Router 的作者——这是 React 应用程序最受欢迎的路由库，在`npm`上有超过 10 亿次下载。Remix 的哲学深受 Ryan 和 Michael 在构建和维护 React Router 过程中学到的教训的影响。

最初，Remix 被设计为一个基于许可证的框架。但在 2021 年 10 月，Remix 背后的开发团队宣布他们已经获得了种子资金，并将 Remix 开源。2021 年 11 月，该团队在经过 18 个月的开发后发布了 Remix 的 v1 版本。一年后，在 2022 年 10 月，Shopify 收购了 Remix。现在，Remix 团队完全专注于 Remix 的开发，同时仍在 Shopify 追求相同的使命和愿景。

Remix 不仅开源，而且 Remix 团队还拥抱开放开发。该团队已将路线图以及所有**请求评论**（**RFCs**）和提案公开。他们还进行路线图会议的直播，并积极鼓励社区参与和贡献。目标是尽可能地向社区开放开发过程，同时仍然培养引导 Ryan 和 Michael 的哲学思想。

随着时间的推移，Remix 背后的团队提到了许多对他们来说重要的事情。最重要的是，Remix 的目的是推动 Web 的发展。Ryan 和 Michael 都强调，他们希望看到更好的网站。使命是为您提供构建出色用户体验的工具。他们希望人们喜欢使用您的东西。

Remix 团队在 Remix 文档中很好地总结了其哲学思想。Remix 团队强调的一些要点如下：

+   Remix 旨在避免过度抽象。在 Remix 中，API 被视为 Web 平台之上的一个薄抽象层。简洁是王道。Remix 不会重新发明轮子。

+   Remix 既能展望未来，也能回顾过去。它将尖端技术与经过实战检验的 Web 标准相结合，创造新的方法。Remix 利用 HTTP2 流和边缘部署，同时拥抱 HTML 表单、cookies 和 URL。

+   Remix 逐步增强用户体验，同时不偏离浏览器的默认行为。目标是始终忠于浏览器的默认行为，并在可能的情况下随时回退到它。

+   Remix 是关于解锁 Web 平台的全部功能——或者，正如 Remix 团队所表述的，客户端/服务器模型。

+   Remix 背后的团队非常关注网络标签和您的应用程序包大小。目标是加载更少的内容，尽可能快地加载。

框架为您的应用程序代码提供基础和框架。Remix 团队还将 Remix 称为中心栈（而不是全栈）。Remix 的目的是成为核心，连接到您的应用程序的客户端和服务器两端。它旨在成为中心。

对我来说，Remix 是一款功能强大的工具，具有出色的开发者体验，让我能够为 Web 构建内容。我重视 Remix 提供的 API 的简洁性和实用性。自从我开始使用 Remix 以来，我学到了很多关于 Web 的知识，这都要归功于对使用 Web 平台的强调。Remix 结合了新的方法与旧的方法。使用它令人耳目一新，并且已经开始影响其周围的生态系统。我们现在真正生活在全栈 Web 框架的时代。

Remix 团队推广 Remix 思维方式。例如，Ryan 提出了一种三步法来开发网络体验：

1.  让所有内容在没有 JavaScript 的情况下也能工作。

1.  使用 JavaScript 增强体验。

1.  尽可能地将业务逻辑移至服务器。

在每一步中，我们都是在上一步的基础上构建，以增强体验：

1.  首先，我们专注于在不使用 JavaScript 的情况下构建特性。使用 Remix，我们利用了 Web 平台。我们使用表单来修改数据，并使用服务器端重定向来传递反馈。在特性在不使用 JavaScript 的情况下工作后，我们可能可以发布它并完成。

1.  接下来，我们在客户端使用 JavaScript 来增强用户体验。我们可能会添加乐观 UI、延迟数据加载和实时数据更新。

1.  最后，我们将尽可能多的业务逻辑移至服务器。这允许在客户端 JavaScript 未加载的情况下优雅降级。它还减少了你的应用程序的包大小。

通过使用 Remix 并参与 Remix 社区，你将接触到 Remix 的哲学。将 Remix 的哲学应用到你的开发过程中将真正提升你的 React 开发能力。

Remix 的哲学也可以通过它提供的工具来理解。在下一节中，我想向您介绍框架特性的心智模型。

# 原语、约定和杠杆

在本节中，我们将对 Remix 提供的不同特性进行分类。一个框架为你的应用程序提供了基础和框架。它进一步向你作为开发者暴露工具。我们可以将这些工具分为三个类别：

+   原语

+   约定

+   杠杆

原语、约定和杠杆可以作为一个很好的心智模型来映射 Remix 的不同特性。让我们看看这三个类别如何不同。

## 原语

原语用于你的应用程序代码与框架层交互。它们是将你的应用程序集成到框架提供的基础和框架中的连接线。常见的原语包括函数、钩子、常量、类型、类和组件。框架暴露这些原语，以便你可以在代码中使用它们。艺术在于使原语易于理解，同时足够可组合，以实现强大的业务逻辑。Remix 正是这样做的。

Remix 为你的客户端和服务器端代码提供了原语。Remix 的原语通常是 Web 平台的薄层抽象，并提供与原生原语相似的 API。例如，Remix 的 `Form` 组件接受与原生表单元素相同的属性，但还提供了一些额外的属性来增强体验。

此外，Remix 的原语本身也暴露了标准的 Web API。你在 Remix 中编写的绝大多数服务器端代码都可以访问一个遵循 Web Fetch API 规范的 `Request` 对象。Remix 并没有重新发明轮子。

## 约定

框架还引入了约定。常见的约定包括文件和文件夹命名约定。在前一节中，我们展示了 Remix 中路由文件的代码示例。Remix 的路由文件（路由模块）允许你导出属于 Remix 路由文件命名约定的特定函数。

约定旨在提升开发者体验。例如，基于文件的路由允许你将应用程序的路由结构定义为文件和文件夹层次结构。Remix 编译你的代码并推断你的路由层次结构，因此你无需将路由层次结构定义为代码。

直观的约定可以减少连接应用程序所需的配置量。它们将负担转移到框架上。约定构成了框架与你的应用程序之间的合同，并且可以显著减少你必须编写的样板代码量。

框架的 API 主要由原语和约定组成。所有框架都包含原语，许多框架也利用约定。然而，Remix 特别强调第三类：杠杆。

## 杠杆

杠杆可以理解为选项。Ryan 在关于 Remix 的第一场会议演讲中提出了这个隐喻。Ryan 强调，Remix 只是网络平台上的一个薄抽象层。Remix 让你决定要优化哪些网络关键指标。**首次字节时间**（**TTFB**）或**累积布局偏移**（**CLS**）？加载旋转器还是较慢的页面加载？优化不同的网络关键指标可能是冲突的目标。Remix 提供了杠杆，让你可以引导你的网络应用程序朝着对你正确且重要的方向前进。

杠杆带来了实用性和责任。Remix 提供了原语，但你必须决定如何设计你的应用程序。我相信这种力量使你成为一个更好的网络开发者。但更重要的是，它释放了网络平台的全部潜力。有许多事情需要优化，Remix 提供了杠杆。这使得 Remix 与其他不提供相同类型灵活性和控制的框架区别开来。

现在我们已经为如何分类 Remix 的功能准备了一个心理模型，我们将揭开幕布。Remix 提供了许多出色的功能，并带来了许多生活质量的提升。这是因为它同时承担了多项责任。让我们揭开盖子，了解这意味着什么。

# Remix 幕后

那么，Remix 在幕后是如何工作的呢？根据我们迄今为止所学到的，Remix 似乎做了很多事情。在本节中，我们将揭开幕布。这将帮助你理解 Remix 承担的一些责任。

Remix 提供了出色的开发者体验，但有时也感觉像魔法。一点魔法可以走得很远，但如果不理解发生了什么，也可能让人感到不知所措。如果出现问题，魔法也很难调试。这就是为什么我想在我们创建第一个 Remix 项目之前就揭示一些 Remix 的内部工作原理。

我们可以识别出 Remix 承担的三个不同的责任：

+   Remix 打包你的代码

+   Remix 管理你的应用程序的路由

+   Remix 处理传入的 HTTP 请求

根据确定的职责，Remix 可以分为三个主要组件：编译器、路由器和运行时。在接下来的章节中，我们将检查每个组件。首先，让我们更详细地看看 Remix 作为编译器是如何运作的。

## Remix 是一个编译器

Remix 将基于文件的路线模块编译成代码。它从文件和文件夹层次结构中推断路线结构。对于每个路线模块，Remix 检查它导出的函数，并将路线文件夹转换成运行时使用的数据结构。这使得 Remix 成为一个编译器。

当你使用 Remix 工作时，你会注意到其构建步骤的速度。这要归功于 Remix 使用的构建工具 esbuild。Remix 不暴露 esbuild。因此，Remix 可以调整其打包代码的方式，并通过使用最新的构建工具在未来保持竞争力。如果发布了更快、更强大的构建工具，Remix 明天就可以切换到 esbuild。

Remix 基于 esbuild 将你的 JavaScript 文件打包成以下内容：

+   服务器包

+   客户端包

+   资产清单

Remix 的**命令行界面**（CLI）将你的代码编译成客户端和服务器包。服务器包包含 Remix 的 HTTP 处理程序和适配器逻辑。这是在服务器上运行的代码。客户端构建包含操作 Remix 客户端 React 应用程序的客户端脚本。

Remix 还会根据你的路由层次结构编译一个**资产清单**。客户端和服务器都使用资产清单，其中包含应用程序的依赖关系图信息。清单告诉 Remix 需要加载什么，并允许在页面转换时预取资产和应用程序数据。

说到页面转换...

## Remix 是一个路由器

Remix 为你的客户端和服务器代码实现了一个路由器。这简化了服务器到客户端的移交。前端和后端的深度集成使得 Remix 弥合了网络差距。这允许 Remix 做一些很酷的事情。例如，Remix 并行调用你的路由`loader`函数，以避免请求瀑布。

我们可以简化一下，说 Remix 在底层使用 React Router。更具体地说，Remix 同时使用`react-router-dom`和`@remix-run/router`。`@remix-run/router`是一个前端库/框架无关的包，由 React Router v6 和 Remix 使用。React Router 和 Remix 已经对齐，并具有相似的 API 表面。从许多方面来看，你可以把 Remix 看作是其底层路由解决方案的编译器。

Remix 具有客户端和服务器部分，其路由解决方案也是如此。Remix 暴露的 React 组件确保你的应用程序感觉像一个单页应用（SPA）。Remix 处理表单提交和链接点击。如果加载了 JavaScript，它会阻止浏览器默认行为，但在必要时可以回退到完整页面加载。所有这些都是在运行时由 Remix 的路由器管理的。

说到运行时...

## Remix 是一个运行时

Remix 在现有的服务器上运行，例如 Express.js Node 服务器。Remix 提供适配器以创建与不同服务器端 JavaScript 环境的兼容性。这使得 Remix 的 HTTP 处理器对底层服务器保持无感知。

适配器将来自服务器环境的传入请求转换为标准的 `Request` 对象，并将 HTTP 处理器的响应转换回服务器环境响应的实现。

Remix 通过 JavaScript 服务器接收 HTTP 请求并准备响应。Remix 的路由器知道要加载和渲染哪些路由模块以及要获取哪些资源。在客户端，Remix 激活您的 React 应用程序并协调路由和数据获取。作为一个框架，Remix 为您的应用程序提供基础并执行应用程序的代码。接下来，让我们看看 Remix 不是什么。

## Remix 不是什么

在本章的早期部分，我们介绍了 Remix 并探讨了它提供的许多工具和功能。Remix 是一个全栈 Web 框架，但了解 Remix 不是什么也同样重要。最重要的是，Remix 以下列内容之一：

+   一个 JavaScript 服务器

+   一个数据库

+   一个 **对象关系映射器**（**ORM**）

+   一个云服务提供商

+   一个样式或主题库

+   一个魔法水晶球

Remix 既不是服务器也不是 JavaScript 引擎。Remix 在 Node.js 等 JavaScript 环境上运行，并使用适配器与 Express.js 等 Web 服务器通信。Remix 也不提供应用程序数据层的解决方案。它帮助您加载和修改数据，但实现这些数据加载器和动作是您的工作。您需要选择适合您用例的数据库解决方案。Remix 也不是一个 ORM。因此，您必须在动作和加载器中查询数据，定义您的数据类型，或使用第三方库以获得支持。

Remix 背后的公司不作为云服务提供商或提供云托管服务。您几乎可以在任何可以执行 JavaScript 的地方托管您的 Remix 应用程序。许多云服务默认支持 Remix，但 Remix 公司本身不提供任何托管服务。

Remix 也不是一个样式或主题库。Remix 对如何使用 CSS 有自己的看法，但其解决方案是通用的。除了在前缀路由级别加载样式表的实用工具之外，Remix 不提供用于样式或主题化的工具。

列出的许多内容都不在 Remix 的范围内，但其中一些可能在将来有所变化。现在，让我们专注于现在。在本章的第一部分，我们了解了很多关于 Remix 提供的众多功能。提到的大多数事情都是在幕后发生的。本书将逐步引导您了解 Remix 的每个方面。每一章都将专注于一个特定主题，例如路由、数据获取和修改、错误处理以及状态管理。通过逐一检查这些主题，我们将探讨 Remix 的意义。我当然很兴奋开始编码！

# 摘要

在第一章中，我们将 Remix 介绍为一个全栈 Web 框架。Remix 推崇使用 Web 平台，并让您利用标准 Web API。它通过紧密集成前端和后端来弥合网络差距。这使得 Remix 能够做一些酷的事情。

我们还研究了 Remix 背后的哲学。Remix 背后的团队强调避免过度抽象。其使命是让您能够快速、流畅且稳健地构建用户体验。愿景是让您交付人们会喜爱的软件。

我们引入了术语原语、约定和杠杆来对 Remix 的不同功能进行分类。原语是可以导入并用于我们代码中的暴露工具。约定是诸如文件和文件夹命名约定之类的合同，用于避免繁琐的配置。杠杆是 Remix 提供的选项，允许我们针对对我们重要的事情优化我们的应用程序。

您还了解了 Remix 在幕后所做的工作。Remix 承担了三个不同的职责。它是一个编译器、一个路由器和运行时。将这三个职责结合在一个框架中，可以实现一些了不起的事情，例如通过将客户端和服务器代码放置在一起来简化请求瀑布并避免前端-后端分离。

在本章中，我们提到了许多概念，如服务器端渲染、预取和客户端路由。我们将在本书的后续章节中重新审视所有提到的概念。在下一章中，我们将开始我们的 Remix 开发之旅，并创建一个“Hello World” Remix 应用程序。

# 进一步阅读

如果您还没有查看 Remix 的主页 ([`remix.run`](https://remix.run))，我鼓励您去查看。Remix 背后的团队在阐述其价值主张和提出解决方案方面做得非常出色。而且，它看起来很棒。

Remix 文档也是如此。我鼓励您在阅读本书的章节时熟悉 Remix 的文档。例如，Remix 背后的团队对其哲学进行了出色的总结。您可以在以下链接了解更多：[`remix.run/docs/en/v1/pages/philosophy`](https://remix.run/docs/en/v1/pages/philosophy)。

您是否对 Remix 的深入解释更感兴趣？您可以在以下链接找到关于 Remix 的深入技术解释：[`remix.run/docs/en/2/pages/technical-explanation`](https://remix.run/docs/en/2/pages/technical-explanation)。

您可以在以下链接找到 Remix 与 Shopify 联合的官方公告：[`remix.run/blog/remixing-shopify`](https://remix.run/blog/remixing-shopify)。

公共路线图和大多数其他规划文档都位于 GitHub 上。路线图可以在以下链接找到：[`github.com/orgs/remix-run/projects/5`](https://github.com/orgs/remix-run/projects/5)。

如果你想了解 Remix 与其他技术相比的方法，请查看这篇博客文章：[`remix.run/blog/remix-and-the-edge`](https://remix.run/blog/remix-and-the-edge)。如果你想了解更多关于替代解决方案的背景信息，我推荐以下博客文章：[`frontendmastery.com/posts/the-new-wave-of-javascript-web-frameworks`](https://frontendmastery.com/posts/the-new-wave-of-javascript-web-frameworks).
