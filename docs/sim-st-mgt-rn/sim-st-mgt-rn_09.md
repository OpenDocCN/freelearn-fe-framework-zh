

# 第九章：使用 React Query 进行服务器端驱动状态管理

欢迎您，我亲爱的读者，来到最后一章，本章将描述我们 Funbook 应用的状态管理解决方案。在前一章中，我们探讨了（截至本书编写时）最年轻的状态管理库——**Jotai**。Jotai 是一个基于 Facebook 团队在他们的开源库**Recoil**中提出的想法的极简解决方案。**React Query**同样也是极简的，但意义却大不相同。React Query 是为了在服务器上管理获取和修改数据而创建的。在本章中，我们将探讨 React Query 能提供什么。我们将首先对这个库进行广泛的了解；然后我们将实现它用于数据获取。鉴于我们当前的应用设置，我们没有真实的后端服务器进行通信，所以我们只能从理论上查看数据修改。我们还将查看 React Query 团队为**React Native**创建的一些专用实用工具。

下面是我们将在本章中涵盖的主题列表：

+   什么是 React Query，为什么它会在本书中？

+   安装和配置 React Query

+   使用 React Query 进行数据获取

+   其他 React Query 功能

+   React Query 的 React Native 实用工具

到本章结束时，你将很好地理解如何使用 React Query 来提升你的开发体验和代码库。你将掌握如何使用 React Query 处理数据获取，并对该库的其他功能有一般了解。

# 技术要求

为了跟上本章的内容，你需要具备一些**JavaScript**和**ReactJS**的知识。如果你已经阅读了本书的至少**第一章**到**第四章**，你应该能够无任何问题地继续前进。

随意使用你选择的 IDE，因为**React Native**不需要任何特定功能。目前，前端开发者中最受欢迎的 IDE 包括微软的 VSCode、Atom、Sublime Text 和 WebStorm。

本章提供的代码片段旨在说明我们应该如何使用代码。它们并不提供完整的画面。为了在阅读本章的同时获得更好的编码体验，请在你的 IDE 中打开 GitHub 仓库，查看其中的文件。你可以从名为`example-app-full`或`chapter-9`的文件夹中的文件开始。如果你从`example-app-full`开始，你将负责实现本章中描述的解决方案。如果你选择查看`chapter-9`，你将看到我实现的整个解决方案。

如果你遇到困难或迷失方向，可以查看 GitHub 仓库中的代码：[`github.com/PacktPublishing/Simplifying-State-Management-in-React-Native/tree/main/chapter-9`](https://github.com/PacktPublishing/Simplifying-State-Management-in-React-Native/tree/main/chapter-9)。

# 什么是 React Query，为什么它会在本书中？

首先，让我们谈谈这个库的名字。在本章中，我使用的是 React Query 这个名字，它也是一个常用的名字。然而，React Query 的创造者 *Tanner Linsley* 在 2022 年对他拥有的和维护的开源库进行了一些重构。他创建了一个总称，**TanStack**，并将大量库放在这个名字下。因此，React Query 变成了 TanStack Query，从 React Query 版本 4 开始。你可以在本章末尾的 *进一步阅读* 部分找到 TanStack 主页的链接。

现在我们已经解决了名字的问题，让我们来谈谈 React Query 在本书中的位置。React Query **不是**一个状态管理库。它是一个提供在服务器上舒适地进行数据获取和数据变更的解决方案的库。为什么我们要讨论它呢？因为高效地与服务器通信可以替代任何全局状态管理的需要。鉴于我们现实生活中的社交媒体应用克隆，我们在每一章中都在管理点赞的图片。如果我们每次用户点赞图片时都向服务器发送那个信息，或者当用户访问 **FavoritedImages** 表面时从服务器拉取列表的最新版本，会怎么样呢？你可能认为：“哇，那会有很多请求！很多加载状态，应用就会变得毫无用处……” 你是对的！除非你使用 React Query。React Query 不仅简化了数据获取，还管理了缓存值、刷新值、后台获取以及更多。

现在我们已经对 React Query 有了一个理论上的理解，我们可以开始编码了。让我们来玩一玩这个非状态管理库。

# 安装和配置 React Query

安装这个库与其他依赖项没有不同，我们需要运行一个安装脚本。要使用 `npm` 来做这件事，请输入以下内容：

```js
$ npm i @tanstack/react-query
```

或者，如果你更喜欢使用 `yarn`，请输入以下内容：

```js
$ yarn add @tanstack/react-query
```

一旦安装了库，我们需要添加一些最小化的模板代码。我们需要让我们的应用知道我们正在使用 React Query。我们需要使用一个特殊的包装器。你看到我在说什么了吗？是的！我们将使用一个提供者，如下所示：

```js
// App.js
import {
  QueryClient,
  QueryClientProvider,
} from '@tanstack/react-query'
//…
const queryClient = new QueryClient()
export default function App() {
//…
  return (
    <SafeAreaProvider>
    <QueryClientProvider client={queryClient}>
//…
       </QueryClientProvider>
    </SafeAreaProvider>
  );
}
//…
```

我们将首先从 React Query 导入必要的函数——`QueryClient` 和 `QueryClientProvider`。然后，我们将创建一个新的 `QueryClient` 函数并将其传递给 `QueryClientProvider`。我们的应用现在可以使用 React Query 的功能，而不是简单的获取。

这是一个确保你的应用在模拟器或设备上正确运行的好时机。

一旦你确认安装新的依赖项没有在你的项目中造成意外的破坏，我们就可以在下一节中实现使用 React Query 的真实数据获取了。

# 使用 React Query 进行数据获取

正如你所知，我们需要为我们的应用获取一些不同的数据。我们将获取头像列表、用于动态界面的图片列表、用于“收藏的图片”界面的图片列表以及会话列表。我们可以自由地在任何地方添加 React Query 的获取操作。对于简单的查询，我们可以在组件中使用库提供的 `useQuery` 钩子。我们也可以编写自己的自定义钩子，包含更多的逻辑或条件。让我们从一个最简单的例子开始：查询服务器以检查用户是否已登录。

为了在设置导航以显示登录屏幕或否的顶层组件中使用 React Query 钩子，我们需要稍微重新组织一下我们的代码。我们不能在同一个组件的返回语句中同时使用 `QueryClientProvider` 和 `useQuery` 钩子。让我们将主组件的名称从 `App` 改为 `AppWrapped`，并在 `App.js` 文件中添加这个新的应用组件：

```js
// App.js
export default function App() {
    return (
      <QueryClientProvider client={queryClient}>
      <AppWrapped />
    </QueryClientProvider>
  )
};
```

现在，让我们将主组件的名称从 `App` 改为 `AppWrapped`，并从子组件中移除 `QueryClientProvider`。让我提醒你，如果你在代码示例中迷路了，可以查看 GitHub 仓库：[`github.com/PacktPublishing/Simplifying-State-Management-in-React-Native/tree/main/chapter-9`](https://github.com/PacktPublishing/Simplifying-State-Management-in-React-Native/tree/main/chapter-9)。

我们的 `AppWrapped` 组件应该准备好使用 `useQuery` 钩子。确保你首先按照以下方式导入它：

```js
// App.js
import {
  useQuery,
//…
} from '@tanstack/react-query'
//…
const fetchLoginStatus = async () => {
    const response = await fetch(requestBase +
      "/loginState.json");
    return response.json();
  }
const AppWrapped = () => {
  const { data } = useQuery(['loginState'],
    fetchLoginStatus);
//…
{!data?.loggedIn ? (
    <Stack.Screen name='Login' component={Login} />
          ) : (
            <>
               <Stack.Screen
                name='Home'
//…
```

在你导入 `useQuery` 钩子之后，你需要创建一个负责从服务器获取和等待数据的函数。这个函数是 `fetchLoginStatus`，我们将将其传递给 `useQuery` 钩子。这个函数可以创建在任何你想要的文件中。一旦我们设置了获取操作，我们需要在组件中使用 `useQuery` 钩子。我们引入了一个解构的对象键 `data`，其中我们检查 `loggedInStatus` 的值。

对象解构

根据你使用现代 JavaScript 的频率，你可能已经注意到了解构语法，其中 `const` 关键字后面跟着花括号或方括号中的项。这种语法称为解构赋值，用于从数组（方括号）、对象或属性（花括号）中提取值。

`const { data } = objectWithADataItem` 与 `const data = objectWithADataItem.data` 是相同的。

现在我们已经看到了一个简单的例子，让我们看看稍微复杂一点的内容，创建一个自定义钩子和一个依赖查询。

## 获取图像数据

获取图像数据可能就像获取登录状态数据一样简单；然而，我想谈谈一些更复杂的事情。所以，我们将通过确保只有在用户登录后才能获取图像来人为地使我们的生活复杂化。我们将从在新建的 `queries` 文件夹内创建一个名为 `useCustomImageQuery` 的自定义钩子开始。我们的自定义钩子将返回一个 `useQuery` 钩子：

```js
// src/queries/useCustomImageQuery
import { useQuery } from "@tanstack/react-query";
import { requestBase } from "../utils/constants";
const getImages = async () => {
  const response = await fetch(requestBase +
    "/john_doe/likedImages.json");
  return response.json();
}
export const useCustomImageQuery = () => {
  const { data } = useQuery(['loginState']);
  return useQuery(
    ["imageList"],
    getImages,
    {
    enabled: data?.loggedIn,
  });
};
```

我们首先导入了必要的 `useQuery` 函数和我们的工具函数 `requestBase`。接下来，我们创建了一个名为 `getImages` 的获取函数。这个函数从指定的 API 端点获取数据并返回它。最后，我们创建了一个自定义钩子，名为 `useCustomImageQuery`。在钩子的第一行，我们检查 `loginState` 查询。它看起来与我们在 `App.js` 中首次使用它的样子不同，不是吗？它只有一个参数：`loginState`。在这个 React Query 的世界里，这个参数被称为 **查询键**，它实际上是解锁 React Query 力量的钥匙。使用这个键，你可以访问任何之前获取的数据；你也可以手动使其无效或修改它。至于我们，我们现在只需要检查登录状态，使用这个特定的查询键。

我们自定义钩子的 `return` 语句由一个带有三个参数的 `useQuery` 钩子组成。首先，我们有至关重要的查询键，`imageList`。接下来，我们看到对获取函数的调用。最后但同样重要的是，我们有一个包含名为 `enabled` 的键的配置对象。这个键决定了何时调用给定的查询。在我们的例子中，当 `loginStatus` 查询的结果返回 `true` 值时，将调用查询。我们刚刚成功设置了 React Query 来获取图像。剩下要做的就是显示它们。让我们转到 `ListOfFavorited` 组件，我们将用以下自定义钩子替换上下文调用：

```js
// src/components/ListOfFavorited.js
import { useCustomImageQuery } from "../queries/
  useCustomImageQuery";
//…
export const ListOfFavorites = ({ navigation }) => {
  const { data: queriedImages } = useCustomImageQuery();
//…
  return (
//…
    <FlatList
    data={ queriedImages }
//…
```

如果一切按计划进行，你现在应该能够运行应用程序并看到由 React Query 从后端拉取的收藏图像列表。如果你遇到任何问题，请记住，我们创建的自定义钩子只是一个函数，可以像这样进行调试。你可以在组件中、在钩子中或在钩子调用的 `getImages` 函数中放置 `console.log`。

希望你能够顺利地设置好一切。在本节中，我们练习了使用 React Query 来获取和显示数据。我们利用了 ReactJS 的知识——因为我们创建了一个自定义钩子——但 React Query 钩子可以以多种方式设置。鉴于我们的应用程序有一个只能提供数据的模拟后端，这就是我们在 React Query 的实际使用中能走多远。不过，我亲爱的读者，我邀请你继续阅读，了解这个库还包含哪些其他优秀功能。

# 其他 React Query 功能

如上所述，我们无法在我们的示例应用中使用 React Query 在服务器上突变数据，因为我们的后端不够健壮。在实际应用中，你可能会使用一个既能接受`POST`请求也能接受`GET`请求的 API。在这些情况下，你将能够借助 React Query 来更改数据。为了做到这一点，我们得到了另一个专门的钩子：`useMutation`。以下是我们如果能够使用它来处理收藏图片时这个钩子的样子：

```js
  const imageListMutation = useMutation(newImage => {
    return fetch('/john_doe/likedImages ',
      {method: 'POST', body: newImage})
  });
```

前面的函数非常简单。它将一个`fetch`调用包裹在 React Query 实用工具中。这个实用工具为我们提供了一些东西，比如它有以下状态：`isIdle`、`isLoading`、`isError`和`isSuccess`。我们可以检查这些状态并根据情况更新视图。我们将在`ImageDetailsmodal`中使用这个突变：

```js
// src/surfaces/ImageDetailsmodal.js
//…
export const ImageDetailsmodal = ({ navigation }) => {
  const imageListMutation = useMutation(newImage => {
    return fetch('/john_doe/likedImages ',
      {method: 'POST', body: newImage})
  });
//…
  return (
//…
      <Pressable
          onPress={() => {
           imageListMutation.mutate({route.params.imageItem
             })
          }}
        >
        {mutation.isLoading ? (
            <Text>Loading…</Text>
              ) : (
                <Ionicons
                  //…
          /> )
          }
        </Pressable>
//…
```

让我重申：我们正在进行发送数据到服务器的干运行，因为我们的应用后端无法处理`POST`请求。

在前面的代码中，我们首先向`ImageDetailsModal`添加了一个 React Query 突变函数。我们将其传递给`Pressable`组件。然后，在`Pressable`组件内部，我们添加了一个三元运算符来检查突变是否处于加载状态。如果是的话，我们将显示一个`Text`组件，显示`isSucccess`和`isError`，你可能会更优雅地处理加载。

这听起来很棒，但按照我们上面实现突变的方式，我们仍然需要传统地重新获取数据，以便在`ListOfFavorites`组件中获取最新版本。除非我们使用 React Query 的全部力量来更新之前通过`useCustomImageQuery`钩子获取的数据的缓存版本！以下是我们在突变中需要更改的内容：

```js
const updateImges = () => {
   return fetch('/john_doe/likedImages ',
     {method: 'POST', body: newImage})
}
const imageListMutation = useMutation(updateImges, {
   onSuccess: data => {
    queryClient.setQueryData(['imageList'], data)
  }
})
```

在前面的代码片段中，我们首先提取了`fetch`函数以提高可读性。然后，我们将`onSuccess`逻辑添加到突变中，并告诉它使用`imageList`查询键更新标记的项目的新数据。多亏了这个策略，我们不必每次突变发生时都手动更新`imageList`数据。你可以在*进一步阅读*部分链接的 TanStack 文档中了解更多关于突变响应后更新的信息。

我们已经涵盖了 React Query 的两个最重要的方面：获取和突变数据。然而，在实际项目中还有很多更多功能可以利用。你可以检查获取状态，就像我们在示例突变中所做的那样。你也可以进行并行查询以同时获取数据。如果你想的话，你可以在获取完成之前设置初始数据来填充你的视图。你也可以在任何需要的时候暂停或禁用查询。对于大型数据集，有一种特殊的查询类型，即分页查询，它将数据批量处理成可消费的块。如果你的数据是无限的，React Query 提供了无限查询的实用工具。许多大型应用可能会利用页面加载时预取数据。

我鼓励您，亲爱的读者，阅读 React Query 文档，以便能够掌握它提供的所有可能的解决方案。我自己在使用 React Query 时也感到惊讶，因为这个库可以解决许多常见问题。

# React Native 的 React Query 实用工具

正如我们所知，与纯 ReactJS 相比，React Native 有其独特的特性。React Query 并没有将管理这些特性的任务留给开发者，而是提供了一些有趣的解决方案。例如，有一个`onlineManager`可以添加到 React Native 应用中，以便当应用在线时重新连接。如果我们希望在应用聚焦时刷新或重新获取数据，我们可以使用 React Query 的`focusManager`与 React Native 的`AppState`一起使用。在某些情况下，我们可能希望在应用中特定屏幕聚焦时重新获取数据，React Query 也为此用例提供了解决方案。如果您想详细了解这些实用工具及其使用方法，请访问 TanStack 文档[`tanstack.com/query/v4/docs/react-native`](https://tanstack.com/query/v4/docs/react-native)。

# 摘要

React Query 经过实战检验，适用于扩展应用程序，并且可以成为各种项目的绝佳解决方案。在本章中，我们在 Funbook 应用中安装了它，并将其添加到应用中。由于我们的项目规模较小，不需要对默认配置进行任何更改，所以我们没有进行任何特定的配置。然后，我们探讨了如何使用简单的数据获取机制来检查用户的登录状态。接下来，我们创建并使用了一个具有依赖关系的更复杂的数据获取钩子。我们展示了获取到的数据，然后我们对其他 React Query 实用工具进行了浏览。React Query 是我们穿越 React Native 应用状态管理库世界的最后一站。我希望您喜欢这次旅程！

我邀请您，亲爱的读者，与我一起进入最后一章，我们将总结我们在 React Native 应用状态管理主题上所学的所有内容。

# 进一步阅读

+   [`tanstack.com/`](https://tanstack.com/) – TanStack 主页。

+   [`tanstack.com/query/v4/docs/guides/updates-from-mutation-responses`](https://tanstack.com/query/v4/docs/guides/updates-from-mutation-responses) – TanStack Query，*来自* *突变响应* *的更新。*

# 第四部分 – 摘要

在本部分中，读者将概述本书涵盖的所有不同解决方案。

本部分包括以下章节：

+   *第十章*，*附录*
