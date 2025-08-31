# 第一章：*第一章*: 什么是使用 React 钩子的微状态管理？

**状态管理**是开发 React 应用程序中最重要的话题之一。传统上，React 中的状态管理是某种单体结构，提供了一个通用的状态管理框架，并且开发者在框架内创建特定目的的解决方案。

**React hooks** 的引入改变了情况。我们现在有用于状态管理的原始钩子，它们是可重用的，可以用作构建更丰富功能的基石。这使得我们可以使状态管理变得轻量级，换句话说，就是微状态管理。**微状态管理**更具有目的性，并与特定的编码模式一起使用，而单体状态管理则更通用。

在这本书中，我们将探讨使用 React 钩子的各种状态管理模式。我们的重点是全局状态，其中多个组件可以共享状态。React 钩子已经为局部状态提供了良好的功能——即单个组件或小型组件树内的状态。**全局状态**是 React 中的一个难题，因为 React 钩子缺少直接提供全局状态的能力；相反，这留给了社区和生态系统来处理。我们还将探讨一些现有的微状态管理库，每个库都有不同的用途和模式；在这本书中，我们将讨论 Zustand、Jotai、Valtio 和 React Tracked。

重要提示

这本书专注于全局状态，不讨论“通用”状态管理，这是一个独立的话题。最受欢迎的状态管理库之一是 Redux ([`redux.js.org`](https://redux.js.org))，它使用单向数据模型进行状态管理。另一个流行的库是 XState ([`xstate.js.org`](https://xstate.js.org))，它是状态图的实现，是复杂状态的视觉表示。两者都提供了管理状态的高级方法，但这本书的范围之外。另一方面，这些库也具备全局状态的能力。例如，React Redux ([`react-redux.js.org`](https://react-redux.js.org)) 是一个将 React 和 Redux 绑定以用于全局状态的库，这属于本书的范围。为了使本书只关注全局状态，我们不特别讨论与 Redux 相关的 React Redux。

在本章中，我们将定义什么是微状态管理，讨论 React 钩子如何允许微状态管理，以及为什么全局状态具有挑战性。我们还将回顾两个用于状态管理的基本钩子的用法，并比较它们的相似之处和不同之处。

在本章中，我们将涵盖以下主题：

+   理解微状态管理

+   使用钩子

+   探索全局状态

+   使用 `useState`

+   使用 `useReducer`

+   探索 `useState` 和 `useReducer` 之间的相似之处和不同之处

# 技术要求

要运行代码片段，你需要一个 React 环境——例如，Create React App ([`create-react-app.dev`](https://create-react-app.dev)) 或 CodeSandbox ([`codesandbox.io`](https://codesandbox.io))。

预期你已经具备 React 和 React hooks 的基本知识。更准确地说，你应该已经熟悉官方的 React 文档，你可以在这里找到：[`reactjs.org/docs/getting-started.html`](https://reactjs.org/docs/getting-started.html)。

我们不使用类组件，除非你需要学习带有类组件的现有代码，否则没有必要学习它们。

本章的代码可在 GitHub 上找到：[`github.com/PacktPublishing/Micro-State-Management-with-React-Hooks/tree/main/chapter_01`](https://github.com/PacktPublishing/Micro-State-Management-with-React-Hooks/tree/main/chapter_01)。

# 理解微状态管理

什么是微状态管理？目前还没有官方确立的定义；然而，让我们在这里尝试定义一个。

重要提示

这个定义可能不会在未来反映社区标准。

在 React 中，**状态**是指任何代表**用户界面**（**UI**）的数据。状态会随时间变化，React 负责确保组件根据状态进行渲染。

在我们拥有 React hooks 之前，使用单体状态库是一种流行的模式。单一状态覆盖了许多用途，从而提高了开发者的体验，但有时这可能是过度设计，因为单体状态库可能包含未使用的功能。有了 hooks，我们有了创建状态的新方法。这使我们能够为每个特定的目的提供不同的解决方案。以下是一些例子：

+   表单状态应该与全局状态分开处理，而单状态解决方案是无法做到这一点的。

+   服务器缓存状态有一些独特的特性，例如重新获取，这是与其他状态不同的功能。

+   导航状态有特殊要求，即原始状态位于浏览器端，再次强调，单状态解决方案不适用。

解决这些问题是 React hooks 的一个目标。React hooks 的趋势是针对各种状态使用专门的解决方案。有许多基于 hooks 的库可以解决诸如表单状态、服务器缓存状态等问题。

仍然需要通用状态管理，因为我们还需要处理那些未被目的导向解决方案覆盖的状态。通用状态管理的工作量在应用程序中各不相同。例如，主要处理服务器状态的应用程序可能只需要一个或几个小的全局状态。另一方面，与该应用程序中所需的服务器状态相比，一个丰富的图形应用程序可能需要更多的全局状态。

因此，通用状态管理的解决方案应该是轻量级的，开发者可以根据自己的需求选择一个。这就是我们所说的微状态管理。为了定义这个概念，它是在 React 中的轻量级状态管理，其中每个解决方案都有几个不同的特性，开发者可以根据应用需求从可能的解决方案中选择一个。

微状态管理可能有多个要求，以满足开发者各种需求。有基础状态管理需求，例如做以下这些事情：

+   读取状态

+   更新状态

+   使用状态渲染

但是，可能还有其他额外的要求，例如以下这些：

+   优化重新渲染

+   与其他系统交互

+   异步支持

+   派生状态

+   简单的语法；等等

然而，我们不需要所有功能，其中一些可能存在冲突。因此，微状态管理解决方案也不能是一个单一的解决方案。针对不同的需求，有多个解决方案。

关于微状态管理和其库的另一个需要提及的方面是其学习曲线。易于学习对于通用状态管理也很重要，但由于微状态管理覆盖的使用案例可能较小，因此应该更容易学习。更简单的学习曲线将导致更好的开发者体验和更高的生产力。

在本节中，我们讨论了什么是微状态管理。接下来，我们将看到一些处理状态的钩子的概述。

# 与钩子一起工作

React 钩子对于微状态管理至关重要。React 钩子包括一些原始钩子，用于实现状态管理解决方案，例如以下这些：

+   `useState` 钩子是一个创建局部状态的基本函数。得益于 React 钩子的可组合性，我们可以创建一个自定义钩子，它可以基于 `useState` 添加各种功能。

+   `useReducer` 钩子也可以创建局部状态，通常用作 `useState` 的替代品。我们将在本章后面重新审视这些钩子，以了解 `useState` 和 `useReducer` 之间的相似之处和不同之处。

+   `useEffect` 钩子允许我们在 React 渲染过程之外运行逻辑。对于开发全局状态管理库来说，这尤为重要，因为它使我们能够实现与 React 组件生命周期协同工作的功能。

React 钩子之所以新颖，是因为它们允许你从 UI 组件中提取逻辑。例如，以下是一个 `useState` 钩子简单使用的计数器示例：

```js
const Component = () => {
  const [count, setCount] = useState(0);
  return (
    <div>
      {count}
      <button onClick={() => setCount((c) => c + 1)}>+1
      </button>
    </div>
  );
};
```

现在，让我们看看如何提取逻辑。使用相同的计数器示例，我们将创建一个自定义钩子，命名为 `useCount`，如下所示：

```js
const useCount = () => {
  const [count, setCount] = useState(0);
  return [count, setCount];
};
const Component = () => {
  const [count, setCount] = useCount();
  return (
    <div>
      {count}
      <button onClick={() => setCount((c) => c + 1)}>
        +1
      </button>
    </div>
  );
};
```

它的变化不大，有些人可能会认为这过于复杂。然而，有两个要点需要注意，如下所述：

+   我们现在有一个更清晰的名字——`useCount`。

+   `Component` 与 `useCount` 的实现无关。

第一点对于一般编程来说非常重要。如果我们正确命名自定义钩子，代码就更容易阅读。例如，你可能会将 `useCount` 命名为 `useScore`、`usePercentage` 或 `usePrice`。尽管它们有相同的实现，但如果名称不同，我们就会将其视为不同的钩子。命名事物非常重要。

当涉及到微状态管理库时，第二点也很重要。由于 `useCount` 是从 `Component` 中提取出来的，我们可以添加功能而不会破坏组件。

例如，当计数改变时，我们想在控制台输出一个调试信息。为此，我们将执行以下代码：

```js
const useCount = () => {
  const [count, setCount] = useState(0);
  useEffect(() => {
    console.log('count is changed to', count);
  }, [count]);
  return [count, setCount];
};
```

只需更改 `useCount`，我们就可以添加显示调试信息的特性。我们不需要更改组件。这就是将逻辑提取为自定义钩子的好处。

我们还可以添加一条新规则。假设我们不想允许计数任意改变，而只想通过每次增加一来改变。以下自定义钩子完成了这项工作：

```js
const useCount = () => {
  const [count, setCount] = useState(0);
  const inc = () => setCount((c) => c + 1);
  return [count, inc];
};
```

这为整个生态系统提供了提供各种目的的自定义钩子打开了大门。它们可以是添加微小功能的包装器，也可以是执行更大任务的巨大钩子。

你将在公共领域找到许多自定义钩子，这些钩子可以在 **Node 包管理器**（**npm**）([`www.npmjs.com/search?q=react%20hooks`](https://www.npmjs.com/search?q=react%20hooks)) 或 GitHub ([`github.com/search?q=react+hooks&type=repositories`](https://github.com/search?q=react+hooks&type=repositories)) 上找到。

我们还应该稍微讨论一下 suspense 和并发渲染，因为 React hooks 是设计和开发来与这些模式一起工作的。

## 数据获取和并发渲染的 suspense

数据获取和并发渲染的 suspense 尚未由 React 发布，但简要提及它们是很重要的。

重要提示

数据获取和并发渲染的 suspense 在正式发布时可能会有不同的名称，但这些都是写作时的名称。

`async`。

**并发渲染**是一种将渲染过程分割成块以避免长时间阻塞**中央处理器**（**CPU**）的机制。

React hooks 是设计来与这些机制一起工作的；然而，你需要避免误用它们。

例如，一条规则是你不应该修改现有的 `state` 对象或 `ref` 对象。这样做可能会导致意外的行为，例如不触发重新渲染、触发过多的重新渲染，以及触发部分重新渲染（意味着当应该重新渲染时，一些组件重新渲染而其他组件没有）。

钩子函数和组件函数可以被多次调用。因此，另一个规则是这些函数必须足够“纯”，以便它们在多次调用时表现一致。

这些是人们经常违反的两个主要规则。在实践中，这是一个难题，因为即使你的代码违反了这些规则，在非并发渲染中也可能只是工作，人们就不会注意到误用。即使在并发渲染中，也可能在一定程度上没有问题，人们只会偶尔看到问题。这使得对于第一次使用 React 的初学者来说尤其困难。

除非你熟悉这些概念，否则最好使用经过良好设计和实战检验的（微）状态管理库来处理 React 的未来/较新版本。

重要提示

到目前为止，并发渲染在*React 18 工作组*中有所描述，您可以在以下链接中了解更多信息：[`github.com/reactwg/react-18/discussions`](https://github.com/reactwg/react-18/discussions)。

在本节中，我们回顾了基本的 React 钩子，并对概念有了更深入的理解。接下来，我们将开始探索全局状态，这是本书的主要内容。

# 探索全局状态

React 为在组件中定义并在组件树内使用的状态提供了原始的钩子，如`useState`。这些通常被称为局部状态。

以下示例使用局部状态：

```js
const Component = () => {
  const [state, setState] = useState();
  return (
    <div>
      {JSON.stringify(state)}
      <Child state={state} setState={setState} />
    </div>
  );
};
const Child = ({ state, setState }) => {
  const setFoo = () => setState(
    (prev) => ({ ...prev, foo: 'foo' })
  );
  return (
    <div>
      {JSON.stringify(state)}
      <button onClick={setFoo}>Set Foo</button>
    </div>
  );
};
```

另一方面，全局状态是一种在多个组件中使用的状态，通常在应用程序中相隔甚远。全局状态不一定是单例的，我们可能将全局状态称为共享状态，以明确它不是单例。

以下代码片段提供了一个 React 组件具有全局状态的示例：

```js
const Component1 = () => {
  const [state, setState] = useGlobalState();
  return (
    <div>
      {JSON.stringify(state)}
    </div>
  );
};
const Component2 = () => {
  const [state, setState] = useGlobalState();
  return (
    <div>
      {JSON.stringify(state)}
    </div>
  );
};
```

由于我们尚未定义`useGlobalState`，它将不起作用。在这种情况下，我们希望`Component1`和`Component2`具有相同的状态。

在 React 中实现全局状态并非易事。这主要是因为 React 基于组件模型。在组件模型中，局部性很重要，这意味着组件应该是隔离的，并且应该是可重用的。

关于组件模型的注意事项

组件是单元的可重用部分，就像一个函数。如果你定义了一个组件，它可以被多次使用。这只有在组件定义是自包含的情况下才可能。如果组件依赖于外部的东西，它可能不可重用，因为它的行为可能不一致。技术上，组件本身不应该依赖于全局状态。

React 没有提供全局状态的直接解决方案，这似乎取决于开发者和社区。已经提出了许多解决方案，每个都有其优缺点。本书的目标是展示典型解决方案并讨论这些优缺点，我们将在接下来的章节中这样做：

+   *第三章*，*使用上下文共享组件状态*

+   *第四章*，*使用订阅共享模块状态*

+   *第五章*，*使用上下文和订阅共享组件状态*

在本节中，我们学习了使用 React hooks 的全局状态会是什么样子。接下来，我们将学习一些`useState`的基础知识，为下一章的讨论做准备。

# 使用 useState

在本节中，我们将学习如何使用`useState`，从基本用法到高级用法。我们从一个最简单的形式开始，即使用新值更新状态，然后是使用函数更新，这是一个非常强大的功能，最后我们将讨论懒初始化。

## 使用值更新状态值

使用`useState`更新状态值的一种方法是通过提供一个新值。你可以向`useState`返回的函数传递一个新值，这将最终用新值替换状态值。

这里是一个显示使用值更新的反例：

```js
const Component = () => {
  const [count, setCount] = useState(0);
  return (
    <div>
      {count}
      <button onClick={() => setCount(1)}>
        Set Count to 1
      </button>
    </div>
  );
};
```

你在`onClick`处理程序中将值`1`传递给`setCount`。如果你点击按钮，它将触发`Component`重新渲染，`count=1`。

如果你再次点击按钮会发生什么？它将再次调用`setCount(1)`，但由于值相同，它“退出”并且组件不会重新渲染。**退出**是 React 中的一个技术术语，基本上意味着避免触发重新渲染。

让我们在这里看看另一个例子：

```js
const Component = () => {
  const [state, setState] = useState({ count: 0 });
  return (
    <div>
      {state.count}
      <button onClick={() => setState({ count: 1 })}>
        Set Count to 1
      </button>
    </div>
  );
};
```

对于第一次点击，这与前面的例子行为完全相同；然而，如果你再次点击按钮，组件将重新渲染。你不会在屏幕上看到任何差异，因为计数没有变化。这是因为第二次点击创建了一个新的对象，`{ count: 1 }`，它与前一个对象不同。

现在，这会导致以下不良做法：

```js
const Component = () => {
  const [state, setState] = useState({ count: 0 });
  return (
    <div>
      {state.count}
      <button
        onClick={() => { state.count = 1; setState(state); }
      >
        Set Count to 1
      </button>
    </div>
  );
};
```

这不符合预期。即使你点击按钮，它也不会重新渲染。这是因为状态对象在引用上没有改变，并且它退出了，这意味着这本身不会触发重新渲染。

最后，有一个关于值更新的有趣用法，我们在这里可以看到：

```js
const Component = () => {
  const [count, setCount] = useState(0);
  return (
    <div>
      {count}
      <button onClick={() => setCount(count + 1)}>
        Set Count to {count + 1}
      </button>
    </div>
  );
};
```

点击按钮将增加计数；然而，如果你足够快地连续点击两次按钮，它将只增加一个数字。这有时是可取的，因为它与按钮标题匹配，但有时不是，如果你期望计算按钮实际被点击的次数。这需要函数更新。

## 使用函数更新状态值

使用`useState`更新状态的另一种方法称为函数更新。

这里是一个显示使用函数更新的反例：

```js
const Component = () => {
  const [count, setCount] = useState(0);
  return (
    <div>
      {count}
      <button onClick={() => setCount((c) => c + 1)}>
        Increment Count
      </button>
    </div>
  );
};
```

这实际上计算了按钮被点击的次数，因为`(c) => c + 1`是按顺序调用的。正如我们在前面的章节中看到的，值更新与`Set Count to {count + 1}`功能具有相同的使用场景。在大多数情况下，如果更新基于前一个值，函数更新工作得更好。`Set Count to {count + 1}`功能实际上意味着它不依赖于前一个值，而是依赖于显示的值。

函数更新也可能发生退出。以下是一个演示此点的例子：

```js
const Component = () => {
  const [count, setCount] = useState(0);
  useEffect(() => {
    const id = setInterval(
      () => setCount((c) => c + 1),
      1000,
    );
    return () => clearInterval(id);
  }, []);
  return (
    <div>
      {count}
      <button
        onClick={() =>
          setCount((c) => c % 2 === 0 ? c : c + 1)}
      >
        Increment Count if it makes the result even
      </button>
    </div>
  );
};
```

如果更新函数返回与上一个状态完全相同的状态，它将退出，并且这个组件不会重新渲染。例如，如果你调用 `setCount((c) => c)`，它将永远不会重新渲染。

## 懒初始化

`useState` 可以接收一个初始化函数，该函数只会在第一次渲染时评估。我们可以这样做：

```js
const init = () => 0;
const Component = () => {
  const [count, setCount] = useState(init);
  return (
    <div>
      {count}
      <button onClick={() => setCount((c) => c + 1)}>
        Increment Count
      </button>
    </div>
  );
};
```

在此示例中，`init` 的使用并不非常有效，因为返回 `0` 不需要太多的计算，但重点是 `init` 函数可以包含复杂的计算，并且只被调用以获取初始状态。`init` 函数是懒加载的，不是在调用 `useState` 之前评估；换句话说，它只在 `mount` 时调用一次。

我们现在已经学会了如何使用 `useState`；接下来是 `useReducer`。

# 使用 `useReducer`

在本节中，我们将学习如何使用 `useReducer`。我们将了解其典型用法、如何退出、使用原始值以及懒初始化。

## 典型用法

Reducer 对于复杂的状态很有帮助。这里是一个具有两个属性对象的简单示例：

```js
const reducer = (state, action) => {
  switch (action.type) {
    case 'INCREMENT':
      return { ...state, count: state.count + 1 };
    case 'SET_TEXT':
      return { ...state, text: action.text };
    default:
      throw new Error('unknown action type');
  }
};
const Component = () => {
  const [state, dispatch] = useReducer(
    reducer,
    { count: 0, text: 'hi' },
  );
  return (
    <div>
      {state.count}
      <button
        onClick={() => dispatch({ type: 'INCREMENT' })}
      >
        Increment count
      </button>
      <input
        value={state.text}
        onChange={(e) =>
          dispatch({ type: 'SET_TEXT', text: e.target.value })}
      />
    </div>
  );
};
```

`useReducer` 允许我们通过将定义的 reducer 函数和初始状态作为参数来预先定义一个 reducer 函数。定义 reducer 函数在 hook 之外的好处是能够分离代码和可测试性。因为 reducer 函数是一个纯函数，所以更容易测试其行为。

## 退出

与 `useState` 一样，退出也适用于 `useReducer`。使用之前的示例，让我们修改 reducer，使其在 `action.text` 为空时退出，如下所示：

```js
const reducer = (state, action) => {
  switch (action.type) {
    case 'INCREMENT':
      return { ...state, count: state.count + 1 };
    case 'SET_TEXT':
      if (!action.text) {
        // bail out
        return state
      }
      return { ...state, text: action.text };
    default:
      throw new Error('unknown action type');
  }
};
```

注意返回 `state` 本身是很重要的。如果你返回 `{ ...state, text: action.text || state.text }` 而不是，它不会退出，因为它正在创建一个新的对象。

## 原始值

`useReducer` 适用于非对象值，如数字和字符串等原始值。使用原始值的 `useReducer` 仍然很有用，因为我们可以在它之外定义复杂的 reducer 逻辑。

这里是一个只有一个数字的 reducer 示例：

```js
const reducer = (count, delta) => {
  if (delta < 0) {
    throw new Error('delta cannot be negative');
  }
  if (delta > 10) {
    // too big, just ignore
    return count
  }
  if (count < 100) {
    // add bonus
    return count + delta + 10
  }
  return count + delta
}
```

注意，动作（即 `delta`）也不一定需要是对象。在这个 reducer 示例中，状态值是一个数字——一个原始值，但逻辑要复杂一些，条件比仅仅加法要多。

## 懒初始化（init）

`useReducer` 需要两个参数。第一个是一个 reducer 函数，第二个是一个初始状态。`useReducer` 接受一个可选的第三个参数，称为 `init`，用于懒初始化。

例如，`useReducer` 可以这样使用：

```js
const init = (count) => ({ count, text: 'hi' });
const reducer = (state, action) => {
  switch (action.type) {
    case 'INCREMENT':
      return { ...state, count: state.count + 1 };
    case 'SET_TEXT':
      return { ...state, text: action.text };
    default:
      throw new Error('unknown action type');
  }
};
const Component = () => {
  const [state, dispatch] = useReducer(reducer, 0, init);
  return (
    <div>
      {state.count}
      <button
        onClick={() => dispatch({ type: 'INCREMENT' })}
      >
        Increment count
      </button>
      <input
        value={state.text}
        onChange={(e) => dispatch({ 
          type: 'SET_TEXT', 
          text: e.target.value,
        })}
      />
    </div>
  );
};
```

`init` 函数只在 `mount` 时调用一次，因此它可以包含复杂的计算。与 `useState` 不同，`init` 函数在 `useReducer` 中接受第二个参数——`initialArg`——在之前的示例中是 `0`。

现在我们已经单独探讨了 `useState` 和 `useReducer`，是时候比较它们了。

# 探索 useState 和 useReducer 之间的相似之处和不同之处

在本节中，我们展示了 `useState` 和 `useReducer` 之间的相似之处和不同之处。

## 使用 `useReducer` 实现 `useState`

用 `useReducer` 替代 `useState` 实现 100% 是可能的。实际上，已知 `useState` 在 React 中是用 `useReducer` 实现的。

重要提示

这可能在未来不会成立，因为 `useState` 可能会被更有效地实现。

以下示例展示了如何使用 `useReducer` 实现 `useState`：

```js
const useState = (initialState) => {
  const [state, dispatch] = useReducer(
    (prev, action) =>
      typeof action === 'function' ? action(prev) : action,
    initialState
  );
  return [state, dispatch];
};
```

这可以简化并改进如下：

```js
const reducer = (prev, action) =>
  typeof action === 'function' ? action(prev): prev;
const useState = (initialState) =>
  useReducer(reducer, initialState);
```

在这里，我们证明了你可以用 `useReducer` 实现用 `useState` 能做到的事情。所以，无论你在哪里使用 `useState`，你都可以直接替换成 `useReducer`。

## 使用 `useState` 实现 `useReducer`

现在，让我们探索是否可能相反——我们能否用 `useState` 替换所有 `useReducer` 的实例？令人惊讶的是，几乎可以。这里的“几乎”意味着存在细微的区别。但总的来说，人们期望 `useReducer` 比起 `useState` 更灵活，让我们看看 `useState` 在现实中是否足够灵活。

以下示例说明了如何使用 `useState` 实现基本 `useReducer` 功能：

```js
const useReducer = (reducer, initialState) => {
  const [state, setState] = useState(initialState);
  const dispatch = (action) =>
    setState(prev => reducer(prev, action));
  return [state, dispatch];
};
```

除了这个基本功能外，我们还可以实现懒初始化。让我们也使用 `useCallback` 来有一个稳定的 dispatch 函数，如下所示：

```js
const useReducer = (reducer, initialArg, init) => {
  const [state, setState] = useState(
    init ? () => init(initialArg) : initialArg,
  );
  const dispatch = useCallback(
    (action) => setState(prev => reducer(prev, action)),
    [reducer]
  );
  return [state, dispatch];
};
```

这种实现几乎完美地作为 `useReducer` 的替代品。你使用 `useReducer` 的用例很可能被这个实现处理。

然而，我们有两个细微的区别。由于它们很微妙，我们通常不会过多地考虑它们。让我们在以下两个小节中了解它们，以获得更深入的理解。

## 使用 `init` 函数

一个区别是我们可以在 hooks 或组件外部定义 `reducer` 和 `init`。这只有在 `useReducer` 中才可能，而不是在 `useState` 中。

这里有一个简单的计数示例：

```js
const init = (count) => ({ count });
const reducer = (prev, delta) => prev + delta;
const ComponentWithUseReducer = ({ initialCount }) => {
  const [state, dispatch] = useReducer(
    reducer,
    initialCount,
    init
  );
  return (
    <div>
      {state}
      <button onClick={() => dispatch(1)}>+1</button>
    </div>
  );
};
const ComponentWithUseState = ({ initialCount }) => {
  const [state, setState] = useState(() => 
    init(initialCount));
  const dispatch = (delta) =>
    setState((prev) => reducer(prev, delta));
  return [state, dispatch];
};
```

正如你在 `ComponentWithUseState` 中所看到的，`useState` 需要两个内联函数，而 `ComponentWithUseReducer` 没有内联函数。这是一件小事，但一些解释器或编译器可以在没有内联函数的情况下进行更好的优化。

## 使用内联 reducer

内联 reducer 函数可以依赖于外部变量。这只有在 `useReducer` 中才可能，而不是在 `useState` 中。这是 `useReducer` 的一个特殊功能。

重要提示

这种能力通常不使用，除非真的有必要，否则不建议使用。

因此，以下代码在技术上是可以的：

```js
const useScore = (bonus) =>
  useReducer((prev, delta) => prev + delta + bonus, 0);
```

即使 `bonus` 和 `delta` 都被更新，这也能正确工作。

使用 `useState` 模拟时，这不会正确工作。它会在前一个渲染中使用旧的 `bonus` 值。这是因为 `useReducer` 在渲染阶段调用 reducer 函数。

正如所注，这通常不常用，所以总的来说，如果我们忽略这种特殊行为，我们可以说 `useReducer` 和 `useState` 基本上是相同的，可以互换。你可以根据你的偏好或编程风格选择任何一个。

# 摘要

在本章中，我们讨论了状态管理并定义了微状态管理，其中 React 钩子在微状态管理中扮演着重要角色。为了为后续章节做准备，我们学习了用于状态管理解决方案的一些 React 钩子，包括 `useState` 和 `useReducer`，同时也探讨了它们的相似之处和不同之处。

在下一章中，我们将学习更多关于全局状态的知识。为此，我们将讨论局部状态以及何时局部状态有效，然后我们将探讨何时需要全局状态。
