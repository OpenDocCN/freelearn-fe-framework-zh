

# 第四章：使用 React Query 获取数据

React Query 通过利用其自定义钩子之一`useQuery`，允许你获取、缓存和处理你的服务器状态。为了你的数据能够被缓存，React Query 有一个称为查询键的概念。结合查询键和一些严格的默认值，React Query 将你的服务器状态管理提升到新的水平。

在本章中，你将了解`useQuery`钩子，并理解 React Query 是如何让你获取和缓存数据的。在这个过程中，你将了解所有查询中使用的默认值。你还将了解一些可以用来使你的`useQuery`体验更好的选项。

在熟悉了`useQuery`之后，你就可以开始在特定场景下使用它来重新获取你的查询。你还可以利用`useQuery`的一些额外属性来获取相互依赖的查询。

在本章结束时，我们将回顾一个代码文件，以回顾本章所学的内容。

在本章中，我们将涵盖以下主题：

+   `useQuery`是什么以及它是如何工作的？

+   使用`useQuery`重新获取数据

+   使用`useQuery`获取依赖查询

# 技术要求

本章的所有代码示例都可以在 GitHub 上找到：[`github.com/PacktPublishing/State-management-with-React-Query/tree/feat/chapter_4`](https://github.com/PacktPublishing/State-management-with-React-Query/tree/feat/chapter_4)。

# `useQuery`是什么以及它是如何工作的？

如你在上一章所学，查询是你向异步源发送的请求以获取数据。

在 React Query 文档中，查询也被定义为以下方式：

查询是对异步数据源的声明性依赖，它与一个唯一键相关联。

([`tanstack.com/query/v4/docs/guides/queries`](https://tanstack.com/query/v4/docs/guides/queries))

在掌握了这个概念之后，你现在就可以理解 React Query 是如何利用其自定义钩子`useQuery`来让你订阅查询的。

要使用`useQuery`自定义钩子，你必须像这样导入它：

```js
import { useQuery } from "@tanstack/react-query";
```

下面是`useQuery`的语法：

```js
const values = useQuery({
   queryKey: <insertQueryKey>,
   queryFn: <insertQueryFunction>,
 });
```

如你所见，`useQuery`钩子只需要两个参数即可工作：

+   **查询键**：用于标识你的查询的唯一键

+   **查询函数**：一个返回 Promise 的函数

## 什么是查询键？

查询键是 React Query 用来标识你的查询的唯一值。它还通过使用查询键，React Query 在`QueryCache`中缓存你的数据。查询键还允许你手动与查询缓存进行交互。

查询键需要是一个数组，它可以包含一个字符串或一系列其他值，如对象。重要的是，这个查询键数组中的值必须是可序列化的。

在 React Query v4 之前，查询键不一定需要是一个数组。它可以是单个字符串，因为 React Query 会将其内部转换为数组。所以，如果您在网上找到一些不使用数组作为查询键的示例，请不要觉得奇怪。

这里有一些有效的查询键示例：

```js
useQuery({ queryKey: ['users']  })
useQuery({ queryKey: ['users', 10] })
useQuery({ queryKey: ['users', 10, { isVisible: true }] })
useQuery({ queryKey: ['users', page, filters] })
```

如您所见，只要是一个数组，查询键就是有效的。

作为良好的实践，为了使您的查询键在阅读多个`useQuery`钩子时更加独特和易于识别，您应该将查询的所有依赖项作为查询键的一部分添加。将其视为与您的`useEffect`钩子上的依赖项数组相同的模型。这对于阅读目的很有帮助，因为查询键还允许 React Query 在查询的依赖项发生变化时自动重新获取查询。

需要记住的一点是查询键是确定性散列的。这意味着数组内部项的顺序很重要。

这里有一些查询，当它们的查询键被确定性散列时，它们是同一个查询：

```js
useQuery({ queryKey: ['users', 10, { page, filters }] })
useQuery({ queryKey: ['users', 10, { filters, page }] })
useQuery({ queryKey: ['users', 10, { page, random:
  undefined, filters }] })
```

所有这些示例都是同一个查询——在三个示例中，查询键中数组的顺序保持不变。

现在，您可能想知道这是如何可能的，考虑到对象内部的面页和过滤器每次都会改变位置，在最后一个示例中还有一个名为`random`的第三个属性。这是真的，但它们仍然在对象内部，而这个对象在查询键数组内部的位置没有改变。此外，`random`属性是未定义的，所以在散列对象时，它被排除。

现在，让我们看看一些查询，当它们的查询键被确定性散列时，它们不是同一个查询：

```js
useQuery({ queryKey: ['users', 10, undefined, { page,
  filters }] })
useQuery({ queryKey: ['users', { page, filters }, 10] })
useQuery({ queryKey: ['users', 10, { page, filters }] })
```

所有这些示例都代表不同的查询，因为当查询键是确定性散列时，这些示例最终会变成完全不同的查询。您可能想知道为什么第一个示例与最后一个示例不同。不应该像从`{ queryKey: ['users', 10, { page, random: undefined, filters }] })`对象中消失的那样，`undefined`值消失吗？

不，因为在当前场景中，它不在一个对象内部，并且顺序很重要。当它被散列时，这个未定义的值将在散列键内部被转换为一个 null 值。

现在您已经熟悉了查询键，您可以了解更多关于查询函数的内容。

## 查询函数是什么？

查询函数是一个返回 promise 的函数。这个返回的 promise 将解析并返回数据，或者抛出一个错误。

因为查询函数只需要返回一个 promise，这使得 React Query 变得更加强大，因为查询函数可以支持任何能够执行异步数据获取的客户端。这意味着**REST**和**GraphQL**都得到了支持，所以如果您愿意，您可以同时拥有这两种选项。

现在，让我们看看一个使用 GraphQL 的查询函数示例，以及一个使用 REST 的查询函数示例：

GraphQL

```js
import { useQuery } from "@tanstack/react-query";
import { request, gql } from "graphql-request";
const customQuery = gql`
  query {
    posts {
      data {
        id
        title
      }
    }
  }
`;
const fetchGQL = async () => {
  const endpoint = <add_endpoint_here>
  const {
    posts: { data },
  } = await request(endpoint, customQuery);
  return data;
};
 …
useQuery({
queryKey: ["posts"],
queryFn: fetchGQL
});
```

在前面的代码片段中，我们可以看到一个使用 React Query 和 GraphQL 的例子。这是我们正在做的事情：

1.  我们首先创建我们的 GraphQL 查询，并将其分配给我们的`customQuery`变量。

1.  然后，我们创建`fetchGQL`函数，它将成为我们的查询函数。

1.  在我们的`useQuery`钩子中，我们将相应的查询键传递给钩子，并将我们的`fetchGQL`函数作为查询函数。

现在，让我们看看如何使用 REST 来完成这个操作：

REST

```js
import axios from "axios";
const fetchData = async () => {
  const { data } = await axios.get(
    `https://danieljcafonso.builtwithdark.com/
      react-query-api`
  );
  return data;
};
…
useQuery({
    queryKey: ["api"],
    queryFn: fetchData,
  });
```

在前面的代码片段中，我们可以看到一个使用 React Query 和 REST 的例子。这是我们正在做的事情：

1.  我们首先创建`fetchData`函数，它将成为我们的查询函数。

1.  在我们的`useQuery`钩子中，我们将相应的查询键传递给钩子，并将我们的`fetchData`函数作为查询函数。

这些例子让 React Query 更加闪耀，因为只要你有能够执行异步数据获取的客户端，你就可以在查询函数中使用这个客户端。如前所述，为了确保 React Query 能够正确处理你的错误场景，在使用这些客户端时，我们需要检查的一件事是，当你的请求失败时，它们是否会自动抛出错误。如果它们不会抛出错误，你必须自己抛出错误。

这是在使用`fetch`的查询函数中这样做的方法：

```js
const fetchDataWithFetch = async () => {
  const response = await fetch('https://danieljcafonso.
    builtwithdark.com/react-query-api')
  if (!response.ok) throw new Error('Something failed in
    your request')
  return response.json()
}
```

在前面的代码片段中，使用`fetch`执行请求后，我们检查我们的响应是否有效。如果不是，我们抛出一个错误。如果一切正常，我们返回响应数据。

当你持续创建查询和构建查询函数时，最终会想到的一点是，将查询键传递给查询函数会很有帮助。毕竟，如果查询键代表查询的依赖项，那么在查询函数中可能需要它们是有意义的。

你可以这样操作，有两种模式可以这样做：

+   **内联函数**

+   **查询函数上下文**

### 内联函数

当你的查询键中不需要传递许多参数到查询函数时，你可以利用这个模式。通过编写内联函数，你可以提供对当前作用域中变量的访问，并将它们传递到查询函数。

这里是这个模式的一个例子：

```js
const fetchData = async (someVariable) => {
 const { data } = await axios.get(
   `https://danieljcafonso.builtwithdark.com/
     react-query-api/${someVariable}`
 );
 return data;
};
…
useQuery({
   queryKey: ["api", someVariable],
   queryFn: () => fetchData(someVariable),
 });
```

在前面的代码片段中，我们首先创建一个名为`fetchData`的函数，它将接收一个名为`someVariable`的参数。这个参数随后被用来补充用于获取数据的 URL。当我们到达`useQuery`声明时，由于我们需要将`someVariable`变量用作查询的依赖项，所以我们将其包含在查询键中。最后，在查询函数中，我们创建一个内联函数，该函数将调用`fetchData`并传递我们的`someVariable`值。

如你所见，当我们没有很多参数时，这种模式非常出色。现在，考虑一下这种情况：你的查询键最终有 12 个参数，并且它们都在查询函数内部被需要。这并不是一个坏习惯，但它会稍微影响你的代码可读性。为了避免这些情况，你可以求助于 `QueryFunctionContext` 对象。

### QueryFunctionContext

每次调用查询函数时，React Query 会自动将你的查询键作为 `QueryFunctionContext` 对象传递给查询函数。

这里是使用 `QueryFunctionContext` 模式的一个例子：

```js
const fetchData = async ({ queryKey }) => {
 const [_queryKeyIdentifier, someVariable] = queryKey;
 const { data } = await axios.get(
   `https://danieljcafonso.builtwithdark.com/
     react-query-api/${someVariable}`
 );
 return data;
};
useQuery({
   queryKey: ["api", someVariable],
   queryFn: fetchData,
 });
```

在前面的代码片段中，我们首先创建我们的 `fetchData` 函数。这个函数将接收 `QueryFunctionContext` 作为参数，因此从这个对象中，我们可以立即解构 `queryKey`。正如你在 *什么是查询键？* 部分所知，查询键是一个数组，因此我们传递给函数的参数的顺序对我们传递给查询键的参数顺序很重要。在这个例子中，我们需要 `someVariable` 变量，它是作为我们数组的第二个元素传递的，因此我们解构我们的数组以获取第二个元素。然后我们使用 `someVariable` 来补充用于获取数据的 URL。当我们到达 `useQuery` 声明时，由于我们需要将 `someVariable` 变量用作查询的依赖项，我们将其包含在查询键中。由于它包含在查询键中，它将自动发送到我们的查询函数。

这种模式减少了创建内联函数的需求，并强制要求将你的查询的所有依赖项添加到查询键中。这种模式可能的一个缺点是，当有这么多参数时，你将不得不记住它们在查询键中添加的顺序，以便在查询函数中使用它们。解决这个问题的方法之一是发送一个包含你查询函数中所需的所有参数的对象。这样，你就无需记住数组元素的顺序。

这就是你可以这样做的方式：

```js
useQuery({
   queryKey: [{queryIdentifier: "api", someVariable}],
   queryFn: fetchData,
 });
```

通过将一个对象作为你的查询键传递，该对象将被作为 `QueryFunctionContext` 对象发送到你的查询函数。

然后，在你的函数中，你只需要这样做：

```js
const fetchData = async ({ queryKey }) => {
  const { someVariable } = queryKey[0];
…
};
```

在前面的代码片段中，我们从 `QueryFunctionContext` 对象中解构我们的 `queryKey`。然后，由于我们的对象将是查询键的第一个位置，我们可以从那里解构我们需要的值。

现在你已经理解了每个 `useQuery` 钩子所需的两个选项，我们可以开始查看它返回的内容。

## useQuery 返回什么？

当使用 `useQuery` 钩子时，它返回几个值。要访问这些值，你只需将钩子的返回值分配给一个变量或从钩子的返回值中解构值即可。

你可以这样做：

```js
const values = useQuery(...);
const { data, error, status, fetchStatus }= useQuery(...);
```

在这个代码片段中，我们可以看到访问 `useQuery` 钩子返回值的两种不同方式。

在本节中，我们将回顾 `useQuery` 钩子的以下返回值：

+   `data`

+   `error`

+   `status`

+   `fetchStatus`

### `data`

这个变量是查询函数返回的最后成功解析的数据。

这就是你可以使用`data`变量的方式：

```js
const App = () => {
  const { data } = useQuery({
    queryKey: ["api"],
    queryFn: fetchData,
  });
  return (
    <div>
       {data ? data.hello : null}
    </div>
  );
};
```

在这个代码片段中，我们做了以下操作：

1.  我们从`useQuery`钩子中解构`data`变量。

1.  在我们的返回中，我们检查是否已经从我们的查询中获取了数据。当我们这样做时，我们将渲染它。

当查询最初执行时，这些数据将是未定义的。一旦它执行完成并且查询函数成功解析了你的数据，我们就能访问这些数据。如果由于某种原因，我们的查询函数的 promise 被拒绝，那么我们可以使用下一个变量：`error`。

### `error`

`error`变量让你能够访问查询函数失败后返回的错误对象。

这就是你可以使用`error`变量的方式：

```js
const App = () => {
  const { error } = useQuery({
    queryKey: ["api"],
    queryFn: fetchData,
  });
  return (
    <div>
       {error ? error.message : null}
    </div>
  );
};
```

在前面的代码片段中，我们做了以下操作：

1.  我们从`useQuery`钩子中解构`error`变量。

1.  在我们的返回中，我们检查是否有任何错误。如果有，我们渲染`error`信息。

当查询最初执行时，`error`值将是 null。如果由于某种原因，查询函数拒绝并抛出错误，那么这个错误将被分配给我们的`error`变量。

在`data`和`error`的示例中，我们都检查了它们是否已定义，这样我们就可以让我们的应用程序用户知道我们查询的当前状态。为了使这更容易，并帮助你为应用程序创建更好的用户体验，添加了`status`变量。

### `status`

在执行查询时，查询可以经过几个状态。这些状态帮助你向用户提供更多的反馈。为了让你知道查询的当前状态，创建了`status`变量。

这里是`status`变量可能具有的状态：

+   `loading`：没有查询尝试完成，并且还没有缓存的数据。

+   `error`：在执行查询时出现了错误。每当这是状态时，`error`属性将接收查询函数返回的错误。

+   `success`：你的查询成功并且返回了数据。每当这是状态时，`data`属性将接收查询函数的成功数据。

这就是你可以使用`status`变量的方式：

```js
const App = () => {
  const { status, error, data } = useQuery({
    queryKey: ["api"],
    queryFn: fetchData,
  });
  if(status === "loading") {
    return <div>Loading...</div>
  }
  if(status === "error") {
    return <div>There was an unexpected error:
      {error.message}</div>
  }
  return (
    <div>
       {data.hello}
    </div>
  );
};
```

在前面的代码片段中，我们正在利用`status`变量为我们的用户提供更好的用户体验。这是我们在做的事情：

1.  我们首先从`useQuery`钩子中解构`status`变量。

1.  我们检查`status`是否为加载状态。这意味着我们还没有任何数据，并且我们的查询已经完成。如果情况如此，我们将渲染一个加载指示器。

1.  如果我们的`status`不是加载状态，我们检查查询过程中是否出现了任何错误。如果我们的`status`等于`error`，那么我们需要解构我们的`error`变量并显示错误信息。

1.  最后，如果我们的`status`也不是错误状态，那么我们可以安全地假设我们的`status`等于成功；因此，我们应该有查询函数返回的数据的`data`变量，并且我们可以将其显示给用户。

现在，你已经知道了如何使用`status`变量。为了方便，React Query 还引入了一些布尔变体来帮助我们识别每个状态。它们如下所示：

+   `isLoading`：你的`status`变量处于加载状态

+   `isError`：你的`status`变量处于错误状态

+   `isSuccess`：你的`status`变量处于成功状态

让我们重写我们之前的代码片段，利用我们的`status`布尔变体：

```js
const App = () => {
  const {  isLoading, isError, error, data } = useQuery({
    queryKey: ["api"],
    queryFn: fetchData,
  });
  if(isLoading) {
    return <div>Loading...</div>
  }
  if(isError) {
    return <div>There was an unexpected error:
      {error.message}</div>
  }
  return (
    <div>
       {data.hello}
    </div>
  );
};
```

如你所见，代码是相似的。我们只需要在我们的解构中将`status`变量替换为`isLoading`和`isError`，然后在相应的状态检查中使用`isLoading`和`isError`变量。

现在，`status`变量为你提供了关于你的查询数据的信息。然而，这并不是 React Query 拥有的唯一状态变量。在下一节中，你将介绍`fetchStatus`。

### fetchStatus

在 React Query v3 中，他们发现当处理用户可能离线的情况时存在一个问题。如果用户触发了查询，但在请求过程中由于某种原因失去了连接，`status`变量将保持在加载状态，直到用户重新获得连接并且查询自动重试。

为了处理这类问题，在 React Query v4 中，他们引入了一个新的属性，称为`networkMode`。这个属性可以有三种状态，但默认情况下将使用在线状态。好事是这种模式允许你使用`fetchStatus`变量。

`fetchStatus`变量为你提供了关于你的查询函数的信息。

这里是这个变量可能具有的状态：

+   `fetching`：你的查询函数目前正在执行。这意味着它目前正在获取数据。

+   `paused`：你的查询想要获取数据，但由于失去了连接，它现在已经停止执行。这意味着它目前处于暂停状态。

+   `idle`：查询目前没有任何操作。这意味着它目前处于空闲状态。

现在，让我们学习如何使用`fetchStatus`变量：

```js
const App = () => {
  const {  fetchStatus, data } = useQuery({
    queryKey: ["api"],
    queryFn: fetchData,
  });
  if(fetchStatus === "paused") {
    return <div>Waiting for your connection to return…
      </div>
  }
  if(fetchStatus === "fetching") {
    return <div>Fetching…</div>
  }
  return (
    <div>
       {data.hello}
    </div>
  );
};
```

在前面的代码片段中，我们正在利用`fetchStatus`变量为我们的用户提供更好的用户体验。我们正在做的是：

1.  我们首先从`useQuery`钩子的返回值中解构出`fetchStatus`变量。

1.  我们接着检查我们的`fetchStatus`当前状态是否为暂停。如果是`true`，那么现在没有网络连接，因此我们让我们的用户知道。

1.  如果之前的`If 检查`是`false`，那么我们可以验证我们的`fetchStatus`当前状态是否为获取。如果之前的`If 检查`是`true`，那么现在查询函数正在运行，因此我们让我们的用户知道。

1.  如果我们不是在获取数据，那么我们可以假设我们的查询函数的`fetchStatus`是空闲的；因此，它已经完成了获取，所以我们应该有返回的数据。

现在，你已经知道了如何使用`fetchStatus`变量。就像`status`变量一样，React Query 也引入了一些布尔变体来帮助我们识别这两种状态。它们如下所示：

+   `isFetching`：您的 `fetchStatus` 变量处于获取状态

+   `isPaused`：您的 `fetchStatus` 变量处于暂停状态

让我们利用我们的 `fetchStatus` 布尔变体重写之前的片段：

```js
const App = () => {
  const { isFetching, isPaused, data } = useQuery({
    queryKey" [""pi"],
    queryFn: fetchData,
  });
  if(isPaused) {
    return <div>Waiting for your connection to return...
      </div>
  }
  if(isFetching) {
    return <div>Fetching...</div>
  }
  return (
    <div>
       {data.hello}
    </div>
  );
};
```

如您从片段中看到的，代码相当相似。我们只需将我们的 `fetchStatus` 变量替换为解构中的 `isFetching` 和 `isPaused`，然后在相应的 `fetchStatus` 检查中使用这些 `isFetching` 和 `isPaused` 变量。

既然我们已经了解了 `useQuery` 钩子返回的值，让我们看看我们如何使用一些选项来定制相同的钩子。

## 常用选项解释

当使用 `useQuery` 钩子时，可以传递比查询键和查询函数更多的选项。这些选项帮助您创建更好的开发者体验，以及更好的用户体验。

在本节中，我们将探讨一些更常见且非常重要的选项，您需要了解。

下面是我们要介绍的一些选项：

+   `staleTime`

+   `cacheTime`

+   `retry`

+   `retryDelay`

+   `启用`

+   `onSuccess`

+   `onError`

### `staleTime`

`staleTime` 选项是查询数据不再被认为是 *新鲜* 的毫秒数。当设置的时间过去后，查询会被称为 *过时*。

当查询处于 *新鲜* 状态时，它将从缓存中拉取，而不会触发更新缓存的新请求。当查询被标记为 *过时* 时，数据仍然会从缓存中拉取，但可以触发查询的自动重新获取。

默认情况下，所有查询都使用设置为 `0` 的 `staleTime`。这意味着所有缓存数据默认都会被认为是 *过时* 的。

这是我们如何配置 `staleTime` 的方法：

```js
useQuery({
  staleTime: 60000,
});
```

在这个片段中，我们定义这个钩子的查询数据在一分钟内被认为是 *新鲜* 的。

### `cacheTime`

`cacheTime` 选项是缓存中不活跃数据在内存中保持的时间（以毫秒为单位）。一旦这个时间过去，数据将被垃圾回收。

默认情况下，当没有 `useQuery` 钩子的活动实例时，查询会被标记为不活跃。当这种情况发生时，这个查询数据将保留在缓存中 5 分钟。在这 5 分钟后，这些数据将被垃圾回收。

这就是如何使用 `cacheTime` 选项：

```js
useQuery({
  cacheTime: 60000,
});
```

在片段中，我们定义了在查询不活跃 1 分钟后，数据将被垃圾回收。

### 重试

`retry` 选项是一个值，表示查询失败时是否会重试。当 `true` 时，它会重试直到成功。当 `false` 时，它不会重试。

这个属性也可以是一个数字。当它是一个数字时，查询将重试指定次数。

默认情况下，所有失败的查询都会重试三次。

这就是如何使用 `retry` 选项：

```js
useQuery({
  retry: false,
});
```

在这个片段中，我们将 `retry` 选项设置为 `false`。这意味着当查询失败时，这个钩子不会尝试重新获取数据。

我们也可以这样配置 `retry` 选项：

```js
useQuery({
  retry: 1,
});
```

在这个片段中，我们将数字 `1` 设置为 `retry` 选项。这意味着如果这个钩子无法获取查询，它将只重试请求一次。

### retryDelay

`retryDelay` 选项是在下一次重试尝试之前应用的延迟时间（以毫秒为单位）。

默认情况下，React Query 使用指数退避延迟算法来定义重试之间的时间间隔。

这是使用 `retryDelay` 选项的方法：

```js
useQuery({
  retryDelay: (attempt) => attempt * 2000,
});
```

在这个片段中，我们定义了一个线性退避函数作为我们的 `retryDelay` 选项。每次重试时，这个函数都会接收到尝试次数并将其乘以 `2000`。这意味着每次重试之间的时间间隔将增加 2 秒。

### enabled

`enabled` 选项是一个布尔值，表示您的查询何时可以运行或不能运行。

默认情况下，此值是 `true`，因此所有查询都被启用。

这是使用 `enabled` 选项的方法：

```js
useQuery({
  enabled: arrayVariable.length > 0
});
```

在这个片段中，我们将表达式评估的返回值分配给 `enabled` 选项。这意味着只要 `arrayVariable` 的长度大于 0，这个查询就会执行。

### onSuccess

`onSuccess` 选项是一个函数，当您的查询在获取过程中成功时将被触发。

这是使用 `onSuccess` 选项的方法：

```js
useQuery({
  onSuccess: (data) => console.log("query was successful",
    data),
});
```

在这个片段中，我们将一个箭头函数传递给我们的 `onSuccess` 选项。当我们的查询成功获取数据时，这个函数将使用我们的 `data` 作为参数被调用。然后我们使用这个 `data` 来在我们的 `console` 中进行日志记录。

### onError

当您的查询在获取过程中失败时，`onError` 选项是一个将被触发的函数。

这是使用 `onError` 选项的方法：

```js
useQuery({
  onError: (error) => console.log("query was unsuccessful",
    error.message),
});
```

在这个片段中，我们将一个箭头函数传递给我们的 `onError` 选项。当查询失败时，这个函数将使用 `thrown` 错误作为参数被调用。然后我们在我们的 `console` 中记录错误。

如您所见，`useQuery` 钩子支持很多选项，而之前展示的只是冰山一角。在接下来的章节中，您将了解到更多，所以请做好准备！

您现在熟悉了 `useQuery` 钩子，应该能够使用它来开始获取您的服务器状态数据。现在，让我们看看一些模式和方式，我们可以使用这个钩子来处理一些常见的服务器状态挑战。

# 使用 useQuery 重新获取数据

重新获取数据是管理我们的服务器状态的一个重要部分。有时，您需要更新数据，因为数据已经过时，或者是因为您已经有一段时间没有与页面交互了。

无论手动还是自动，React Query 都支持并允许您重新获取数据。

在本节中，我们将了解它是如何工作的，以及您可以利用哪些自动和手动方式来重新获取数据。

## 自动重新获取

React Query 内置了一些选项，以使您的生命更轻松并保持服务器状态新鲜。为此，它会在某些情况下自动处理数据重新获取。

让我们看看允许 React Query 自动执行数据重新获取的事物。

### 查询键

查询键用于标识您的查询。

在之前讨论查询键时，我多次提到我们应该将所有查询函数的依赖项作为查询键的一部分包括在内。为什么我会这么说？

因为当这些依赖项中的任何一个发生变化时，你的查询键也会发生变化，当你的查询键发生变化时，你的查询将自动重新获取。

让我们看看以下示例：

```js
const [someVariable, setSomeVariable] = useState(0)
useQuery({
    queryKey: ["api", someVariable],
    queryFn: fetchData,
  });
return <button onClick={() => setSomeVariable
  (someVariable + 1)}> Click me </button>
```

在前面的代码片段中，我们定义了一个`useQuery`钩子，其中`someVariable`是其查询键的一部分。这个查询将像往常一样在初始渲染时获取，但当我们点击我们的按钮时，`someVariable`的值将改变。查询键也会改变，这将触发查询重新获取以获取你的新数据。

### 重新获取选项

在“常用选项解释”部分中，我没有分享的一些选项。这是因为它们默认启用，通常最好保留它们，除非它们不适合你的用例。

这里是`useQuery`默认启用的与数据重新获取相关的选项：

+   `refetchOnWindowFocus`：每当你的当前窗口获得焦点时，此选项会触发重新获取。例如，当你返回到你的应用程序并更改标签页时，React Query 将触发数据的重新获取。

+   `refetchOnMount`：每当你的钩子挂载时，此选项会触发重新获取。例如，当一个新的使用你的钩子的组件挂载时，React Query 将触发数据的重新获取。

+   `refetchOnReconnect`：每当你的互联网连接丢失时，此选项将触发重新获取。

有一点很重要，即默认情况下，这些选项只会重新获取你的数据，如果你的数据被标记为过时。即使数据已过时，这种数据重新获取也可以配置，因为所有这些选项（除布尔值外）也支持接收一个值为`always`的字符串。当这些选项的值为`always`时，它将始终重新触发重新获取，即使数据没有过时。

这是如何配置它们的：

```js
useQuery({
    refetchOnMount: "always",
    refetchOnReconnect: true,
    refetchOnWindowFocus: false
  });
```

在前面的代码片段中，我们正在做以下操作：

+   对于`refetchOnMount`选项，我们总是希望我们的钩子在任何使用它的组件挂载时重新获取我们的数据，即使缓存的数据没有过时

+   对于`refetchOnReconnect`，我们希望我们的钩子在我们离线后重新获得连接时重新获取我们的数据，但只有当我们的数据已过时

+   对于`refetchOnWindowFocus`，我们绝不想在窗口聚焦时让我们的钩子重新获取数据

现在，你可能想到的一个问题是是否有任何方法可以强制我们的钩子每隔几秒钟重新获取我们的数据，即使数据没有过时。好吧，即使你没有想过，React Query 也允许你这样做。

React Query 添加了另一个与重新获取相关的选项，称为`refetchInterval`。此选项允许你指定查询重新获取数据的频率（以毫秒为单位）。

这是如何使用它的：

```js
useQuery({
    refetchInterval: 2000,
    refetchIntervalInBackground: true
  });
```

在这个片段中，我们配置我们的钩子以每 2 秒自动重新获取一次。我们还添加了一个名为`refetchIntervalInBackground`的选项，其值为`true`。此选项将允许您的查询即使在窗口或标签页处于后台时也能继续重新获取。

这总结了自动重新获取。现在，让我们看看我们如何在代码中触发手动重新获取。

## 手动重新获取

有两种手动触发查询重新获取的方式。您可以使用`QueryClient`或从钩子中获取`refetch`函数。

### 使用 QueryClient

如您可能从上一章回忆起的，`QueryClient`允许您在开发者和查询缓存之间建立接口。这允许您利用`QueryClient`在需要时强制数据重新获取。

这就是您可以使用`QueryClient`触发数据重新获取的方式：

```js
const queryClient = useQueryClient();
queryClient.refetchQueries({ queryKey: ["api"] })
```

在前面的片段中，我们正在执行以下操作：

+   使用`useQueryClient`钩子来获取对`QueryClient`的访问权限。

+   使用`QueryClient`，我们调用它公开的一个函数，称为`refetchQueries`。此函数允许您触发与给定查询键匹配的所有查询的重新获取。在这个片段中，我们正在触发具有`["api"]`查询键的所有查询的请求。

### 使用重新获取函数

每个`useQuery`钩子都公开一个`refetch`函数以方便使用。此函数将允许您仅触发该查询的重新获取。

这就是您可以这样做的：

```js
const { refetch } = useQuery({
    queryKey: ["api"],
    queryFn: fetchData,
  });
refetch()
```

在这个片段中，我们从`useQuery`钩子中解构了`refetch`函数。然后，我们可以在任何时候调用该函数，以强制该查询重新获取。

现在您已经知道了 React Query 如何使您能够手动和自动重新获取数据，让我们看看我们如何创建依赖于其他查询的查询。

# 使用`useQuery`获取依赖查询

有时，在开发过程中，我们需要使用一个查询返回的值，这些值可以在另一个查询中使用，或者使查询执行依赖于之前的查询。当这种情况发生时，我们需要有一个称为依赖查询的东西。

React Query 允许您通过`enabled`选项使一个查询依赖于其他查询。

这就是您可以这样做的：

```js
const App = () => {
  const { data: firstQueryData } = useQuery({
    queryKey: ["api"],
    queryFn: fetchData,
  });
  const canThisDependentQueryFetch = firstQueryData?.hello
    !== undefined;
  const { data: dependentData } = useQuery({
    queryKey: ["dependentApi", firstQueryData?.hello],
    queryFn: fetchDependentData,
    enabled: canThisDependentQueryFetch,
  });
…
```

在前面的片段中，我们正在执行以下操作：

1.  我们正在创建一个查询，其查询键为`["api"]`，查询函数为`fetchData`函数。

1.  接下来，我们创建一个名为`canThisDependentQueryFetch`的布尔变量，该变量将检查我们的上一个查询是否有我们所需的数据。这个布尔变量将帮助我们决定我们的下一个查询是否可以获取。

1.  然后，我们创建第二个查询，其查询键为`["dependentAPI", firstQueryData?.hello]`，查询函数为`fetchDependentData`函数，以及我们的`canThisDependentQueryFetch`作为`enabled`选项的`Boolean`变量。

当之前的查询完成数据获取后，`canThisDependentQueryFetch`布尔值将被设置为`true`，并启用此依赖查询的运行。

如您所见，您只需要`enabled`选项就可以使一个查询依赖于另一个查询。现在，在结束这一章之前，让我们将我们所学到的所有知识付诸实践。

# 将所有内容付诸实践

到目前为止，你应该能够开始处理一些使用`useQuery`钩子的数据获取用例。

在本节中，我们将查看一个包含三个组件的文件，这些组件分别称为`ComponentA`、`ComponentB`和`ComponentC`，它们正在执行一些数据获取操作。我们将使用这个文件来回顾我们已经学到的概念，并查看我们是否完全理解了`useQuery`的工作方式。

让我们从文件的开始部分开始：

```js
import { useQuery, useQueryClient } from "@tanstack/react-query";
const fetchData = async ({ queryKey }) => {
  const { apiName } = queryKey[0];
  const response = await fetch(
    `https://danieljcafonso.builtwithdark.com/${apiName}`
  );
  if (!response.ok) throw new Error("Something failed in
    your request");
  return response.json();
};
const apiA = "react-query-api";
const apiB = "react-query-api-two";
```

在前面的代码片段中，我们正在做以下操作：

1.  我们从 React Query 包中导入我们的`useQuery`和`useQueryClient`自定义钩子，以便在定义在接下来的几个代码片段中的组件中使用。

1.  我们创建了一个`fetchData`函数，该函数将接收我们的`QueryFunctionContext`。然后我们从其中解构出`queryKey`。在这个函数内部，我们执行以下操作：

    1.  在这些示例中，我们将使用一个对象作为查询键，这样我们就可以知道数组的第一个位置将包含我们的查询键属性，因此我们从其中解构出`apiName`。

    1.  我们使用`fetch`触发对 URL 的`GET`请求，并使用`apiName`来帮助定义路由。

    1.  因为我们使用的是`fetch`而不是`axios`，所以我们需要手动处理请求失败的情况。如果我们的响应不是 OK，那么我们需要抛出一个错误，以便`useQuery`能够处理错误场景。

    1.  如果我们的响应是有效的，那么我们可以返回我们的响应数据。

1.  然后，我们创建了两个 API 常量值，分别称为`apiA`和`apiB`，它们定义了组件将使用的路由。

现在，让我们继续我们的文件，并查看我们的第一个组件，称为`ComponentA`：

```js
const ComponentA = () => {
  const { data, error, isLoading, isError, isFetching } =
    useQuery({
    queryKey: [{ queryIdentifier: "api", apiName: apiA }],
    queryFn: fetchData,
    retry: 1,
  });
  if (isLoading) return <div> Loading data... </div>;
  if (isError)
    return (
      <div> Something went wrong... Here is the error:
        {error.message}</div>
    );
  return (
    <div>
      <p>{isFetching ? "Fetching Component A..." :
        data.hello} </p>
      <ComponentB/>
    </div>
  );
};
```

让我们回顾一下`ComponentA`：

1.  我们首先通过使用`useQuery`钩子来创建我们的查询：

    1.  这个查询使用一个对象作为查询键。这个对象有`api`作为`queryIdentifier`属性和`apiA`作为`apiName`属性。

    1.  这个查询的查询函数是`fetchData`函数。

    1.  通过使用`retry`选项，我们还指定如果这个查询在获取数据时失败，那么钩子将只重试请求一次。

    1.  我们还从钩子中解构出`data`、`isLoading`、`isError`和`isFetching`。

1.  如果没有查询尝试完成，并且仍然没有缓存的数据，我们希望向用户显示我们正在加载数据。我们使用`isLoading`和`If`检查来实现这一点。

1.  如果有错误，我们希望显示它。我们使用`isError`来检查是否有任何错误。如果有，我们渲染那个错误。

1.  如果我们的查询没有加载或出现错误，那么我们可以假设它是成功的。然后我们渲染一个包含以下内容的`div`：

    +   一个`p tag`将检查我们的钩子`isFetching`。如果正在获取，它将显示`Fetching Component A`。如果不正在获取，它将显示获取到的数据。

    +   我们的`ComponentB`。

现在，让我们看看`ComponentB`：

```js
const ComponentB = () => {
  const { data } = useQuery({
    queryKey: [{ queryIdentifier: "api", apiName: apiB }],
    queryFn: fetchData,
    onSuccess: (data) => console.log("Component B fetched
      data", data),
  });
  return (
    <div>
      <span>{data?.hello}</span>
      <ComponentC parentData={data} />
    </div>
  );
};
```

这就是我们`ComponentB`中正在做的事情：

1.  我们首先通过使用`useQuery`钩子创建我们的查询：

    1.  此查询通过一个对象作为查询键进行标识。此对象具有`api`作为`queryIdentifier`属性和`apiB`作为`apiName`属性。

    1.  此查询具有`fetchData`函数作为查询函数。

    1.  我们使用`onSuccess`选项并传递一个函数，该函数将接收我们的`data`并在我们的`console`上记录它，以及指示此组件已获取数据。

    1.  我们还从钩子中解构`data`。

1.  然后我们返回一个`div`以进行渲染，如下所示：

    +   我们从获取的数据中获取的`hello`属性。你可能看到的一件事是我们使用了`?.`运算符。我们利用可选链来确保没有错误，并且只有当我们的数据定义时，我们才渲染`hello`属性。

    +   我们的`ComponentC`。此组件将接收我们的`ComponentB`数据作为其`parentData`属性。

让我们通过查看`ComponentC`来总结我们的文件审查：

```js
const ComponentC = ({ parentData }) => {
  const { data, isFetching } = useQuery({
    queryKey: [{ queryIdentifier: "api", apiName: apiA }],
    queryFn: fetchData,
    enabled: parentData !== undefined,
  });
  const queryClient = useQueryClient();
  return (
    <div>
      <p>{isFetching ? "Fetching Component C..." :
        data.hello} </p>
      <button
        onClick={() =>
          queryClient.refetchQueries({
            queryKey: [{ queryIdentifier: "api",
              apiName: apiA }],
          })
        }
      >
        Refetch Parent Data
      </button>
    </div>
  );
};
export default ComponentA;
```

因此，这是`ComponentC`中正在发生的事情：

1.  我们首先通过使用`useQuery`钩子创建我们的查询：

    1.  此查询通过一个对象作为查询键进行标识。此对象具有`api`作为`queryIdentifier`属性和`apiA`作为`apiName`属性。

    1.  此查询具有`fetchData`函数作为查询函数。

    1.  我们使用`enabled`选项使此查询依赖于`parentData`；因此，此查询只有在`ComponentB`中的查询完成并解析数据后才会运行。

    1.  我们从钩子中解构`data`和`isFetching`。

1.  我们使用`useQueryClient`钩子来获取对`QueryClient`的访问权限。

1.  最后，我们返回一个将渲染以下内容的`div`：

    +   一个`p`标签，将检查我们的钩子`isFetching`。如果正在获取，则显示`Fetching Component C`。如果不，则显示获取的数据。

    +   一个按钮，当点击时，将使用`queryClient`重新获取查询键具有`api`作为`queryIdentifier`属性和`apiA`作为`apiName`属性的查询。这意味着在此按钮点击时，`ComponentA`和`ComponentC`中的`useQuery`都将重新获取一些数据。

此外，在前面的代码片段中，我们默认导出我们的`ComponentA`，因此它是此文件的入口点。

现在我们已经看到了代码文件，让我们回顾钩子的生命周期并了解后台发生了什么：

+   当`ComponentA`渲染时，以下情况会发生：

    +   一个具有`[{ queryIdentifier: "api", apiName: apiA }]`查询键的`useQuery`实例挂载：

        +   由于这是第一次挂载，没有缓存也没有之前的请求，因此我们的查询将开始获取我们的数据，其`status`将为加载状态。此外，我们的查询函数将作为`QueryFunctionContext`的一部分接收我们的查询键。

        +   当我们的数据获取成功时，数据将根据`[{ queryIdentifier: "api", apiName: apiA }]`查询键进行缓存。

        +   由于我们假设默认的`staleTime`，其值为`0`，钩子将标记其数据为过时。

+   当`ComponentA`渲染`ComponentB`时，以下情况会发生：

    +   具有查询键`[{ queryIdentifier: "api", apiName: apiB }]`的`useQuery`实例挂载：

        +   由于这是第一次挂载，没有缓存也没有之前的请求，因此我们的查询将开始获取数据，其`状态`将是加载中。此外，我们的查询函数将作为`QueryFunctionContext`的一部分接收我们的查询键。

        +   当我们的数据获取成功时，数据将根据`[{ queryIdentifier: "api", apiName: apiB }]`查询键进行缓存，并且钩子将调用`onSuccess`函数。

        +   由于我们假设默认的`staleTime`，即`0`，钩子将标记其数据为过时。

+   当`ComponentB`渲染`ComponentC`时，以下情况发生：

    +   具有查询键`[{ queryIdentifier: "api", apiName: apiA }]`的`useQuery`实例挂载：

        +   由于此钩子与`ComponentA`中的钩子具有相同的查询键，钩子下已经缓存了数据，因此数据可以立即访问。

        +   由于此查询在之前的获取后标记为过时，此钩子需要重新获取它，但它需要等待查询首先启用，因为此查询依赖于我们首先拥有`ComponentB`的数据。

        +   一旦启用，查询将触发重新获取。这使得`ComponentA`和`ComponentC`上的`isFetching`都变为`true`。

        +   一旦获取请求成功，数据将根据`[{ queryIdentifier: "api", apiName: apiA }]`查询键进行缓存，并且查询再次标记为过时。

+   现在，考虑到它是父组件，让我们设想一个场景，其中`ComponentA`卸载：

    +   由于不再有任何具有`[{ queryIdentifier: "api", apiName: apiA }]`查询键的活动查询实例，默认的缓存超时设置为 5 分钟。

    +   一旦过去 5 分钟，此查询下的数据将被删除并回收。

    +   由于不再有任何具有`[{ queryIdentifier: "api", apiName: apiB }]`查询键的活动查询实例，默认的缓存超时设置为 5 分钟。

    +   一旦过去 5 分钟，此查询下的数据将被删除并回收。

如果你能够跟踪此前的过程以及查询在它们使用过程中的生命周期，那么恭喜你：你理解了`useQuery`是如何工作的！

# 摘要

在本章中，我们学习了`useQuery`自定义钩子以及它是如何通过使用其必需的选项（称为查询键和查询函数）来获取和缓存数据的。你学习了如何定义查询键以及你的查询函数如何允许你使用任何数据获取客户端，如 GraphQL 或 REST，只要它返回一个承诺或抛出一个错误。

你还了解了一些`useQuery`钩子返回的内容，例如查询的`data`和`error`。为了构建更好的用户体验，你还介绍了`status`和`fetchStatus`。

为了让你自定义开发者体验并将其提升到下一个层次，你了解了一些常用的选项，你可以使用这些选项来自定义你的`useQuery`钩子，使其按你的意愿运行。为了你的方便，以下是一些需要注意的编译默认值：

+   `staleTime`: 0

+   `cacheTime`: 5 * 60 * 1,000 (5 minutes)

+   `retry`: 3

+   `retryDelay`: 指数退避延迟算法

+   `enabled`: True

在结束之前，你了解了一些处理服务器状态挑战（如重新获取和依赖查询）的模式。

最后，你将所学的一切付诸实践，并回顾了一个示例，展示了如何利用所有这些知识，以及当你这样做时`useQuery`钩子是如何在内部工作的。

在*第五章*《更多数据获取挑战》中，你将继续学习如何使用`useQuery`钩子来解决一些更常见的服务器状态挑战，例如数据预取、分页请求和无限制查询。你还将使用开发者工具来帮助你调试查询。
