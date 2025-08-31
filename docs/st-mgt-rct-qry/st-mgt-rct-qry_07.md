

# 第七章：使用 Next.js 或 Remix 进行服务器端渲染

并非所有我们的应用程序都在客户端渲染。如今，使用利用 **服务器端渲染**（**SSR**）的框架是很常见的。这些框架有助于提高应用程序的性能，并且它们的采用率每天都在增长。

现在，当使用这些框架时，大多数情况下，我们倾向于在服务器端执行数据获取或突变，这引发了一个问题：

我在使用 SSR 框架时还需要 React Query 吗？

在本章中，您将了解 React Query 如何与 `initialData` 和 `hydrate` 等框架相结合。

一旦您熟悉了这些模式，您将了解如何将它们应用到您的 Next.js 和 Remix 应用程序中。

在本章中，我们将涵盖以下主题：

+   为什么我应该使用 React Query 与服务器端渲染框架一起使用？

+   使用 `initialData` 模式

+   使用 `hydrate` 模式

# 技术要求

本章的所有代码示例都可以在 GitHub 上找到：[`github.com/PacktPublishing/State-management-with-React-Query/tree/feat/chapter_7`](https://github.com/PacktPublishing/State-management-with-React-Query/tree/feat/chapter_7)。

# 为什么我应该使用 React Query 与服务器端渲染框架一起使用？

SSR 已经被证明是网络开发者的好帮手。随着全栈框架如 Next.js 和最近出现的 Remix 的流行，React 生态系统已经发生了变化，导致新的模式被应用。

什么是服务器端渲染（SSR）？

SSR 是一个允许你在服务器上而不是在浏览器上渲染应用程序的过程。在这个过程中，服务器将渲染的页面发送到客户端。然后客户端通过一个称为“水合”的过程使页面完全交互式。

由于可以使用 SSR，可能值得做的事情之一是在服务器上获取数据。这有许多优点，但其中最好的是向用户提供已经加载了初始数据的页面。现在，仅仅因为你在服务器端加载数据，并不意味着你不需要在客户端获取数据的情况。如果你的页面包含客户端上频繁更新的数据，React Query 仍然是你最好的朋友。

但 React Query 是如何与 Next.js 或 Remix 等框架结合到我们的代码中的？我们会在服务器上获取数据，然后再在客户端获取吗？

简短的回答是不。如果我们那样做，我们只是在服务器上浪费内存，而没有利用 SSR 的优势。我们可以做的是在服务器端预取数据，并将其提供给 React Query，以便它在客户端管理。这样，当用户获取页面时，页面已经包含了用户所需的数据，从那时起，React Query 就会负责一切。

我们可以将两种模式应用于服务器上的预取数据，并将其发送到客户端的 React Query。它们如下：

+   `initialData` 模式

+   `hydrate` 模式

在下一节中，我们将学习如何利用`initialData`模式并将其应用到所提到的框架中：Next.js 和 Remix。

# 使用 initialData 模式

`initialData`模式是你可以在`useQuery`钩子中设置的选项。使用这个选项，你可以向`useQuery`提供它将用于初始化特定查询的数据。

这就是如何利用服务器端框架的最佳功能和 React Query 的`initialData`选项的过程：

1.  你首先在服务器端预先获取你的数据并将其发送到你的组件。

1.  在你的组件内部，你使用`useQuery`钩子渲染你的查询。

1.  在这个钩子内部，你添加`initialData`选项，并将你在服务器端预先获取的数据传递给它。

现在我们来看看如何在 Next.js 中使用这个模式。

## 在 Next.js 中应用 initialData 模式

在下面的代码片段中，我们将使用 Next.js 的`getServerSideProps`在服务器上获取一些数据，然后利用`initialData`模式将数据传递给 React Query：

```js
import axios from "axios";
import { useQuery } from "@tanstack/react-query";
const fetchData = async ({ queryKey }) => {
  const { username } = queryKey[0];
  const { data } = await axios.get(
    `https://danieljcafonso.builtwithdark.com/
      react-query-api/${username}`
  );
  return data;
};
export async function getServerSideProps() {
  const user = await fetchData({ queryKey: [{ username:
    "danieljcafonso" }] });
  return { props: { user } };
}
export default function InitialData (props) {
  const { data } = useQuery({
    queryKey: [{ queryIdentifier: "api", username:
      "danieljcafonso" }],
    queryFn: fetchData,
    initialData: props.user,
  });
  return <div>This page is server side generated
    {data.hello}</div>;
}
```

在前面的代码片段中，我们将`initialData`模式应用到 Next.js 应用程序中。这里，我们有一个将服务器端生成的组件。这就是我们在这里所做的事情：

1.  我们为这个组件做必要的导入。在这个场景中，是`axios`和我们的`useQuery`钩子。

1.  我们创建我们的查询函数。在这个函数中，我们获取到我们的查询键，并从查询键中解构出用户名以执行我们的`GET`请求。然后我们返回我们的查询数据。

1.  由于我们希望这个页面是服务器端渲染的，所以我们将其中的`getServerSideProps`函数包含在内。这个函数将在服务器端运行，在其中，我们调用我们的`fetchData`函数来获取我们的服务器状态数据并将其作为 props 返回，这些 props 将被发送到我们的`InitialData`组件。

1.  在我们的`InitialData`组件中，我们获取到我们的`props`。在这些`props`中，我们可以访问从我们的`getServerSideProps`函数返回的数据。然后我们将这些数据传递给我们的创建的`useQuery`实例作为`initialData`选项。这意味着这个钩子在重新获取之前将具有我们在构建时获取的数据作为其初始数据。

现在你已经知道如何在 Next.js 中应用这个模式，让我们在 Remix 中做同样的事情。

## 在 Remix 中应用 initialData 模式

在下面的代码片段中，我们将使用 Remix 的`loader`在服务器上获取一些数据，然后利用`initialData`模式将数据传递给 React Query：

```js
import axios from "axios";
import { useQuery } from "@tanstack/react-query";
import { useLoaderData } from "@remix-run/react";
import { json } from "@remix-run/node";
const fetchData = async ({ queryKey }) => {
  const { username } = queryKey[0];
  const { data } = await axios.get(
    `https://danieljcafonso.builtwithdark.com/
      react-query-api/${username}`
  );
  return data;
};
export async function loader() {
  const user = await fetchData({ queryKey: [{ username:
    "danieljcafonso" }] });
  return json({ user });
}
export default function InitialData() {
  const { user } = useLoaderData();
  const { data } = useQuery({
    queryKey: [{ queryIdentifier: "api", username:
      "danieljcafonso" }],
    queryFn: fetchData,
    initialData: user,
  });
  return <div>This page is server side rendered
    {data.hello}</div>;
}
```

在前面的代码片段中，我们将`initialData`模式应用到 Remix 应用程序中。这就是我们在这里所做的事情：

1.  我们为这个组件做必要的导入。在这个场景中，是`axios`、我们的`useQuery`钩子、Remix 的`useLoaderData`钩子和一个`json`函数。

1.  我们创建我们的查询函数。在这个函数中，我们获取到我们的查询键，并从查询键中解构出用户名以执行我们的`GET`请求。然后我们返回我们的查询数据。

1.  我们接下来创建我们的 `loader` 函数。这是 Remix 用来允许你在服务器端加载将在你的组件中需要的数据的函数。在其内部，我们获取我们的数据，然后使用 `json` 函数发送一个带有 `application/json` 内容类型头和包含数据的 `HTTP` 响应。

1.  在我们的 `InitialData` 组件中，我们利用 `useLoaderData` 来获取 `loader` 返回的数据。然后我们将这些数据传递给我们的 `useQuery` 实例作为 `initialData` 选项。这意味着这个钩子在重新获取之前将构建时获取的数据作为其初始数据。

到现在为止，你应该能够使用 `initialData` 模式。为了更有效地使用它，你需要注意以下几点：

+   如果你在不同位置有相同查询的多个实例，你必须始终向它们传递 `initialData`。这意味着即使你在顶层和子组件中利用查询，你也必须通过 prop-drill 将 `initialData` 传递到需要数据的期望组件。

+   由于你在服务器上获取数据并将其传递到你的 hook，React Query 将基于初始页面加载时而不是服务器上获取数据的时间来识别你的查询何时被渲染。

让我们看看当你使用支持服务器端渲染的框架与 React Query 结合时可以利用的第二种模式：`hydrate` 模式。

# 使用 hydrate 模式

使用 `hydrate` 模式，你可以使用之前预取的查询来脱水你的 `QueryClient` 并将其发送到客户端。在客户端，一旦页面加载并且 JavaScript 可用，React Query 将使用现有数据来 hydrate 你的 `QueryClient`。在此过程之后，React Query 也会确保你的查询是最新的。

这是如何利用 `hydrate` 模式结合你的服务器端框架和 React Query 的最佳实践的过程：

1.  你首先要做的是创建一个 `QueryClient` 实例。

1.  使用之前创建的 `QueryClient` 实例，你利用其 `prefetchQuery` 方法来预取给定查询键的数据。

1.  你将你的 `QueryClient` 脱水并发送到客户端。

1.  你的客户端接收脱水状态，将其 hydrate 并与正在使用的 `QueryClient` 合并。

1.  在你的组件内部，你使用 `useQuery` 钩子以与你在 *步骤 2* 中添加的相同查询键渲染你的查询。你的查询将已经包含其数据。

在下一节中，我们将学习如何利用 `hydrate` 模式并将其应用于所提到的框架：Next.js 和 Remix。

## 在 Next.js 中应用 hydrate 模式

Next.js 使用 `_app` 组件来初始化所有页面，并允许你在页面变化之间保持一些共享状态或持久化布局。由于这个原因，我们可以利用它来用 `Hydrate` 包装所有我们的组件。`Hydrate` 包装器负责接收 `dehydratedState` 并将其 hydrate。

让我们看看如何应用这个包装器：

```js
import { useState } from "react";
import {
  Hydrate,
  QueryClient,
  QueryClientProvider,
} from "@tanstack/react-query";
export default function App({ Component, pageProps }) {
  const [queryClient] = useState(() => new QueryClient());
  return (
    <QueryClientProvider client={queryClient}>
      <Hydrate state={pageProps.dehydratedState}>
        <Component {...pageProps} />{" "}
      </Hydrate>
    </QueryClientProvider>
  );
}
```

在前面的代码片段中，我们执行以下操作：

1.  我们进行所有必要的导入以设置我们的组件。在这个场景中，我们从 React 获取 `useState` 函数，并从 React Query 获取 `Hydrate`、`QueryClient` 和 `QueryClientProvider`。

1.  在我们的 `App` 组件内部，我们执行以下操作：

    1.  我们首先创建一个新的 `QueryClient` 实例，并使用 `useState` 钩子将其分配为 `state` 变量。这是因为我们需要确保这些数据不会被我们应用程序的不同用户和请求共享。这将确保我们只创建一次 `QueryClient`。

    1.  我们然后将我们的 `queryClient` 传递给 `QueryClientProvider` 以初始化它，并允许它被我们的 React Query 钩子访问。`QueryClientProvider` 还将包裹我们的 `Component`。

    1.  最后，我们还用 `Hydrate` 包裹了我们的 `Component`。由于 `Hydrate` 需要接收 `dehydratedState`，无论它是否存在，我们从我们的 `App` 中获取 `pageProps` 并将其传递给我们的 `Hydrate` 状态属性。这意味着对于每个接收 `dehydratedState` 作为 `props` 的组件，这些 `props` 将被传递给我们的 `Hydrate` 包装器。

现在，我们已经准备好开始脱水数据。让我们看看我们如何做到这一点：

```js
import axios from "axios";
import { dehydrate, QueryClient, useQuery } from
  "@tanstack/react-query";
const fetchData = async ({ queryKey }) => {
  const { username } = queryKey[0];
  const { data } = await axios.get(
    `https://danieljcafonso.builtwithdark.com/
      react-query-api/${username}`
  );
  return data;
};
export async function getServerSideProps() {
  const queryClient = new QueryClient();
  await queryClient.prefetchQuery(
    [{ queryIdentifier: "api", username: "danieljcafonso" }],
    fetchData
  );
  return { props: { dehydratedState: dehydrate(queryClient) } };
}
export default function SSR() {
  const { data } = useQuery({
    queryKey: [{ queryIdentifier: "api", username:
      "danieljcafonso" }],
    queryFn: fetchData,
  });
  return <div>This page is server-side-rendered
    {data.hello}</div>;
}
```

在前面的代码片段中，我们预取了一些数据，这些数据将被 React Query 脱水和再水化。以下是我们的操作：

1.  我们为此组件进行必要的导入。在这个场景中，它是 `axios`，并且从 React Query 方面，是 `dehydrate` 函数、`QueryClient` 和 `useQuery` 钩子。

1.  我们创建我们的查询函数。在这个函数中，我们获取对查询键的访问权限，并从查询键中解构 `username` 以执行我们的 `GET` 请求。然后我们返回我们的查询数据。

1.  在 `getServerSideProps` 中，我们执行以下操作：

    1.  我们创建一个新的 `QueryClient` 实例。

    1.  然后，我们利用之前创建的实例来预取一个查询，该查询将在 `[{ queryIdentifier: "api", username: "danieljcafonso" }]` 查询键下缓存，并使用 `fetchData` 作为查询函数。

    1.  我们在 `queryClient` 上使用 `dehydrate` 并将其作为 `props` 返回，以便它可以在我们的 `App` 组件中被捕获。

1.  在我们的 `SSR` 组件中，我们使用 `[{ queryIdentifier: "api", username: "danieljcafonso" }]` 作为查询键和 `fetchData` 作为查询函数创建了一个 `useQuery` 钩子。

由于我们从 `getServerSideProps` 函数中返回了 `dehydratedState`，这将作为 `pageProps` 传递并被 `Hydrate` 包装器捕获，包裹我们的组件。这意味着 React Query 将捕获我们的脱水状态，对其进行水化，并将这些新数据与 `QueryClient` 中的当前数据合并。这意味着当 `SSR` 内部的钩子第一次运行时，它将已经从 `getServerSidePros` 预取了数据。

现在你已经知道如何将此模式应用于 Next.js，让我们在 Remix 中这样做。

## 在 Remix 中应用 hydrate 模式

Remix 使用 `root` 组件来定义所有页面的根布局，并允许你在页面变化之间保持一些共享状态。这是通过使用 `Outlet` 组件来实现的。由于这个组件和根级别的 `Outlet`，我们可以利用它来用 `Hydrate` 包装所有我们的组件。

现在，与 Next.js 不同，没有方法可以访问 `pageProps` 来在根级别访问 `dehydratedState`。因此，我们需要安装一个名为 `use-dehydrated-state` 的第三方包。

以下是向你的项目添加 `use-dehydrated-state` 的方法：

+   如果你正在你的项目中运行 npm，请运行以下命令：

    ```js
    npm i use-dehydrated-state
    ```

+   如果你正在使用 Yarn，请运行以下命令：

    ```js
    yarn add use-dehydrated-state
    ```

+   如果你正在使用 pnpm，请运行以下命令：

    ```js
    pnpm add use-dehydrated-state
    ```

`use-dehydrated-state` 允许我们在根级组件中访问我们的脱水状态。

现在，我们可以进行必要的设置以利用 `Hydrate` 和 `QueryClientProvider` 包装器：

```js
import {
  ...
  Outlet,
} from "@remix-run/react";
import { useState } from "react";
import {
  Hydrate,
  QueryClient,
  QueryClientProvider,
} from "@tanstack/react-query";
import { useDehydratedState } from "use-dehydrated-state";
export default function App() {
  const [queryClient] = useState(() => new QueryClient());
  const dehydratedState = useDehydratedState();
  return (
    ...
       <QueryClientProvider client={queryClient}>
         <Hydrate state={dehydratedState}>
           <Outlet />
         </Hydrate>
       </QueryClientProvider>
     ...
  );
}
```

在前面的代码片段中，我们执行以下操作：

1.  我们进行所有必要的导入以设置我们的组件。在这个场景中，我们得到以下内容：

    1.  Remix 的 `Outlet`

    1.  来自 React 的 `useState` 函数

    1.  来自 React Query 的 `Hydrate`、`QueryClient` 和 `QueryClientProvider`

    1.  来自 `use-dehydrated-state` 的 `useDehydratedState` 钩子

1.  在我们的 `App` 组件内部，我们执行以下操作：

    1.  我们首先创建一个新的 `QueryClient` 实例，并使用 `useState` 钩子将其分配为 `state` 变量。这是因为我们需要确保这些数据不会被我们的应用程序的不同用户和请求共享。这将确保我们只创建一次 `QueryClient`。

    1.  然后，我们将 `queryClient` 传递给 `QueryClientProvider` 以启动它，并允许它被我们的 React Query 钩子访问。`QueryClientProvider` 还会包装由 `Outlet` 渲染的组件。

    1.  最后，我们还将 `Outlet` 用 `Hydrate` 包装起来。由于 `Hydrate` 需要在从服务器接收到 `dehydratedState` 时接收它，我们从 `useDehydratedState` 钩子中获取它。这意味着对于从其 `loader` 接收 `dehydratedState` 的每个组件，这些数据将被传递给我们的 `Hydrate` 包装器。

现在，我们已经准备好开始脱水数据。让我们看看如何操作：

```js
import axios from "axios";
import { dehydrate, QueryClient, useQuery } from "@tanstack/react-query";
import { json } from "@remix-run/node";
const fetchData = async ({ queryKey }) => {
  const { username } = queryKey[0];
  const { data } = await axios.get(
    `https://danieljcafonso.builtwithdark.com/
      react-query-api/${username}`
  );
  return data;
};
export async function loader() {
  const queryClient = new QueryClient();
  await queryClient.prefetchQuery(
    [{ queryIdentifier: "api", username: "danieljcafonso" }],
    fetchData
  );
  return json({ dehydratedState: dehydrate(queryClient) });
}
export default function Index() {
  const { data } = useQuery({
    queryKey: [{ queryIdentifier: "api", username:
      "danieljcafonso" }],
    queryFn: fetchData,
  });
  return <div>This page is server side rendered
    {data.hello}</div>;
}
```

在前面的代码片段中，我们正在预取一些数据，这些数据将被 React Query 脱水和再水化。以下是我们的操作：

1.  我们为这个组件进行必要的导入。在这个场景中，它们如下所示：

    1.  `axios` 客户端

    1.  来自 React Query 的 `dehydrate` 函数、`QueryClient` 和 `useQuery` 钩子

    1.  Remix 的 `json` 函数

1.  我们创建我们的查询函数。在这个函数中，我们获取查询键的访问权限，并从查询键中解构用户名以执行我们的 `GET` 请求。然后我们返回我们的查询数据。

1.  在 `loader` 中，我们执行以下操作：

    1.  我们创建一个新的 `QueryClient` 实例。

    1.  我们随后利用先前创建的实例来预取一个查询，该查询将在`[{ queryIdentifier: "api", username: "danieljcafonso" }]`查询键下缓存，并使用`fetchData`作为查询函数。

    1.  然后，我们使用`dehydrate`对`queryClient`进行操作，并将其作为`HTTP`响应返回。

1.  在我们的`Index`组件中，我们使用`[{ queryIdentifier: "api", username: "danieljcafonso" }]`作为查询键，并使用`fetchData`作为查询函数创建一个`useQuery`钩子。

由于我们从`loader`函数中返回了`dehydratedState`，这将由`useDehydratedState`捕获并传递给我们的`Hydrate`包装器，包裹我们的组件。这意味着 React Query 将捕获`dehydratedState`，对其进行解冻，并将这些新数据与`QueryClient`中当前的数据合并。由于这个过程，当`Index`内部的钩子第一次运行时，它已经拥有了从`loader`中预取的数据。

# 摘要

本章教会了我们如何使用 React Query 补充我们的服务器端渲染应用程序。

你学习了如何使用 React Query 在服务器端预取数据并将其发送到客户端的 React Query。为此，你需要了解两种模式，`initialData`和`hydrate`。在`initialData`模式中，你在服务器端预取数据并将其传递到客户端`useQuery`钩子的`initialData`选项中。在`hydrate`模式中，你在服务器端预取查询，解冻查询缓存，并在客户端进行解冻。

在*第八章* *测试 React Query 钩子和组件*中，我们将关注帮助你夜晚睡得更好的事情之一：测试。你将了解如何测试你的组件，即使用 React Query，以及一些自定义钩子来提高你的开发者体验。
