# 9

# React Query v5 的变更有哪些？

在撰写本文时，@tanstack/react-query 的 5.0.0-alpha.1 版本刚刚发布。虽然稳定版本可能需要几周时间才能发布，但本书出版时，它可能已经成为了每次将 React Query 添加到您的项目时默认安装的版本。

为了确保您理解在 v5 发布后本书内容可能经历的变化，我们添加了这一章作为额外内容。

本章也可能作为从 v4 迁移到 v5 的辅助指南。

再次声明，*本章的代码片段是在 @tanstack/react-query 的 5.0.0-alpha.1 版本上测试的。其中一些内容可能仍然会发生变化，或者可能出现一些新的内容。无论如何，这些代码片段将在未来几个月内保持在线更新，直到稳定版本发布。您可以在 *技术要求* 部分提到的 GitHub 仓库中找到它们。

到本章结束时，您将了解 React Query v5 中所有对本书中某些内容有影响的变化。

在本章中，我们将涵盖以下主题：

+   支持变更有哪些？

+   仅使用对象格式

+   移除记录器

+   将 `loading` 重命名为 `pending`

+   将 `cacheTime` 重命名为 `gcTime`

+   将 `Hydrate` 重命名为 `HydrationBoundary`

+   移除 `keepPreviousData` 并使用 `placeholderData`

+   引入了一种新的乐观更新方式

+   向无限查询引入 `maxPages`

# 技术要求

本章的所有代码示例都可以在 GitHub 上找到：[`github.com/PacktPublishing/State-management-with-React-Query/tree/feat/chapter_9`](https://github.com/PacktPublishing/State-management-with-React-Query/tree/feat/chapter_9)。

# 支持变更有哪些？

在这里需要注意的第一件事是浏览器支持已发生变化。从 v5 开始，您的浏览器需要与以下配置兼容：

+   Google Chrome 版本需要至少为版本 84

+   Mozilla Firefox 版本需要至少为版本 90

+   Microsoft Edge 版本需要至少为版本 84

+   Safari 版本需要至少为版本 15

+   Opera 版本需要至少为版本 70

现在我们已经了解了支持变更，让我们看看从 v4 到 v5 的哪些功能发生了变化，首先是自定义钩子和函数的对象格式。

# 仅使用对象格式

在 React Query 的 v4 版本中，大多数自定义钩子和函数都被重载以支持之前的模式。这意味着在您的代码中，以下代码片段中的两个 `useQuery` 钩子将是同一件事：

```js
const { data } = useQuery({
    queryKey: ["api"]
    queryFn: fetchData,
});
const { data } = useQuery(["api"], fetchData);
```

如您从前面的代码片段中看到的，我们两次创建了一个带有 `queryKey` `["api"]` 和 `queryFn` `fetchData` 的查询。这是因为第二个和第一个示例只是同一个被重载的钩子的实例。

随着 v5 的引入，前面代码片段中显示的第二个示例不再受支持；因此，您只能通过传递一个包含所需选项的单个对象来使用您的钩子。以下是您现在需要遵循的语法：

```js
useQuery({ queryKey, queryFn, ...options })
useMutation({ mutationFn, ...options })
useInfiniteQuery({ queryKey, queryFn, ...options })
```

如您从前面的代码片段中看到的，我们有三组 React Query 钩子，并且每个钩子都接收一些东西：

+   `useQuery`和`useInfiniteQuery`钩子需要接收`queryKey`和`queryFn`作为必需参数。这些钩子允许您传递一些您应该已经从上一章中了解到的选项。

+   `useMutation`钩子需要接收`mutationFn`作为必需参数。它还允许您传递一些我们在*第六章*中了解到的选项，当时我们看到了`useMutation`钩子接收到的选项。

幸运的是，在整个书中，我们从一开始就遵循了对象方法，所以您应该从一开始就遵循正确的做法，不会因为变化而受到太大影响。

另一点需要注意是，这个更改适用于`queryClient`函数。例如`invalidateQueries`、`refetchQueries`和`prefetchQuery`等函数也必须接收预期的对象。

现在您已经了解了单对象格式，我们可以看看 v5 中移除的一个东西——`logger`。

# 移除日志记录器

之前，React Query 在生产环境中将失败的查询记录到控制台。这很快成为一个问题，因为我们的应用程序用户能看到他们不应该知道的实现细节错误。为了解决这个问题，添加了创建自定义日志记录器的功能，您可以通过它覆盖 React Query 用于日志记录的内容。

最近，React Query 在生产环境中移除了所有日志记录，并改进了开发日志。考虑到这种情况，在 v5 中，`logger`不再需要，已被移除。

从现在开始，`console`将用作默认日志记录器。

现在您知道了这个更改，让我们看看 v5 的第一个重命名——将`loading`重命名为`pending`。

# 重命名 loading 为 pending

`loading`状态引起了一些混淆。这是因为大多数人将其与数据加载相关联；其次，如果您的查询由于`enabled`选项设置为`false`而被禁用，它将显示为`loading`。为了避免更多的混淆并有一个更清晰的名字，`loading`状态已被重命名。

这里是已应用的变化：

+   `loading`状态已被重命名为`pending`

+   派生的`isLoading`状态已被重命名为`isPending`

+   新增了一个派生的`isLoading`标志，它基本上等同于`isPending && isFetching`表达式

+   考虑到已经有一个名为`isInitialLoading`的标志在做同样的事情，`isInitialLoading`标志已被弃用

让我们现在回顾一下在*第四章*中看到的`ComponentA`，并应用这些更改：

```js
const ComponentA = () => {
  const { data, error, isPending, isError, isFetching } =
    useQuery({
    queryKey: [{ queryIdentifier: "api", apiName: apiA }],
    queryFn: fetchData,
  });
  if (isPending) return <div> Loading data... </div>;
  ...
};
```

如您从前面的代码片段中看到的，我们只需要将`isLoading`重命名为`isPending`。

至于行为，也是一样的。我们需要注意的地方是，在第一次查询挂载后我们没有数据时，我们的`status`查询将是`pending`而不是`loading`，就像之前一样。

考虑到这一点，我们可以转向 v5 的下一个重命名——`cacheTime`现在变为`gcTime`。

# 将`cacheTime`重命名为`gcTime`

这是我个人最开心的变化之一，因为它可能是 React Query 中最被误解的选项。通常，人们认为`cacheTime`意味着数据将被缓存的时间长度，而不是它真正代表的含义，即缓存中不活跃数据在内存中保持的时间。

为了消除这种误解，`cacheTime`选项已被重命名为`gcTime`。这是因为`gc`通常是垃圾回收器的缩写。因此，从现在起，我们明确声明数据被垃圾回收的时间。

要使用它，你只需要将`gcTime`选项添加到你的`useQuery`/`useMutation`钩子中，如下所示：

```js
useQuery({
    gcTime: 60000
});
```

在代码片段中，我们定义了在查询不活跃一分钟之后，数据将被垃圾回收。

为了总结重命名狂潮，让我们看看我们的`Hydrate`组件是如何变化的。

# 将`Hydrate`重命名为`HydrationBoundary`

当在 SSR 中使用 hydrate 模式时，`Hydrate`组件并没有完全描述其含义。为了使其更加简洁并与其他在 TanStack Query 中定义的边界匹配，它被重命名为`HydrationBoundary`。因此，你现在需要在你的 Next.js 或 Remix 代码中将它重命名。

让我们现在看看代码片段是如何变化的。

## Next.js hydrate 模式重命名

这就是我们的 Next.js `_app`组件现在的样子：

```js
import { useState } from "react";
import {
  HydrationBoundary,
  QueryClient,
  QueryClientProvider,
} from "@tanstack/react-query";
export default function App({ Component, pageProps }) {
  const [queryClient] = useState(() => new QueryClient());
  return (
  <QueryClientProvider client={queryClient}>
    <HydrationBoundary state={pageProps.
          dehydratedState}>
      <Component {...pageProps} />
    </HydrationBoundary>
  </QueryClientProvider>
  );
}
```

如您从前面的代码片段中看到的，我们只需要将`Hydrate`重命名为`HydrationBoundary`。其他一切保持不变。

## Remix hydrate 模式更改

这就是我们的 Remix 根组件现在的样子：

```js
import {
  ...
  Outlet,
} from "@remix-run/react";
import { useState } from "react";
import {
  HydrationBoundary,
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
       <HydrationBoundary state={dehydratedState}>
         <Outlet />
       </HydrationBoundary>
     </QueryClientProvider>
   ...
  );
}
```

如您从前面的代码片段中看到的，就像我们在 Next.js 示例中看到的那样，我们只需要将`Hydrate`重命名为`HydrationBoundary`。其他一切保持不变。

现在你知道了这个变化，让我们看看影响我们进行分页查询的方式的一些变化。

# 移除`keepPreviousData`并使用`placeholderData`

`keepPreviousData`选项和`isPreviousData`标志已被移除。这是因为它们几乎与`placeholderData`选项和`isPlaceholderData`标志执行相同的任务。

为了使`placeholderData`能够完全执行与`keepPreviousData`完全相同的功能，之前的查询数据被添加为`placeholderData`函数的参数。这意味着通过利用 React Query 中的`keepPreviousData`自定义函数，`useQuery`将允许`placeholderData`以与之前`keepPreviousData`相同的方式工作。

让我们看看我们的`PaginatedQuery`代码在 v5 中的变化：

```js
import { useQuery, keepPreviousData } from "@tanstack/react-query";
...
const PaginatedQuery = () => {
  ...
  const { isPending, isError, error, data, isFetching,
    isPlaceholderData } =
    useQuery({
      queryKey: userKeys.paginated(page),
      queryFn: fetchData,
      placeholderData: keepPreviousData,
    });
  if (isPending) {
    return <h2>Loading initial data...</h2>;
  }
  ...
  return (
    <>
     ...
        <button
          disabled={isPlaceholderData}
          onClick={() => setPage((old) => old + 1)}
        >
          Next Page
        </button>
...
    </>
  );
};
export default PaginatedQuery;
```

在前面的代码片段中，我们将我们的`PaginatedQuery`组件更改为适应由于删除`keepPreviousData`选项而必要的更改。这是我们所做的：

1.  我们从 React Query 导入我们的`keepPreviousData`辅助函数。

1.  由于我们需要重构组件，我们将`isLoading`重命名为`isPending`。

1.  我们将`isPreviousData`重命名为`isPlaceholderData`。

1.  我们将`keepPreviousData`选项重命名为`placeholderData`，并传递`keepPreviousData`辅助函数。

现在，v5 不仅删除和重命名了一些东西，还增加了一些新功能，包括一种新的执行乐观更新的方法。

# 介绍一种新的执行乐观更新的方法

在执行乐观更新时，你必须始终小心你对缓存所做的更改。一个打字错误或错误可能会意外影响你最初想要更改之外的其他查询。

幸运的是，随着 v5 的发布，TanStack Query 引入了一种执行乐观更新的方法，你可以完全依赖你的 UI 并停止更改你的缓存。

让我们看看如何：

```js
export const NewOptimisticMutation = () => {
  const [name, setName] = useState("");
  const [age, setAge] = useState(0);
  const queryClient = useQueryClient();
  const { data } = useQuery({
    queryKey: userKeys.all(),
    queryFn: fetchAllData,
    retry: 0,
  });
  const mutation = useMutation({
    mutationFn: createUser,
    onSettled: () =>
      queryClient.invalidateQueries({ queryKey: userKeys.
        all() }),
  });
  return (
    <div>
      {data?.map((user, index) => (
        <div key={user.userID + index}>
          Name: {user.name} Age: {user.age}
        </div>
      ))}
      {mutation.isPending && (
        <div key={String(mutation.submittedAt)}>
          Name: {mutation.variables.name} Age:
            {mutation.variables.age}
        </div>
      )}
      <form>
        <input
          name="name"
          type={"text"}
          onChange={(e) => setName(e.target.value)}
          value={name}
        />
        <input
          name="number"
          type={"number"}
          onChange={(e) => setAge(Number(e.target.value))}
          value={age}
        />
        <button
          disabled={mutation.isPaused ||
            mutation.isPending}
          type="button"
          onClick={(e) => {
            e.preventDefault();
            mutation.mutate({ name, age });
          }}
        >
          Add
        </button>
      </form>
    </div>
  );
};
```

在前面的代码片段中，我们可以看到 React Query 允许我们执行乐观更新的新方法。这是我们所做的：

1.  创建姓名和年龄输入的状态变量及其相应的设置器。

1.  获取我们的`queryClient`访问权限。

1.  使用查询工厂`all`函数创建我们的查询，以提供查询键和`fetchAllData`作为`query`函数。

1.  创建我们的突变，使用`createUser`作为突变函数。在这个突变内部，我们利用`onSettled`回调来使我们的查询无效。

1.  在我们的组件返回中，我们创建以下`div`：

    1.  我们使用查询的`data`来显示用户的资料。

    1.  我们使用我们的突变`isPending`标志来告诉我们是否当前有任何突变正在执行。如果这个标志是`true`，我们就可以访问和渲染我们的`mutation`变量在 DOM 上。

    1.  我们使用我们的姓名和年龄输入创建我们的受控表单。

    1.  我们还创建了一个按钮，当点击时，将触发我们的突变，并带上我们的姓名和年龄值。

如你现在所看到的，我们可以执行突变而不改变我们的查询缓存数据。这非常强大，可以节省你因搞乱缓存而造成的许多无意中的头疼。

通过检查前面的代码片段，你可能想知道突变是否与查询位于同一组件上。这意味着如果你有一个位于查询不同位置的突变，你将无法以这种方式执行乐观更新吗？不是的。

如果你想要在别处执行一个突变并执行乐观更新，你可以利用`useMutationState`自定义钩子。

这里是如何做的：

```js
export const NewOptimisticMutationV2 = () => {
  const { data } = useQuery({
    queryKey: userKeys.all(),
    queryFn: fetchAllData,
    retry: 0,
  });

  const [mutation] = useMutationState({
    filters: { mutationKey: userKeys.userMutation(),
      status: "pending" },
    select: (mutation) => ({
      ...mutation.state.variables,
      submittedAt: mutation.state.submittedAt,
    }),
  });
  return (
    <div>
      {data?.map((user, index) => (
        <div key={user.userID + index}>
          Name: {user.name} Age: {user.age}
        </div>
      ))}
      {mutation && (
        <div key={String(mutation.submittedAt)}>
          Name: {mutation.name} Age: {mutation.age}
        </div>
      )}
      <MutationForm />
    </div>
  );
};
```

在前面的代码片段中，我们有`NewOptimisticMutationV2`组件。在这个组件中，我们在突变所在的组件外部执行乐观更新。在这个组件中，我们渲染我们的查询数据，并将我们的突变发生的组件`MutationForm`作为子组件渲染。

在`NewOptimisticMutationV2`组件中，我们这样做：

1.  使用我们的查询工厂`all`函数创建我们的查询，以提供查询键，并将`fetchAllData`作为`query`函数。

1.  通过使用`useMutationState`钩子来获取我们的突变。

1.  通过此钩子，我们访问当前具有挂起状态的突变，以及来自查询工厂的`mutationKey` `userKeys.userMutation()`。

1.  然后，利用`useMutationState`钩子的`select`选项来获取`mutation`变量和`submittedAt`属性。

1.  在我们的组件返回中，我们创建了一个`div`，如下所示：

    1.  我们使用查询的`data`来显示我们的用户数据。

    1.  如果我们现在有任何正在执行的突变，我们可以访问和渲染 DOM 上的`mutation`变量。

在前面的描述中，我提到突变需要`mutationKey`才能被找到。这就是如何将其添加到您的突变中的方法：

```js
const mutation = useMutation({
    mutationFn: createUser,
    mutationKey: userKeys.userMutation(),
  });
```

如您从前面的代码片段中可以看到，我们从查询工厂添加了`userKeys.userMutation()`键，并将其添加到`useMutation`钩子的`mutationKey`属性中。

现在您已经了解了执行乐观更新的新方法，让我们看看我们的无限查询发生了什么变化。

# 介绍无限查询的最大页面数

无限查询是一个帮助您构建无限列表的惊人模式。然而，在 v5 版本之前，它有一个问题——所有获取的页面都缓存在内存中；因此，您看到的页面越多，消耗的内存就越多。

为了防止这种情况发生并提高用户体验，`maxPages`选项被添加到`useInfiniteQuery`钩子中。此选项限制了将存储在查询缓存中的页面数。

这就是我们的无限查询示例，如*第五章*中所示，现在的样子：

```js
const {
    isPending,
    isError,
    error,
    data,
    fetchNextPage,
    isFetchingNextPage,
    hasNextPage,
  } = useInfiniteQuery({
    queryKey: userKeys.api(),
    queryFn: getInfiniteData,
    defaultPageParam: 0,
    maxPages: 5,
    getNextPageParam: (lastPage, pages) => {
      return lastPage?.info?.nextPage;
    },
    getPreviousPageParam: (firstPage, pages) => {
return firstPage?.info?.prevPage
    }
  });
  if (isPending) {
    return <h2>Loading initial data...</h2>;
  }
  ...
```

在前一个代码片段中，我们可以看到 v5 之后的无限查询代码重构，并利用了`maxPages`选项。以下是变化的内容：

1.  我们使用`isPending`而不是`isLoading`

1.  `defaultPageParam`选项指示 React Query 将使用哪个默认页面来获取第一页。此选项现在是必需的，因此已添加。

1.  我们将`5`作为`maxPages`选项。这意味着只有五页将被存储在内存中。由于我们使用了此选项，因此现在需要`getPreviousPageParam`选项，以便 React Query 在需要时可以双向获取页面。

通过这种方式，我们现在已经封装了所有可能影响这本书的 React Query v5 的相关更改。

# 摘要

在本章中，我们了解了 v5 可能给 React Query 带来的所有变化。到现在为止，您应该知道您需要在浏览器中注意的支持更改，并理解为什么我们一直在整本书中遵循对象格式。

您已经看到了为什么移除`logger`，并理解为什么将`loading`重命名为`pending`更有意义。

说到重命名，您不会再感到困惑了，因为 `gcTime` 比 `cacheTime` 更准确，而 `HydrationBoundary` 更好地代表了它的功能，比 `Hydrate` 更为恰当。

您已经了解到，对于分页查询，`placeholderData` 选项是最佳选择，而 `keepPreviousData` 已被移除。

最后，您了解了一种新的执行乐观更新而不更新缓存的方法，通过利用 `maxPages` 选项，您还找到了在无限查询中节省内存的方法。

如您可能从之前我说的话中回忆起来，这是在 React Query 的 alpha 版本中进行的测试，所以其中一些事情可能仍然会改变。

看到这些变化真是令人兴奋，因为它们逐渐改进了这个库。

个人来说，我迫不及待地想看到 TanStack Query 接下来的发展。随着每个新版本的推出，它总是找到一种新的方法让我的服务器状态处理变得更简单。希望从现在开始，它也能为您做到同样的事情。
