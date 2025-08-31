

# 第六章：使用 React Query 执行数据突变

在构建应用程序时，你并不总是需要获取数据。有时，你可能想要创建、更新或删除数据。在这些操作中，你的服务器状态将需要改变。

React Query 允许你通过使用突变来更改你的服务器状态。要执行突变，你可以利用 React Query 的另一个自定义钩子，称为`useMutation`。

在本章中，你将介绍`useMutation`钩子，并了解 React Query 如何允许你创建、更新和删除你的服务器状态。类似于*第四章*，在这个过程中，你将了解你在突变中使用的所有默认值。你还将了解一些可以用来改进你的`useMutation`体验的选项。

一旦你熟悉了`useMutation`，你将了解如何利用它的一些选项来执行一些副作用模式，例如手动更新数据或强制查询在执行突变后更新。

在本章结束时，我们将把到目前为止所学的一切整合起来，并将其应用于做一些可能显著提高用户体验的事情：乐观更新。

在本章中，我们将涵盖以下主题：

+   `useMutation`是什么以及它是如何工作的？

+   突变后的副作用模式

+   执行乐观更新

# 技术要求

本章的所有代码示例都可以在 GitHub 上找到，地址为[`github.com/PacktPublishing/State-management-with-React-Query/tree/feat/chapter_6`](https://github.com/PacktPublishing/State-management-with-React-Query/tree/feat/chapter_6)。

# `useMutation`是什么以及它是如何工作的？

到现在为止，你必须已经意识到突变允许你对服务器状态进行更新。这些更新可以是创建数据、删除数据或编辑数据等操作。

为了让你能够在服务器数据上执行突变，React Query 创建了一个名为`useMutation`的钩子。

现在，与默认情况下会自动运行查询的`useQuery`不同，`useMutation`只有在调用从钩子实例化返回的一个函数（称为`mutate`）时才会运行你的突变。

要使用`useMutation`钩子，你必须像这样导入它：

```js
import { useMutation } from '@tanstack/react-query';
```

一旦导入，你就可以用它来定义你的突变。以下是`useMutation`的语法：

```js
const mutation = useMutation({
    mutationFn: <InsertMutationFunction>
})
```

如您从前面的代码片段中看到的，`useMutation`钩子只需要一个必需参数才能工作，即突变函数。

## 突变函数是什么？

突变函数是一个返回负责执行异步任务的 promise 的函数。在这种情况下，这个异步任务将是我们的突变。

我们之前看到的与查询函数相同的原理也适用于 mutation 函数。这意味着，正如我们看到的查询函数一样，由于这个函数只需要返回一个 promise，它再次允许我们使用我们选择的任何异步客户端。这意味着 REST 和 GraphQL 仍然受支持，所以如果你愿意，你可以同时使用这两个选项。

现在我们来看一个使用 GraphQL 和 REST 的 mutation 函数的示例。这些 mutation 函数将被用来在我们的服务器状态中创建新用户：

使用 GraphQL 的 mutation

```js
import { useMutation } from "@tanstack/react-query";
import { gql, GraphQLClient } from "graphql-request";
const customMutation = gql`
mutation AddUser($user: String!, $age: Int!) {
  insert_user(object: { user: $user, age: $age }) {
    user
    age
  }
}
`
const createUserGQL = async (user) => {
  const endpoint = <add_endpoint_here>;
  const client = new GraphQLClient(endpoint)
  return client.request(customMutation, user);
  return data;
};
 ...
const mutation = useMutation({
    mutationFn: createUserGQL
  });
```

前面的代码片段展示了使用 React Query 创建 GraphQL mutation 的示例。以下是我们的操作：

1.  我们首先创建我们的 GraphQL mutation，并将其分配给我们的`customQuery`变量。

1.  然后我们创建`createUserGQL`函数，这个函数将成为我们的 mutation 函数。这个函数也将接收作为参数的`user`数据，这些数据将被我们的 mutation 用于在服务器上创建数据。

1.  在我们的`useMutation`钩子中，我们将`createUserGQL`函数作为 mutation 函数传递给钩子。

让我们看看如何使用 REST 来完成这个操作：

使用 REST 的 mutation

```js
import axios from "axios";
import {useMutation} from "@tanstack/react-query";
const createUser = async (user) => {
  return axios.post
    (`https://danieljcafonso.builtwithdark.com/name-api`,
      user);
};
 …
const mutation = useMutation({
    mutationFn: createUser
  });
```

在前面的代码片段中，我们可以看到一个使用 React Query 创建 REST 的 mutation 的示例。以下是我们的操作：

1.  我们首先创建`createUser`函数，这个函数将成为我们的 mutation 函数。这个函数将接收作为参数的`user`数据，这些数据用于我们的 mutation 在服务器上创建数据。在这里，我们知道我们将使用`POST`方法在服务器上创建数据。

1.  在我们的`useMutation`钩子中，我们将`createUser`函数作为 mutation 函数传递给钩子。

在前面的例子中，我们使用了`axios`，但如果你更喜欢使用`fetch`而不是`axios`，你只需要在`createUser`函数内部将`axios`替换为`fetch`，并对`fetch`进行必要的修改以使其工作。以下是一个使用`fetch`的示例：

```js
const createUserFetch = async (user) => {
  return fetch
    (`https://danieljcafonso.builtwithdark.com/name-api`, {
    method: "POST",
    body: JSON.stringify(user),
    headers: {
      "Content-type": "application/json; charset=UTF-8",
    },
  });
};
const mutation = useMutation({
    mutationFn: createUserFetch
});
```

在前面的代码片段中，我们可以看到之前展示的`createUser`函数的示例，但这次我们使用了`fetch`而不是`axios`。

现在我们已经熟悉了 mutation 函数，我们需要了解`useMutation`钩子如何利用这个函数来允许我们执行 mutations。在下一节中，我们将学习`mutate`函数如何使我们能够这样做，以及`useMutation`返回的其他内容。

## useMutation 返回什么？

和`useQuery`一样，当使用`useMutation`钩子时，它返回几个值。

如本章前面所述，要执行 mutations，我们需要利用`mutate`。现在，`mutate`不是执行 mutations 的唯一方式，也不是`useMutation`返回的唯一内容。

在本节中，我们将回顾`useMutation`钩子的以下返回值：

+   `mutate`

+   `mutateAsync`

+   `数据`

+   `错误`

+   `重置`

+   `状态`

+   `isPaused`

### `mutate`

在使用你的 `useMutation` 钩子创建你的变更后，你需要一种方法来触发它。`mutate` 是你几乎每次都需要用来做到这一点的函数。

这就是如何使用 `mutate`：

```js
const { mutate } = useMutation({
    mutationFn: createUser
  });
mutate({ name: "username", age: 25 })
```

在这个代码片段中，我们做以下操作：

1.  我们从 `useMutation` 钩子中解构出我们的 `mutate` 函数。

1.  我们使用 `mutate` 函数并传递变量，这些变量是我们期望 `mutate` 函数接收以执行我们的变更。

就这样；这就是你如何使用 React Query 来执行变更。你创建你的变更函数，将其传递给你的 `useMutation` 钩子，从其中解构出 `mutate`，并使用所需的参数调用它以执行你的变更。

现在，前面的代码片段旨在展示如何通过使用 `mutate` 来触发变更，但这不是一个非常实用的例子。为了帮助你构建如何使用 `mutate` 来执行变更的心理模型，你可以参考以下代码片段：

```js
import axios from "axios";
import { useMutation } from "@tanstack/react-query";
import { useState } from "react";
const createUser = async (user) => {
  return axios.post
    (`https://danieljcafonso.builtwithdark.com/name-api`,
      user);
};
const SimpleMutation = () => {
  const [name, setName] = useState("");
  const { mutate } = useMutation({
    mutationFn: createUser,
  });
  const submitForm = (e) => {
    e.preventDefault()
    mutate({ name })
  }
  return (
    <div>
      <form>
        <input
          name="name"
          type={"text"}
          onChange={(e) => setName(e.target.value)}
          value={name}
        />
        <button onClick={submitForm}>Add</button>
      </form>
    </div>
  );
};
```

在前面的代码片段中，我们可以看到一个使用受控组件的简单表单示例。这就是前面代码片段中发生的事情：

1.  我们创建一个 `createUser` 变更函数，它将接收一个包含一些数据的 `user` 对象。

在这个函数内部，我们返回 `axios` 客户端的 `post` 方法的调用，这将返回 `useMutation` 期望接收的用于变更函数的承诺。

1.  在我们的 `SimpleMutation` 组件内部，我们做以下操作：

    1.  我们创建一个状态变量来控制输入的状态。

    1.  我们使用 `createUser` 函数作为变更函数来创建我们的变更，并从其中解构出 `mutate`。

    1.  我们创建一个 `submitForm` 函数。这个函数将接收表单的事件并阻止其传播，这样你的页面就不会刷新。在处理事件后，它通过调用 `mutate` 触发变更，并将 `name` 状态变量作为 `user` 对象的一部分传递。

1.  在我们的表单内部，我们创建我们的输入来处理 `name` 并让 React 控制其状态。

1.  我们创建一个带有 `onClick` 事件的按钮来触发我们的 `submitForm` 函数。

正如你应该从前面的解释和代码中理解的那样，每当我们点击使用当前输入值的 `POST` 请求到我们的 URL 时。

在继续本章的过程中，你还会看到 `mutate` 也可以接收一些选项来执行副作用，如果你需要的话。但让我们把这些细节留到以后再说。

虽然 `mutate` 是在 React Query 中执行变更的基础，但如果你愿意，你也可以使用另一个函数：`mutateAsync`。

### mutateAsync

在大多数情况下，你会使用 `mutate`，但有时你可能想访问包含你变更结果的承诺。在这些情况下，你可以使用 `mutateAsync`。

在使用 `mutateAsync` 时，需要注意的一点是你需要自己处理承诺。这意味着在错误场景中，你需要捕获错误。

这就是如何使用 `mutateAsync` 函数：

```js
const { mutateAsync } = useMutation({
  mutationFn: createUser,
});
try {
  const user = await mutateAsync({ name: "username", age:
    25 });
} catch (error) {
  console.error(error);
}
```

在前面的代码片段中，我们从`useMutation`钩子中解构了`mutateAsync`函数：

+   我们需要处理潜在的错误场景，因此我们将`mutateAsync`调用用`try-catch`语句包裹起来。由于这是一个异步函数，我们必须等待数据返回。

+   如果出现错误，我们会捕获它并在我们的控制台中显示错误。

前面的代码片段显示了如何使用`mutateAsync`触发突变；正如我们在`mutate`中所示，这似乎不是一个非常实用的例子。为了帮助你创建如何使用`mutateAsync`执行突变的心理模型，你可以看到以下代码片段：

```js
const ConcurrentMutations = () => {
  const [name, setName] = useState("");
  const { mutateAsync: mutateAsyncOne } = useMutation({
    mutationFn: createUser,
  });
  const { mutateAsync: mutateAsyncTwo } = useMutation({
    mutationFn: registerUser,
  });
  const submitForm = async (e) => {
    e.preventDefault()
    const mutationOne = mutateAsyncOne({ name })
    const mutationTwo = mutateAsyncTwo({ name })
     try {
      const data = await Promise.all([mutationOne,
        mutationTwo]);
      // do something with data
    } catch (error) {
      console.error(error);
    }
  }
  return (
    <div>
      <form>
        <input
          name="name"
          type={"text"}
          onChange={(e) => setName(e.target.value)}
          value={name}
        />
        <button onClick={submitForm}>Add</button>
      </form>
    </div>
  );
};
```

在前面的代码片段中，我们可以看到一个使用受控组件的简单表单示例，其中我们利用`mutateAsync`执行并发突变。这就是代码中发生的事情：

1.  我们创建一个状态变量来控制输入的状态。

1.  我们使用`createUser`函数作为突变函数来创建我们的第一个突变，并从其中解构`mutateAsync`为`mutateAsyncOne`。

1.  我们使用`registerUser`函数作为突变函数来创建我们的第二个突变，并从其中解构`mutateAsync`为`mutateAsyncTwo`。

1.  我们创建一个`submitForm`函数：

    1.  这个函数将接收来自表单的事件并阻止其传播，这样你的页面就不会刷新。

    1.  我们将调用`mutationAsyncOne`并传递`name`作为参数返回的承诺分配给我们的`mutationOne`变量。

    1.  我们将调用`mutationAsyncTwo`并传递`name`作为参数返回的承诺分配给我们的`mutationTwo`变量。

    1.  我们利用`Promise.all`方法并将其传递给我们的`mutationOne`和`mutationTwo`承诺，以便它们可以并发执行。

1.  在我们的表单内部，我们创建输入来处理我们的名称，并让 React 控制其状态。

1.  我们创建一个带有`onClick`事件的按钮来触发我们的`submitForm`函数。

现在，你已经熟悉了如何执行突变，让我们回顾一下受突变成功影响的变量，`data`。

### data

这个变量是突变函数返回的最后成功解析的`data`。

这里是如何使用`data`变量的示例：

```js
const SimpleMutation = () => {
  const { mutate, data } = useMutation({
    mutationFn: createUser,
  });
  return (
    <div>
        {data && <p>{data.data.user}</p>}
      ...
    </div>
  );
}
```

在这个代码片段中，我们做了以下操作：

1.  我们从`useMutation`钩子中解构了我们的`data`变量。

1.  在我们的组件返回时，我们检查是否已经从我们的突变中获取了`data`。如果是，我们就渲染它。

当钩子最初渲染时，这个`data`将是未定义的。一旦突变触发并完成执行，并且突变函数返回的承诺成功解析了我们的数据，我们就可以访问`data`。如果由于某种原因突变函数的承诺被拒绝，我们可以使用下一个变量，即`error`变量。

### error

`error`变量让你可以访问突变函数返回的失败后的`error`对象。

这里是如何使用`error`变量的示例：

```js
const SimpleMutation = () => {
  const { mutate, error } = useMutation({
    mutationFn: createUser,
  });
  return (
    <div>
        {error && <p>{error.message}</p>}
  ...
    </div>
  );
};
```

在前面的代码片段中，我们做了以下操作：

1.  我们从`useQuery`钩子中解构我们的`error`变量。

1.  在我们的组件返回时，我们检查是否有任何错误。如果有，我们将渲染错误信息。

当钩子最初渲染时，`error`值将是 null。如果在突变之后，由于某种原因突变函数拒绝并抛出错误，那么这个错误将被分配给我们的`error`变量。在这里重要的是要提到，这仅适用于你使用`mutate`的情况。如果你使用`mutateAsync`，你必须自己捕获错误并处理它。

当使用`error`变量时，有时为了用户体验，你可能想要清除你的错误。在这些情况下，`reset`函数将成为你的最佳选择。

### reset

`reset`函数允许你将`error`和`data`重置到它们的初始状态。

这个函数在你需要在运行突变后清除当前数据或错误值时很有用。

这是你可以使用`reset`函数的方法：

```js
const SimpleMutation = () => {
  const { mutate, data, error, reset } = useMutation({
    mutationFn: createUser,
  });
  return (
    <div>
        {error && <p>{error.message}</p>}
        {data && <p>{data.data.user}</p>}
        <button onClick={() => reset()}>Clear errors and
          data</button>
        ...
   </div>
  );
};
```

在这个片段中，我们做了以下操作：

1.  我们从`useMutation`钩子中解构`data`、`error`变量和`reset`函数。

1.  在我们的组件返回时，我们检查是否已经从我们的突变中获得了数据或错误。当且仅当我们这样做时，我们将渲染它们。

1.  我们还渲染了一个带有`onClick`事件的按钮。当点击这个按钮时，它将触发我们的`reset`函数来清除我们的`data`和`error`值。

现在，为了使用`error`和`data`变量，我们只需在代码中检查它们是否已定义，以便我们可以渲染它们。为了使这更容易，并且再次帮助你为你的应用程序制作更好的用户体验，你可以求助于使用`status`变量。

### status

就像查询一样，当执行突变时，突变可以经过几个状态。这些状态帮助你向用户提供更多反馈。为了知道你的突变当前的状态，我们创建了`status`变量。

`status`变量可以具有以下状态：

+   `idle`：这是你的突变在执行之前的初始状态。

+   `loading`：这表示你的突变是否正在执行。

+   `error`：这表示在执行最后一个突变时出现了错误。每当这是状态时，`error`属性将接收从突变函数返回的错误。

+   `success`：你的最后一个突变是成功的，并且它已经返回了数据。每当这是状态时，`data`属性将接收从突变函数返回的成功数据。

这是你可以使用`status`变量的方法：

```js
 const SimpleMutation = () => {
  const [name, setName] = useState("");
  const { mutate, status, error, data } = useMutation({
    mutationFn: createUser,
  });
  const submitForm = (e) => {
    e.preventDefault()
    mutate({ name })
  }
  return (
    <div>
      {status === "idle" && <p> Mutation hasn't run </p>}
      {status === "error" && <p> There was an error:
        {error.message} </p>}
      {status === "success" && <p> Mutation was successful:
        {data.name} </p>}
      <form>
        <input
          name="name"
          type={"text"}
          onChange={(e) => setName(e.target.value)}
          value={name}
        />
        <button disabled={status === "loading"}
          onClick={submitForm}>Add</button>
      </form>
    </div>
  );
};
```

在前面的片段中，我们正在利用`status`变量来为我们的用户提供更好的用户体验。以下是我们在做些什么：

1.  我们创建了一个状态变量来处理我们的受控表单。

1.  我们创建我们的突变并从`useMutation`钩子中解构`status`。

1.  我们创建了一个`submitForm`函数来处理我们的突变提交。

1.  我们利用我们的`status`变量在我们的组件返回中执行以下操作：

    1.  如果`status`是`idle`，我们将渲染一条消息，让用户知道我们的突变尚未运行。

    1.  如果`status`等于`error`，我们必须解构我们的`error`变量并显示错误消息。

    1.  如果`status`等于`success`，我们必须解构我们的`data`变量并将其显示给我们的用户。

    1.  如果`status`等于`loading`，这意味着我们正在执行一个突变，因此我们使用这个选项来确保我们禁用我们的**添加**按钮，避免在突变运行期间用户再次点击它。

现在，您知道了如何使用`status`变量。为了方便，React Query 还引入了一些布尔变体，以帮助识别每个状态。它们如下所示：

+   `isIdle`：您的`status`变量处于空闲状态

+   `isLoading`：您的`status`变量处于加载状态

+   `isError`：您的`status`变量处于错误状态

+   `isSuccess`：您的`status`变量处于成功状态

让我们现在重写我们之前的代码片段，利用我们的`status`布尔变体：

```js
 const SimpleMutation = () => {
  const [name, setName] = useState("");
  const { mutate, isIdle, isError, isSuccess, isLoading,
    error, data } = useMutation({
    mutationFn: createUser,
  });
  const submitForm = (e) => {
    e.preventDefault()
    mutate({ name })
  }
  return (
    <div>
      {isIdle && <p> Mutation hasn't run </p>}
      {isError && <p> There was an error: {error.message}
        </p>}
      {isSuccess && <p> Mutation was successful:
        {data.name} </p>}
      <form>
        <input
          name="name"
          type={"text"}
          onChange={€ => setName(e.target.value)}
          value={name}
        />
        <button disabled={isLoading} onClick={submitForm}>
          Add</button>
      </form>
    </div>
  );
};
```

如您所见，代码是相似的。我们只需在解构部分将我们的`status`变量替换为`isLoading`、`isError`、`isSuccess`和`isIdle`，然后在相应的状态检查中使用这些变量。

与查询不同，突变没有`fetchStatus`变量。这并不意味着您的突变不能因突然断开互联网连接而受到影响。为了给用户提供更多反馈，我们创建了`isPaused`变量。

### isPaused

如您应从*第四章*中记住，React Query 引入了一个名为`networkMode`的新属性。当在线模式下使用时，您可以在`useMutation`钩子中访问一个新变量，称为`isPaused`。

这个布尔变量标识您的突变是否因断开连接而当前暂停。

让我们看看如何使用`isPaused`变量：

```js
const SimpleMutation = () => {
  const [name, setName] = useState("");
  const { mutate, isPaused } = useMutation({
    mutationFn: createUser,
  });
  const submitForm = € => {
    e.preventDefault()
    mutate({ name })
  }
  return (
    <div>
      {isPaused && <p> Waiting for network to come back </p>}
      <form>
        <input
          na"e="n"me"
          typ"={"t"xt"}
          onChang€(e) => setName(e.target.value)}
          value={name}
        />
        <button disabled={isPaused} onClick={submitForm}>
          Add</button>
      </form>
    </div>
  );
};
```

在前面的代码片段中，我们利用`isPaused`变量来在我们的应用程序中创建更好的用户体验：

1.  我们从`useMutation`钩子中解构我们的`isPaused`变量。

1.  在我们的组件返回中，我们检查`isPaused`是否为`true`。如果是，我们渲染一条消息让我们的用户知道。我们还将其分配给禁用我们的**添加**按钮，以避免用户意外触发另一个突变。

现在我们知道了`useMutation`钩子返回的一些值，让我们看看我们如何使用一些选项来自定义这个钩子。

## 常用突变选项解释

与`useQuery`钩子类似，我们可以向`useMutation`钩子传递比其突变函数更多的选项。这些选项也将帮助我们创建更好的开发者和用户体验。

在本节中，我们将看到一些更常见且非常重要的选项。

这里是我们将要查看的选项：

+   `cacheTime`

+   `mutationKey`

+   `retry`

+   `retryDelay`

+   `onMutate`

+   `onSuccess`

+   `onError`

+   `onSettled`

### cacheTime

`cacheTime`选项是您的缓存中不活跃数据在内存中保持的时间（以毫秒为单位）。一旦这个时间过去，数据将被垃圾回收。请注意，这与查询的方式不同。如果您执行了一个突变，返回的数据将被缓存，但如果在突变挂起期间再次执行相同的突变，`useMutation`将不会返回之前的突变数据。在突变中，此选项主要用于防止之前的突变数据无限期地保留在`MutationCache`中。

下面是如何使用`cacheTime`选项：

```js
useMutation({
  cacheTime: 60000,
});
```

在这个代码片段中，我们定义了在突变不活跃一分钟之后，数据将被垃圾回收。

### mutationKey

有时您会想通过利用您的`queryClient`的`setMutationDefaults`来为所有突变设置一些默认值。

`mutationKey`选项允许 React Query 知道是否需要将之前配置的默认值应用于此突变。

下面是如何使用`mutationKey`选项：

```js
useMutation({
  mutationKey: ["myUserMutation"],
});
```

在前面的代码片段中，我们使用`["myUserMutation"]`作为突变键创建了一个突变。如果为任何具有`["myUserMutation"]`作为突变键的突变配置了默认值，它们现在将被应用。

### 重试

`retry`选项是一个值，表示当突变失败时，您的突变是否会重试。当为`true`时，它会重试直到成功。当为`false`时，它不会重试。

此属性也可以是一个数字。当它是一个数字时，突变将重试指定次数。

默认情况下，React Query 不会在出错时重试突变。

下面是如何使用`retry`选项：

```js
useMutation({
  retry: 2,
});
```

在这个代码片段中，我们将`retry`选项设置为`2`。这意味着当执行突变失败时，此钩子将重试执行突变两次。

### retryDelay

`retryDelay`选项是在下一次重试尝试之前应用的延迟（以毫秒为单位）。

默认情况下，React Query 使用指数退避延迟算法来定义重试之间的时间间隔。

下面是如何使用`retryDelay`选项：

```js
useMutation({
  retryDelay: (attempt) => attempt * 2000,
});
```

在代码片段中，我们定义了一个线性退避函数作为我们的`retryDelay`选项。每次重试时，此函数都会接收尝试次数并将其乘以 2,000。这意味着每次重试之间的时间将增加两秒。

### onMutate

`onMutate`选项是一个在您的突变函数被触发之前会调用的函数。此函数还会接收您的突变函数将接收的变量。

您可以从这个函数返回值，这些值将被传递到您的`onError`和`onSettled`回调函数中：

```js
useMutation({
  onMutate: (variables) => showNotification("Updating the
    following data:", variables),
});
```

在这个代码片段中，我们将一个箭头函数传递到我们的`onMutate`选项中。当我们的突变被触发时，分配给`onMutate`选项的函数将使用这些变量调用，然后我们使用这些变量向用户显示有关挂起突变的提示。

### onSuccess

`onSuccess`选项是一个函数，当你的突变成功时将被触发。

如果此函数返回一个承诺，它将被等待并解决。这意味着你的突变状态将处于加载状态，直到承诺解决。

这是如何使用`onSuccess`选项的方法：

```js
useMutation({
  onSuccess: (data) => console.log("mutation was
    successful", data),
});
```

在代码片段中，我们向`onSuccess`选项传递一个箭头函数。当我们的突变成功执行时，分配给`onSuccess`选项的此函数将使用我们的数据被调用。然后我们使用这些数据在我们的控制台中记录一条消息。

### onError

`onError`选项是一个函数，当你的突变失败时将被触发。

如果此函数返回一个承诺，它将被等待并解决。这意味着你的突变状态将处于加载状态，直到承诺解决。

这是如何使用`onError`选项的方法：

```js
useMutation({
  onError: (error) => console.log("mutation was
    unsuccessful", error.message),
});
```

在代码片段中，我们向`onError`选项传递一个箭头函数。当突变失败时，分配给`onError`选项的此函数将使用抛出的错误被调用。然后我们在我们的控制台中记录错误。

### onSettled

`onSettled`选项是一个函数，当你的突变成功或失败时将被触发。

如果此函数返回一个承诺，它将被等待并解决。这意味着你的突变状态将处于加载状态，直到承诺解决。

这是如何使用`onSettled`选项的方法：

```js
useMutation({
  onSettled : (data, error) => console.log("mutation has
    settled"),
});
```

在代码片段中，我们向`onSettled`选项传递一个箭头函数。当突变成功或失败时，分配给`onSettled`选项的此函数将使用抛出的错误或解决的数据被调用。然后我们在我们的控制台中记录一条消息。

到目前为止，你应该已经熟悉了`useMutation`钩子的用法，并且应该能够开始使用它来创建、更新或删除你的服务器状态数据。现在，让我们看看我们如何利用这个钩子和一些选项来执行一些常见的副作用模式。

# 在突变之后执行副作用模式

当你阅读这个标题时，你可能想知道你是否以前见过如何在突变之后执行副作用。答案是肯定的，你已经做到了。要执行突变后的副作用，你可以利用这些选项中的任何一个：

+   `onMutate`

+   `onSuccess`

+   `onError`

+   `onSettled`

现在，你可能还没有看到如何利用这些副作用来做一些可能改善用户体验的惊人事情，比如执行多个副作用、重新获取查询，甚至在突变后更新查询数据。

在本节中，我们将回顾一些我们如何利用`useMutation`钩子的回调函数以及更多内容来执行之前提到的副作用。

## 如何执行额外的副作用

在开发过程中，可能会出现一个场景，如果你能够执行两个 `onSuccess` 回调将会很有用。现在，你当然可以在你的 `useMutation` 钩子回调中添加你想要的任何逻辑，但如果你想要拆分逻辑或者只在一个单独的变异上执行这个特定的逻辑呢？这确实很有用，因为你可以分离关注点和逻辑。嗯，你当然可以做到！

`mutate` 函数允许你创建自己的回调函数，这些函数将在你的 `useMutation` 回调之后执行。

你只需要意识到你的 `useMutation` 回调先执行，然后是你的 `mutate` 函数回调。这一点很重要，因为有时候如果你在 `useMutation` 回调中做了某些导致钩子卸载的操作，你的 `mutate` 函数回调可能不会执行。

下面是一个如何使用 `mutate` 回调函数的例子：

```js
 const { mutate } = useMutation({
    mutationFn: createUser,
    onSuccess: (data) => {
      showToast(`${data.data.name} was created
        successfuly`)
    }
  });
  const submitForm = (e) => {
    e.preventDefault()
    mutate({ name }, {
      onSuccess: (data) => {
        const userId = data.data.userID
        goToRoute(`/user/${userId}`)
      }
    })
  }
  ...
```

在前面的代码片段中，我们利用 `mutate` 回调函数来执行一些额外的副作用。以下是我们的操作：

1.  我们使用 `useMutation` 创建我们的变异。

1.  在这个变异内部，我们利用 `onSuccess` 回调，它将接收解析后的数据并向用户显示一个提示，告诉他们数据已被创建。

1.  我们随后创建一个 `submitForm` 函数，该函数将在我们的代码中的某个 `onSubmit` 事件中被提供。

1.  当被触发时，这个函数将阻止接收的事件传播。

1.  这个函数将通过调用 `mutate` 来触发我们的变异。在这个 `mutate` 中，我们利用它的 `onSuccess` 回调来触发路由更改。

现在我们知道了如何使用 `mutate` 回调函数来执行一些额外的副作用，让我们看看如何在执行变异后重新触发查询。

## 如何在变异后重新触发查询的重新获取

当执行将改变你当前向用户显示的查询数据的变异时，建议你重新获取该查询。这是因为，在这个时候，你知道数据已经改变，但如果你的查询仍然被标记为内部新鲜，React Query 不会重新获取它；因此，你必须自己来做。

在阅读了前面的两个章节后，当你阅读这个标题时，你肯定会有所思考，那就是查询无效化！

下面是如何利用 `onSuccess` 回调来重新触发查询的重新获取：

```js
  const queryClient = useQueryClient()
  const { data } = useQuery({
    queryKey: ["allUsers"],
    queryFn: fetchAllData,
  });
  const { mutate } = useMutation({
    mutationFn: createUser,
    onSuccess: () => {
      queryClient.invalidateQueries({
        queryKey: ["allUsers"],
      })
    }
  });
```

在前面的代码片段中，我们利用 `onSuccess` 回调在变异成功后重新触发查询。以下是我们的操作：

1.  我们可以访问 `queryClient`。

1.  我们使用 `["allUsers"]` 作为查询键创建我们的查询。

1.  我们创建我们的变异。在这个变异的 `onSuccess` 回调中，我们利用我们的 `queryClient` 的 `invalidateQueries` 方法来触发带有 `["allUsers"]` 作为查询键的查询的重新获取。

如本节开头所述，这是一个推荐的做法，每次你正在变异用户在页面上看到的数据时，都应该这样做。现在，你可能正在想：如果我们的变异是成功的，它可能已经返回了新数据，所以我们不能只是手动更新我们的查询数据并避免额外的请求吗？

## 如何在变异后更新我们的查询数据

你当然可以手动更新你的查询数据。你所需要的是访问`queryClient`以及你想要更新的查询的查询键。

虽然这可能在用户端节省一些带宽，但它并不能保证你最终显示给用户的数据是准确的。如果其他人使用相同的应用程序更改了你的数据怎么办？

现在，如果有保证没有其他人能够更新这个服务器状态，那么请随意尝试。但请确保你的查询在某个地方重新获取，以确保所有数据都是最新的。

这是如何在成功变异后更新你的查询数据的方法：

```js
 const queryClient = useQueryClient()
  const { data } = useQuery({
    queryKey: ["allUsers"],
    queryFn: fetchAllData,
  });
  const { mutate } = useMutation({
    mutationFn: createUser,
    onSuccess: (data) => {
      const user = data.data
      queryClient.setQueryData(["allUsers"], (prevData) =>
        [user, ...prevData]);
    }
  });
```

在之前的代码片段中，我们利用`onSuccess`回调来更新我们的查询数据，并避免重新获取它。以下是我们的操作：

1.  我们获取对`queryClient`的访问权限。

1.  我们使用`["allUsers"]`作为查询键创建我们的查询。

1.  我们创建我们的变异。在这个变异的`onSuccess`回调中，我们利用`queryClient`的`setQueryData`函数来手动更新带有`["allUsers"]`作为查询键的查询数据。

1.  在这次更新中，我们创建了一个新数组，该数组结合了我们创建的数据和之前的数据，以创建新的查询数据。

如你所见，你可以应用一些模式来改善在执行变异后的用户体验。现在，当经常提到变异时，每次都会出现一个话题，这个话题将结束本章：乐观更新！

# 执行乐观更新

正如我们在*第二章*中看到的，乐观更新是在一个正在进行的变异期间使用的一种模式，我们更新我们的 UI 以显示变异完成后将如何显示，尽管我们的变异尚未被确认完成。

好吧，React Query 允许你执行乐观更新，并且这使得它变得极其简单。你所需要做的就是使用我们在上一节中看到的回调函数。

这是如何使用`useMutation`钩子执行乐观更新的方法：

```js
import axios from "axios";
import { useQuery, useMutation, useQueryClient } from
  "@tanstack/react-query";
import { useState } from "react";
const fetchAllData = async () => {
  const { data } = await axios.get(
    `https://danieljcafonso.builtwithdark.com/name-api`
  );
  return data;
};
const createUser = async (user) => {
  return axios.post
    (`https://danieljcafonso.builtwithdark.com/name-api`,
      user);
};
const Mutation = () => {
  const queryClient = useQueryClient();
  const [name, setName] = useState("");
  const [age, setAge] = useState(0);
  const { data } = useQuery({
    queryKey: ["allUsers"],
    queryFn: fetchAllData,
  });
  const mutation = useMutation({
    mutationFn: createUser,
    onMutate: async (user) => {
      await queryClient.cancelQueries({
        queryKey: ["allUsers"],
      });
      const previousUsers = queryClient.getQueryData({
        queryKey: ["allUsers"],
      });
      queryClient.setQueryData(["allUsers"], (prevData) =>
        [user, ...prevData]);
      return { previousUsers };
    },
    onError: (error, user, context) => {
      showToast("Something went wrong...")
      queryClient.setQueryData(["allUsers"], context.
        previousUsers);
    },
    onSettled: () =>
      queryClient.invalidateQueries({
        queryKey: ["allUsers"],
      }),
  });
  return (
    <div>
     {data?.map((user) => (
        <div key={user.userID}>
          Name: {user.name} Age: {user.age}
        </div>
      ))}
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
          type="button"
          onClick={(e) => {
            e.preventDefault()
            mutation.mutate({ name, age })
          }}
        >
          Add
        </button>
      </form>
    </div>
  );
};
```

在前面的代码片段中，我们将我们对变异的知识应用到实践中，以创建更好的用户体验。以下是我们的操作：

1.  我们为我们的代码定义所需的导入。

1.  创建`fetchAllData`查询函数。这个函数将触发一个`GET`请求到我们的端点以获取用户数据。

1.  创建`createUser`变异函数。这个函数将接收用户并执行一个`POST`请求到我们的端点以创建它。

1.  在我们的`Mutation`组件内部，我们执行以下操作：

    1.  我们获取对`queryClient`的访问权限。

    1.  创建姓名和年龄输入的状态变量及其相应的设置器。

    1.  使用`["allUsers"]`作为查询键和`fetchAllData`作为查询函数创建我们的查询。

    1.  使用`createUser`作为突变函数创建我们的突变。在这个突变内部，我们定义了一些回调：

        1.  在`onMutate`回调中，我们执行乐观更新：

            +   我们确保取消任何针对我们的查询（以`["allUsers"]`作为查询键）的正在进行中的查询。为此，我们使用我们的`queryClient` `cancelQueries`方法。

            +   我们将之前缓存的数据保存在`["allUsers"]`查询键下，以防需要回滚。为此，我们利用我们的`queryClient` `getQueryData`函数。

            +   我们通过合并我们的新数据与我们的旧数据，并更新`["allUsers"]`查询键下的缓存数据来执行乐观更新。为此，我们利用我们的`queryClient` `setQueryData`函数。

            +   如果需要回滚，我们返回我们的`previousUsers`数据。

        1.  在`onError`回调中，如果发生错误，我们需要回滚我们的数据：

            +   作为一种良好的实践，我们让我们的用户知道我们的突变操作出现了问题。在这种情况下，我们显示一个吐司通知。

            +   为了进行回滚，我们访问我们的上下文参数，并利用`onMutate`回调返回的`previousUsers`数据。然后我们使用这个变量来覆盖`["allUsers"]`查询键下的缓存数据。为此，我们使用我们的`queryClient` `setQueryData`函数。

        1.  在`onSettled`回调中，当我们的突变完成时，我们需要重新获取我们的数据：

            +   为了重新获取我们的数据，我们利用我们的`queryClient` `invalidateQueries`并使用`["allUsers"]`作为查询键来使查询无效。

        1.  在我们的组件返回中，我们创建一个`div`元素，如下所示：

    +   我们使用查询的`data`变量来显示用户的资料。

    +   我们使用我们的姓名和年龄输入创建受控表单。

    +   我们还创建了一个按钮，当按下时，会触发它的`onClick`事件，从而触发我们的带有姓名和年龄值的突变。

看过你如何构建乐观更新后，以下是我们的创建的乐观更新的流程：

1.  我们的组件渲染，我们的查询获取我们的数据并将其缓存。

1.  当我们点击**添加**按钮时，查询返回的数据会自动更新，包括新用户，并且立即在 UI 上反映这一变化。

1.  如果发生错误，我们将回滚到之前的数据。

1.  当我们的突变完成时，我们重新获取我们刚刚执行乐观更新的查询的数据，以确保我们的查询已更新。

拥有这些知识，你现在拥有了所有你需要用你的新盟友`useMutation`将突变游戏提升到下一个级别的知识！

# 摘要

在本章中，我们学习了 React Query 如何通过使用`useMutation`钩子来执行突变。到目前为止，你应该能够创建、删除或更新你的服务器状态。为了进行这些更改，你求助于突变函数，这个函数就像你的查询函数一样，支持任何客户端，并允许你使用 GraphQL 或 REST，只要它返回一个承诺即可。

你了解了一些`useMutation`钩子返回的内容，例如`mutate`和`mutateAsync`函数。与`useQuery`类似，`useMutation`也返回突变`data`和`error`变量，并为你提供一些你可以用来构建更好用户体验的状态。为了你的方便，`useMutation`还返回一个`reset`函数来清除你的状态，以及一个`isPaused`变量，以防你的突变进入暂停状态。

为了让你能够自定义开发者体验，你了解了一些常用的选项，这些选项允许你自定义`useMutation`钩子的体验。然后我们利用这四个选项中的四个来教你如何在突变运行后执行一些副作用。

最后，你使用了一些你学到的知识来执行乐观更新，并为你的应用程序用户提供更好的体验。

在*第七章* *使用 Next.js 或 Remix 进行服务器端渲染*中，我们将了解我们如何在即使我们使用服务器端框架的情况下利用 React Query。你将学习你如何在服务器上获取数据，并在客户端配置 React Query 以使其工作并构建更好的体验。
