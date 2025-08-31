

# 第一章：状态是什么以及我们如何管理它？

**状态**是一个可变的数据源，可以用于在 **React** 应用程序中存储数据，并且可以随时间变化，并可用于确定你的组件如何渲染。

本章将更新你对 React 生态系统中的状态的现有知识。我们将回顾它是什么以及为什么需要它，并了解它是如何帮助你构建 React 应用的。

我们还将回顾如何通过使用 **useState** 钩子、**useReducer** 钩子和 **React Context** 原生地管理状态。

最后，我们将简要介绍常见的状态管理解决方案，如 **Redux**、**Zustand** 和 **MobX**，并了解它们为什么被创建以及它们共有的主要概念。

到本章结束时，你将学习或记住关于状态的所有必要知识，以便继续阅读本书。你还会注意到不同状态管理解决方案之间状态管理的方式存在一种模式，或者重新认识一个熟悉的术语。剧透一下：它是全局状态。

在本章中，我们将涵盖以下主题：

+   React 中的状态是什么？

+   在 React 中管理状态

+   不同的状态管理库有什么共同之处？

# 技术要求

在本书中，你将看到一些代码片段。如果你想尝试它们，你需要以下工具：

+   一种**集成开发环境**（**IDE**）如 Visual Studio Code。

+   一个网络浏览器（Google Chrome、Firefox 或 Edge）。

+   Node.js。本书中的所有代码都是使用当前 LTS 版本编写的（16.16.0）。

+   一个包管理器（npm、Yarn 或 pnpm）。

+   一个 React 项目。如果你没有，你可以在终端中运行以下命令来创建一个：

    ```js
    npx create-react-app my-react-app
    ```

本章的所有代码示例都可以在 GitHub 上找到：[`github.com/PacktPublishing/State-management-with-React-Query/tree/feat/chapter_1`](https://github.com/PacktPublishing/State-management-with-React-Query/tree/feat/chapter_1)。

# React 中的状态是什么？

状态是你的 React 应用的核心。

我挑战你尝试构建一个没有任何类型状态的 React 应用程序。你可能能够做些什么，但很快你就会得出结论，props 不能为你做所有事情，然后陷入困境。

如介绍中所述，状态是一个可变的数据源，用于存储你的数据。

状态是可变的，这意味着它可以随时间变化。当状态变量发生变化时，你的 React 组件将重新渲染以反映状态对 UI 造成的任何更改。

好的，现在，你可能想知道，“*我在状态中会存储什么？*”嗯，我遵循的一个经验法则是，如果你的数据符合以下任何一点，那么它就不是状态：

+   Props

+   总是相同的 数据

+   可以从其他状态变量或 props 推导出的数据

任何不符合此列表的内容都可以存储在状态中。这意味着像通过请求获取的数据、UI 的浅色或深色模式选项以及从 UI 表单中填写错误得到的错误列表等都是状态的例子。

让我们看看以下示例：

```js
const NotState = ({aList = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10
  ]}) => {
  const value = "a constant value";
  const filteredList = aList.filter((item) => item % 2 ===
    0);
  return filteredList.map((item) =>
    <div key={item}>{item}</div>);
};
```

这里，我们有一个名为 `NotState` 的组件。让我们看看里面的值，并使用我们的经验法则。

`aList` 变量是一个组件属性。由于我们的组件将接收这个属性，因此它不需要是状态。

我们的 `value` 变量被分配了一个字符串值。由于这个值始终是 *常量*，因此它不需要是状态。

最后，`filteredList` 变量是从我们的 `aList` 属性中派生出来的；因此，它不需要是状态。

现在你已经熟悉了状态的概念，让我们动手了解如何在 React 中管理它。

# 在 React 中管理状态

在深入一些示例之前，重要的是要提到，在这本书中，*所有展示的示例都是 React 16.8 版本之后的版本*。这是因为 **React Hooks** 是在这个版本中引入的。Hooks 改变了我们编写 React 代码的方式，并允许出现像 React Query 这样的库，因此任何展示的示例都利用了它们。

什么是 React Query？

React Query 是一个用于在 React 中获取、缓存和更新服务器状态的协议无关的钩子集合。

在本节中，我将向您展示 React 如何在组件中处理状态，以及如果我们需要在组件之间共享状态时应该做什么。

让我们考虑以下场景。

我想构建一个允许我计数的应用程序。在这个应用程序中，我想能够做以下事情：

+   查看当前计数器值

+   增加我的计数器

+   减少我的计数器

+   重置计数器

让我们想象我们有一个名为 `App` 的 React 组件：

```js
const App = () => {
  ...
  return (
    <div className="App">
      <div>Counter: {count}</div>
      <div>
        <button onClick={increment}>+1</button>
        <button onClick={decrement}>-1</button>
        <button onClick={reset}>Reset</button>
      </div>
    </div>
```

这个应用程序提供了处理我们的计数器需求所需的 UI，例如一个 `div`，我们应该用它来显示我们的 `count`，以及三个带有 `onClick` 事件的按钮，等待回调函数执行以下所需的每个动作。

我们只是缺少这个组件的核心，即状态。React 本地为我们提供了两种在应用程序中保存状态的方法：`useState` 和 `useReducer`。

让我们从查看 `useState` 开始。

## 使用 `useState` 管理状态

`useState` 是一个 React 钩子，允许你保存一个有状态值。当你调用这个钩子时，它将返回一个有状态值和一个用于更新它的函数。

让我们看看如何利用 `useState` 构建计数器应用的示例：

```js
const App = () => {
  const [count, setCount] = useState(0);
  const increment = () => setCount((currentCount) =>
    currentCount + 1);
  const decrement = () => setCount((currentCount) =>
    currentCount - 1);
  const reset = () => setCount(0);
  return (
    <div className="App">
      <div>Counter: {count}</div>
      <div>
        <button onClick={increment}>+1</button>
        <button onClick={decrement}>-1</button>
        <button onClick={reset}>Reset</button>
      </div>
    </div>
  );
};
```

前面的代码片段利用 `useState` 钩子来保存我们的计数器状态。当我们第一次调用 `useState` 时，会做两件事：

+   状态值初始化为 0

+   `count` 状态变量被解构；然后，对状态更新函数，称为 `setCount`，也做了同样的处理

在此之后，我们声明函数，在其中我们使用状态更新函数`setCount`来增加、减少或重置我们的状态变量。

最后，我们将状态变量分配给相应的 UI 部分，并将回调传递给按钮的`onClick`事件。

通过这种方式，我们已经构建了一个简单的计数器应用。我们的应用将开始渲染计数为 0。每次我们点击按钮时，它将执行相应的状态更新，重新渲染我们的应用，并显示新的计数值。

`useState`是你在 React 应用中需要任何状态时最常用的答案。但别忘了在应用之前先应用“*我在状态中要存储什么？*”的经验法则！

现在，让我们看看如何使用`useReducer`钩子来管理状态并构建相同的计数器应用的例子。

## 使用 useReducer 管理状态

当我们有一个更复杂的状态时，`useReducer`是首选选项。在使用钩子之前，我们需要做一些设置，以便我们有发送到`useReducer`钩子所需的一切：

```js
const initialState = { count: 0 };
const types = {
  INCREMENT: "increment",
  DECREMENT: "decrement",
  RESET: "reset",
};
const reducer = (state, action) => {
  switch (action) {
    case types.INCREMENT:
      return { count: state.count + 1 };
    case types.DECREMENT:
      return { count: state.count - 1 };
    case types.RESET:
      return { count: 0 };
    default:
      throw new Error("This type does not exist");
  }
};
```

在上述代码片段中，我们创建了三件事：

+   一个`initialState`对象。该对象有一个属性 count，其值为`0`。

+   一个描述我们将支持的所有操作类型的`types`对象。

+   一个**reducer**。这个 reducer 负责接收我们的状态和操作。通过匹配该操作与预期的类型，我们将能够更新状态。

现在设置完成，让我们创建我们的计数器：

```js
const AppWithReducer = () => {
  const [state, dispatch] = useReducer(reducer,
    initialState);
  const increment = () => dispatch(types.INCREMENT);
  const decrement = () => dispatch(types.DECREMENT);
  const reset = () => dispatch(types.RESET);
  return (
    <div className="App">
      <div>Counter: {state.count}</div>
      <div>
        <button onClick={increment}>+1</button>
        <button onClick={decrement}>-1</button>
        <button onClick={reset}>Reset</button>
      </div>
    </div>
  );
};
```

上述代码片段利用`useReducer`钩子来保存我们的计数器状态。当我们第一次调用`useReducer`时，会做三件事：

+   我们向我们的钩子指示应该使用哪个`reducer`。

+   我们使用`initialState`对象初始化我们的状态。

+   我们解构`state`对象和`dispatch`函数，这使得我们可以从`useReducer`钩子中分发操作。

在此之后，我们创建负责调用带有预期操作的`dispatch`函数的函数。

最后，我们将状态变量分配给相应的 UI 部分，并将回调传递给按钮的`onClick`事件。

现在你已经掌握了这两个*钩子*，你现在知道如何在组件中管理状态。

现在，让我们设想以下场景：如果你需要你的计数器状态在其他组件中可访问怎么办？

你可以通过 props 传递它们。但如果这个状态需要发送到树上的五个其他组件和不同级别呢？你会进行 prop-drilling 并将它传递给每个组件吗？

为了处理这种场景并提高代码的可读性，**React Context**被创建出来。

## 使用 React Context 共享状态

Context 允许你在不进行 prop-drilling 的情况下在组件之间原生地共享值。让我们学习如何构建一个上下文来处理我们的计数器：

```js
import { useState, createContext } from "react";
export const CountContext = createContext();
export const CountStore = () => {
  const [count, setCount] = useState(0);
  const increment = () => setCount((currentCount) =>
    currentCount + 1);
  const decrement = () => setCount((currentCount) =>
    currentCount - 1);
  const reset = () => setCount(0);
  return {
    count,
    increment,
    decrement,
    reset,
  };
};
const CountProvider = (children) => {
  return <CountContext.Provider value={CountStore()}
    {...children} />;
};
export default CountProvider;
```

在上述代码片段中，我们做了三件事：

+   使用`createContext`函数创建我们的上下文。

+   创建一个`useState`钩子。在存储的末尾，我们返回一个包含执行状态更新和创建我们的状态变量的函数的对象。

+   创建一个`CountProvider`。这个提供者负责创建一个将用于包裹组件的提供者。这将允许该提供者内部的每个组件都能访问我们的`CountStore`值。

一旦完成这个设置，我们需要确保我们的组件可以访问我们的上下文：

```js
root.render(
  <CountProvider>
    <App />
  </CountProvider>
);
```

前面的代码片段利用了我们在*前面的代码片段*中创建的`CountProvider`来包裹我们的`App`组件。这允许`App`内部的每个组件都能消费我们的上下文：

```js
import { CountContext } from "./CountContext/CountContext";
const AppWithContext = () => {
  const { count, increment, decrement, reset } =
    useContext(CountContext);
  return (
    <div className="App">
      <div>Counter: {count}</div>
      <div>
        <button onClick={increment}>+1</button>
        <button onClick={decrement}>-1</button>
        <button onClick={reset}>Reset</button>
      </div>
    </div>
  );
};
```

最后，在这个代码片段中，我们利用了`useContext`钩子来消费我们的`CountContext`。由于我们的组件是在我们的自定义提供者中渲染的，因此我们可以访问上下文中的状态。

每当上下文中的状态更新时，React 将确保所有消费我们的上下文的组件都会重新渲染，并接收状态更新。这往往会导致不必要的重新渲染，因为如果你只消费状态中的一个变量，而由于某种原因另一个变量发生了变化，那么上下文将迫使所有消费者重新渲染。

上下文的一个缺点是，通常，无关的逻辑会聚集在一起。正如你可以从前面的代码片段中看到的那样，这会以牺牲一些样板代码为代价。

现在，上下文仍然很强大，这是 React 如何让你在组件之间共享状态的方式。然而，它并非一直存在，因此社区不得不想出如何实现状态共享的方法。为此，创建了状态管理库。

# 不同的状态管理库有什么共同之处？

React 为你提供的一个自由是，它不对你的开发强加任何标准或实践。虽然这很好，但它也导致了不同的实践和实现。

为了使这更容易，并为开发者提供一些结构，创建了状态管理库：

+   **Redux**提倡一个以存储、reducer 和选择器为重点的方法。这导致需要学习特定的概念，并在项目中填充大量可能影响代码可读性和增加代码复杂性的样板代码。

+   **Zustand**提倡使用自定义钩子方法，其中每个钩子都持有你的状态。这是迄今为止最简单的解决方案，也是我最喜欢的解决方案。它与 React 协同工作，并完全拥抱钩子。

+   **MobX**不强制架构，而是关注于函数式响应式方法。这导致了一些更具体的概念，实践方式的多样性可能导致开发者遇到与 React 中可能已经遇到的相同的代码结构问题。

所有这些库中有一个共同点，那就是它们都在尝试解决我们尝试用 React Context 解决的问题：*管理我们的* *共享状态* *的方法*。

在 React 树内部可访问多个组件的状态通常被称为全局状态。现在，全局状态常常被误解，这导致你的代码中增加了不必要的复杂性，并且常常需要求助于本节中提到的库。

最后，每个开发者和团队都有自己的偏好和选择。考虑到 React 给你提供了处理状态的自由，你必须考虑每个解决方案的所有优缺点，在做出选择之前。从一个迁移到另一个可能需要大量时间，并完全改变你应用程序中处理状态的模式，所以请明智地选择，并留出足够的时间。

虽然全局状态不是 React Query 被构建的原因，但它对其创建有影响。全局状态通常的组成方式导致了管理其特定部分的需要，这部分有许多挑战。这个特定部分被称为服务器状态，而它历史上的处理方式为激励 Tanner Linsley 创建 React Query 铺平了道路。

# 摘要

在本章中，我们熟悉了状态的概念。到目前为止，你应该理解状态作为 React 应用程序核心的重要性，并知道如何借助`useState`和`useReducer`原生化地管理它。

你了解到有时你需要与多个组件共享状态，你可以通过 Context 或利用第三方状态管理库来实现。每种解决方案都有其优缺点，最终将取决于开发者的个人偏好。

在*第二章*《服务器状态与客户端状态》中，你将更深入地了解全局状态，并发现我们的全局状态通常是服务器和客户端状态的组合。你将学习这些术语的含义，如何识别这些状态，以及与它们相关的常见挑战。
