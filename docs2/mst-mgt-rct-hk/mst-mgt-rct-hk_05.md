# *第三章*：使用 Context 共享组件状态

React 自 16.3 版本以来提供了 Context。Context 与状态无关，但它是一种从组件到组件传递数据的机制，而不是使用 props。通过将 Context 与组件状态结合，我们可以提供全局状态。

除了自 React 16.3 版本以来提供的 Context 支持，React 16.8 引入了 `useContext` 钩子。通过使用 `useContext` 和 `useState`（或 `useReducer`），我们可以为全局状态创建自定义 hooks。

Context 并非完全为全局状态而设计。其中一个已知的限制是，所有 Context 消费者在更新时都会重新渲染，这可能导致额外的重新渲染。通常建议将全局状态拆分成多个部分。

本章中，我们讨论了使用 Context 的一般建议，并展示了具体的示例。我们还讨论了一些使用 TypeScript 与 Context 结合的技术。目标是让你对使用 Context 进行全局状态管理感到自信。

本章中，我们将涵盖以下主题：

+   探索 `useState` 和 `useContext`

+   理解 Context

+   为全局状态创建 Context

+   使用 Context 的最佳实践

# 技术要求

如果你刚接触 React Context，强烈建议学习一些基础知识；查看官方文档([`reactjs.org/docs/context.html`](https://reactjs.org/docs/context.html))和官方博客([`reactjs.org/blog/2018/03/29/react-v-16-3.html`](https://reactjs.org/blog/2018/03/29/react-v-16-3.html))。

你还应该对 React 有一定的了解，包括 React hooks；你可以参考官方网站([`reactjs.org`](https://reactjs.org))了解更多信息。

在某些代码中，我们使用了 TypeScript，你应该对其有基本的了解；你可以在这里了解更多：[`www.typescriptlang.org`](https://www.typescriptlang.org)。

本章中的代码可在 GitHub 上找到，链接为 [`github.com/PacktPublishing/Micro-State-Management-with-React-Hooks/tree/main/chapter_03`](https://github.com/PacktPublishing/Micro-State-Management-with-React-Hooks/tree/main/chapter_03)。

要运行本章中的代码片段，你需要一个 React 环境——例如，Create React App ([`create-react-app.dev`](https://create-react-app.dev))或 CodeSandbox ([`codesandbox.io`](https://codesandbox.io))。

# 探索 useState 和 useContext

通过结合 `useState` 和 `useContext`，我们可以创建一个简单的全局状态。让我们回顾一下如何在不使用 `useContext` 的情况下使用 `useState`，`useContext` 对于静态值是如何工作的，以及我们如何结合 `useState` 和 `useContext`。

## 不使用 useContext 使用 useState

在深入 `useContext` 之前，让我们回顾一下如何使用 `useState`，以下是一个具体的示例。这个示例将成为本章后续示例的参考。

在这里，我们定义了一个在组件树中较高的 `count` 状态，并通过状态值和更新函数将其传递到树中。

在`App`组件中，我们使用`useState`获取`count`和`setCount`，这些被传递给`Parent`组件。代码在下面的代码片段中展示：

```js
const App = () => {
  const [count, setCount] = useState(0);
  return <Parent count={count} setCount={setCount} />;
};
```

这是一个非常基本的模式，我们称之为*提升状态*，来自*第二章*，*使用本地和全局状态*。

现在，让我们定义一个`Parent`组件。它将两个 props 传递给`Component1`和`Component2`，如下所示：

```js
const Parent = ({ count, setCount }) => (
  <>
    <Component1 count={count} setCount={setCount} />
    <Component2 count={count} setCount={setCount} />
  </>
);
```

从父组件到子组件的这种 props 传递是一个重复的任务，通常被称为**属性钻取**。

`Component1`和`Component2`显示`count`状态和一个按钮，用于通过`setCount`增加`count`状态，如下面的代码片段所示：

```js
const Component1 = ({ count, setCount }) => (
  <div>
    {count}
    <button onClick={() => setCount((c) => c + 1)}>
      +1
    </button>
  </div>
);
const Component2 = ({ count, setCount }) => (
  <div>
    {count}
    <button onClick={() => setCount((c) => c + 2)}>
      +2
    </button>
  </div>
);
```

这两个组件是纯组件，这意味着它们只根据接收到的 props 接收并显示内容。`Component2`与`Component1`略有不同，它将计数增加两次。如果它们是相同的，我们就不需要定义两个组件。

这个例子没有问题。只有当应用变大，向下传递 props 时，这才会没有意义。在这种情况下，`Parent`组件不一定需要知道`count`状态，并且隐藏`Parent`组件中`count`状态的存在可能是合理的。

## 使用静态值`useContext`

React 上下文有助于消除 props。它是一种从父组件向其子组件传递值的方法，而不使用 props。

以下示例展示了如何使用具有静态值的 React 上下文。它有多个提供者来提供不同的值。提供者可以是嵌套的，并且消费者组件（消费者组件意味着具有`useContext`的组件）将选择组件树中最接近的提供者以获取上下文值。只有一个具有`useContext`的组件来消费上下文，该组件在多个地方使用。

首先，我们使用`createContext`定义一个颜色上下文，它接受一个默认值，如下所示：

```js
const ColorContext = createContext('black');
```

在这种情况下，颜色上下文的默认值是`'black'`。如果组件不在任何提供者中，则使用默认值。

现在，我们定义一个消费者组件。它读取颜色上下文并显示该颜色的文本。代码在下面的代码片段中展示：

```js
const Component = () => {
  const color = useContext(ColorContext);
  return <div style={{ color }}>Hello {color}</div>;
};
```

`Component`读取`color`上下文值，但在此阶段，我们不知道颜色是什么，它实际上依赖于上下文。

最后，我们定义一个`App`组件。在`App`组件中的组件树有多个不同颜色的`ColorContext.Provider`组件。代码在下面的代码片段中展示：

```js
const App = () => (
  <>
    <Component />
    <ColorContext.Provider value="red">
      <Component />
    </ColorContext.Provider>
    <ColorContext.Provider value="green">
      <Component />
    </ColorContext.Provider>
    <ColorContext.Provider value="blue">
      <Component />
      <ColorContext.Provider value="skyblue">
        <Component />
      </ColorContext.Provider>
    </ColorContext.Provider>
  </>
);
```

第一个`Component`实例显示颜色`"black"`，因为它没有被任何提供者包裹。第二个和第三个分别显示`"red"`和`"green"`。第四个`Component`实例显示`"blue"`，最后一个`Component`实例显示`"skyblue"`，因为最近的提供者的值是`"skyblue"`，即使它位于包含`"blue"`的提供者内部。

多个提供者和重用消费者组件是 React Context 的重要功能。如果这个功能对你的用例不重要，你可能不需要 React Context。我们将在*第四章* *使用 Context 的最佳实践*部分讨论没有 Context 的订阅方法。

## 使用 useState 与 useContext 结合

现在，让我们学习如何将`useState`和`useContext`结合来结构化我们的代码。我们可以在上下文中传递`state`值和`update`函数，而不是通过 props。

以下示例使用`useState`和`useContext`实现了一个简单的`count`状态。我们定义了一个上下文，它包含`count`状态值和`setCount`更新函数。`Parent`组件不接收 props，`Component1`和`Component2`使用`useContext`获取状态。

首先，我们为`count`状态创建一个上下文。默认值包含一个静态的`count`值和一个回退的空`setCount`函数。代码在下面的代码片段中展示：

```js
const CountStateContext = createContext({
  count: 0,
  setCount: () => {},
});
```

默认值有助于在 TypeScript 中推断类型。然而，在大多数情况下，我们需要状态而不是静态值，因为默认值并不很有用。在这种情况下使用默认值几乎是无意识的，所以我们可能会抛出一个错误。我们将在*使用 Context 的最佳实践*部分稍后讨论一些最佳实践。

`App`组件使用`useState`有一个状态，并将`count`和`setCount`传递给创建的上下文提供者组件，如下面的代码片段所示：

```js
const App = () => {
  const [count, setCount] = useState(0);
  return (
    <CountStateContext.Provider 
      value={{ count, setCount }}
    >
      <Parent />
    </CountStateContext.Provider>
  );
};
```

我们传递给`CountStateContext.Provider`的上下文值是一个包含`count`和`setCount`的对象。此对象结构与默认值相同。

我们定义了一个`Parent`组件。与上一节中的示例不同，我们不需要传递 props。代码在下面的代码片段中展示：

```js
const Parent = () => (
  <>
    <Component1 />
    <Component2 />
  </>
);
```

尽管`Parent`组件位于`App`中的上下文提供者中，但它并不知道`count`状态的存在。`Parent`内部的组件仍然可以通过上下文使用`count`状态。

最后，我们定义了`Component1`和`Component2`。它们从上下文值中获取`count`和`setCount`，而不是从 props 中获取。代码在下面的代码片段中展示：

```js
const Component1 = () => {
  const { count, setCount } = 
    useContext(CountStateContext);
  return (
    <div>
      {count}
      <button onClick={() => setCount((c) => c + 1)}>
        +1
      </button>
    </div>
  );
};
const Component2 = () => {
  const { count, setCount } = 
    useContext(CountStateContext);
  return (
    <div>
      {count}
      <button onClick={() => setCount((c) => c + 2)}>
        +2
      </button>
    </div>
  );
};
```

这些组件获取的上下文值是什么？它们从最近的提供者获取上下文值。我们可以使用多个提供者来提供隔离的计数状态，这再次是使用 React Context 的重要功能。

在本节中，我们学习了 React Context 以及如何使用它创建一个简单的全局状态。接下来，我们将深入了解 React Context 的行为。

# 理解 Context

当一个 Context 提供者有一个新的 Context 值时，所有 Context 消费者都会接收到新的值并重新渲染。这意味着提供者中的值会传播到所有消费者。了解 Context 传播的工作方式和其限制对我们来说很重要。

## Context 传播的工作方式

如果你使用 Context 提供者，你可以更新 Context 值。当 Context 提供者接收到新的 Context 值时，它会触发 *所有* Context 消费者组件的重新渲染。

有时，子组件会因两个原因而重新渲染——一个是因为父组件，另一个是因为 Context。

要在不改变 Context 值的情况下停止重新渲染，在这种情况下，我们可以使用 *提升内容向上* 技术或 `memo`。`memo` 是一个用于包裹组件的函数，用于防止在组件属性没有变化时重新渲染。

让我们通过将一些组件包裹在 `memo` 中来举一个例子，以了解其行为。

与之前的例子一样，我们再次使用一个简单的 Context，它包含一个颜色字符串，如下所示：

```js
const ColorContext = createContext('black');
```

`'black'` 是默认值，如果组件树中没有找到 Context 提供者，将使用此值。

我们接着定义 `ColorComponent`，它类似于之前的例子，但它还有一个 `renderCount` 来显示这个组件被渲染了多少次，如下面的代码片段所示：

```js
const ColorComponent = () => {
  const color = useContext(ColorContext);
  const renderCount = useRef(1);
  useEffect(() => {
    renderCount.current += 1;
  });
  return (
    <div style={{ color }}>
      Hello {color} (renders: {renderCount.current})
    </div>
  );
};
```

我们使用 `useRef` 为 `renderCount`。`renderCount.current` 是一个表示渲染次数的数字。`renderCount.current` 数字通过 `useEffect` 增加一。

接下来是 `MemoedColorComponent`，它是被 `memo` 包裹的 `ColorComponent`。代码在下面的代码片段中展示：

```js
const MemoedColorComponent = memo(ColorComponent);
```

`memo` 函数用于从基础组件创建一个记忆化的组件。记忆化组件对相同的属性产生稳定的结果。

我们定义了另一个组件，`DummyComponent`，它没有使用 `useContext`。代码在下面的代码片段中展示：

```js
const DummyComponent = () => {
  const renderCount = useRef(1);
  useEffect(() => {
    renderCount.current += 1;
  });
  return <div>Dummy (renders: {renderCount.current})</div>;
};
```

这个组件是为了与 `ColorComponent` 的行为进行比较。

我们还使用 `memo` 为 `DummyComponent` 定义了 `MemoedDummyComponent`，如下所示：

```js
const MemoedDummyComponent = memo(DummyComponent);
```

接下来，我们定义一个 `Parent` 组件；它包含我们之前定义的四种组件。代码在下面的代码片段中展示：

```js
const Parent = () => (
  <ul>
    <li><DummyComponent /></li>
    <li><MemoedDummyComponent /></li>
    <li><ColorComponent /></li>
    <li><MemoedColorComponent /></li>
  </ul>
);
```

最后，`App` 组件使用 `useState` 有一个颜色状态，并将值传递给 `ColorContext.Provider`。它还显示一个文本字段来更改颜色状态。代码在下面的代码片段中展示：

```js
const App = () => {
  const [color, setColor] = useState('red');
  return (
    <ColorContext.Provider value={color}>
      <input
        value={color}
        onChange={(e) => setColor(e.target.value)}
      />
      <Parent />
    </ColorContext.Provider>
  );
};
```

这个例子表现如下：

1.  初始时，所有组件都会进行渲染。

1.  如果你更改文本输入中的值，`App` 组件会由于 `useState` 而重新渲染。

1.  然后，`ColorContext.Provider` 获得了一个新值，同时 `Parent` 组件也进行了渲染。

1.  `DummyComponent` 会渲染，但 `MemoedDummyComponent` 不会。

1.  `ColorComponent` 由于两个原因而渲染——首先，父组件渲染，其次，上下文发生变化。

1.  `MemoedColorComponent` 由于上下文变化而渲染。

这里重要的是要学习的是 `memo` 并不能阻止内部上下文消费者重新渲染。这显然是不可避免的，否则组件可能会有不一致的上下文值。

## 使用上下文处理对象时的限制

使用原始值作为上下文值是直观的，但使用对象值可能需要谨慎，因为它们的行为。一个对象可能包含多个值，而上下文消费者可能不会使用它们全部。

以下示例是为了重现这样一个案例，其中组件只使用对象的一部分。

首先，我们定义一个上下文，其值是一个包含两个计数 `count1` 和 `count2` 的对象，如下所示：

```js
const CountContext = createContext({ count1: 0, count2: 0 });
```

使用这个计数上下文，我们定义一个 `Counter1` 组件来显示 `count1`。我们有 `renderCount` 来显示渲染计数。我们还定义了一个 `MemoedCounter1` 组件，这是一个记忆化组件。代码如下所示：

```js
const Counter1 = () => {
  const { count1 } = useContext(CountContext);
  const renderCount = useRef(1);
  useEffect(() => {
    renderCount.current += 1;
  });
  return (
    <div>
      Count1: {count1} (renders: {renderCount.current})
    </div>
  );
};
const MemoedCounter1 = memo(Counter1);
```

注意到 `Counter1` 组件只使用了上下文值中的 `count1`。

同样，我们定义了一个显示 `count2` 和记忆化的 `MemoCounter2` 组件的 `Counter2` 组件，如下所示：

```js
const Counter2 = () => {
  const { count2 } = useContext(CountContext);
  const renderCount = useRef(1);
  useEffect(() => {
    renderCount.current += 1;
  });
  return (
    <div>
      Count2: {count2} (renders: {renderCount.current})
    </div>
  );
};
const MemoCounter2 = memo(Counter2);
```

`Parent` 组件有两个记忆化的组件，如下面的代码片段所示：

```js
const Parent = () => (
  <>
    <MemoCounter1 />
    <MemoCounter2 />
  </>
);
```

最后，`App` 组件有两个计数，使用两个 `useState` 钩子，并通过一个上下文提供这两个计数。它有两个按钮分别增加两个计数，如下面的代码片段所示：

```js
const App = () => {
  const [count1, setCount1] = useState(0);
  const [count2, setCount2] = useState(0);
  return (
    <CountContext.Provider value={{ count1, count2 }}>
      <button onClick={() => setCount1((c) => c + 1)}>
        {count1}
      </button>
      <button onClick={() => setCount2((c) => c + 1)}>
        {count2}
      </button>
      <Parent />
    </CountContext.Provider>
  );
};
```

再次注意，两个按钮的位置并不重要。

两个计数 `count1` 和 `count2` 完全独立——`Counter1` 只使用 `count1`，而 `Counter2` 只使用 `count2`。因此，理想情况下，`Counter1` 只应在 `count1` 发生变化时重新渲染。如果 `Counter1` 在不改变 `count1` 的情况下重新渲染，它会产生相同的结果，这意味着这只是额外的重新渲染。在这个例子中，即使只有 `count2` 发生变化，`Counter1` 也会重新渲染。

这是我们利用 React Context 时应该注意的额外重新渲染限制。

额外的重新渲染

额外的重新渲染是纯粹的开销，应该从技术上避免。然而，除非性能是一个大问题，否则这将是可行的，因为用户不会注意到几个额外的重新渲染。为了避免几个额外的重新渲染而过度设计可能在实际中不值得解决。

在本节中，我们学习了 React Context 的行为以及为什么它限制为只能与对象一起使用。接下来，我们将学习一些实现全局状态的典型模式。

# 为全局状态创建一个上下文

根据 React Context 的行为，我们将讨论两个关于使用全局状态与上下文结合的解决方案，如下：

+   创建小的状态片段

+   使用 `useReducer` 创建一个状态并使用多个上下文进行传播

让我们看看每个解决方案。

## 创建小的状态片段

第一种解决方案是将全局状态分割成片段。因此，而不是使用一个大型的组合对象，创建一个全局状态和每个片段的上下文。

下面的示例创建了两个 `count` 状态，每个状态都有一个上下文和提供者组件。

首先，我们定义了两个上下文，`Count1Context` 和 `Count2Context`，每个片段一个，如下所示：

```js
type CountContextType = [
  number,
  Dispatch<SetStateAction<number>>
];

const Count1Context = createContext<CountContextType>([
  0,
  () => {}
]);
const Count2Context = createContext<CountContextType>([
  0,
  () => {}
]);
```

上下文值是一个包含 `count` 值和更新函数的元组。我们指定了一个静态值和一个占位函数作为默认值。

然后我们定义了一个仅使用 `Count1Context` 的 `Counter1` 组件，如下所示：

```js
const Counter1 = () => {
  const [count1, setCount1] = useContext(Count1Context);
  return (
    <div>
      Count1: {count1}
      <button onClick={() => setCount1((c) => c + 1)}>
        +1
      </button>
    </div>
  );
};
```

注意到 `Counter1` 的实现仅依赖于 `Count1Context`，并且它不知道任何其他上下文。

同样，我们定义了一个仅使用 `Count2Context` 的 `Counter2` 组件，如下所示：

```js
const Counter2 = () => {
  const [count2, setCount2] = useContext(Count2Context);
  return (
    <div>
      Count2: {count2}
      <button onClick={() => setCount2((c) => c + 1)}>
        +1
      </button>
    </div>
  );
};
```

`Parent` 组件包含 `Counter1` 和 `Counter2` 组件，如下面的代码片段所示：

```js
const Parent = () => (
  <div>
    <Counter1 />
    <Counter1 />
    <Counter2 />
    <Counter2 />
  </div>
);
```

`Parent` 组件有两个计数器，仅用于演示目的。

我们为 `Count1Context` 定义了一个 `Count1Provider` 组件。`Count1Provider` 组件使用 `useState` 有一个 `count` 状态，并将计数值和 `update` 函数传递给 `Count1Context.Provider` 组件，如下面的代码片段所示：

```js
const Count1Provider = ({
  children
}: {
  children: ReactNode
}) => {
  const [count1, setCount1] = useState(0);
  return (
    <Count1Context.Provider value={[count1, setCount1]}>
      {children}
    </Count1Context.Provider>
  );
};
```

同样，我们为 `Count2Context` 定义了一个 `Count2Provider` 组件，如下所示：

```js
const Count2Provider = ({
  children
}: {
  children: ReactNode
}) => {
  const [count2, setCount2] = useState(0);
  return (
    <Count2Context.Provider value={[count2, setCount2]}>
      {children}
    </Count2Context.Provider>
  );
};
```

`Count1Provider` 和 `Count2Provider` 组件类似；唯一的区别是提供值的上下文。

最后，`App` 组件有一个包含两个提供者组件的 `Parent` 组件，如下面的代码片段所示：

```js
const App = () => (
  <Count1Provider>
    <Count2Provider>
      <Parent />
    </Count2Provider>
  </Count1Provider>
);
```

注意到 `App` 组件有两个嵌套的提供者组件。拥有更多的提供者组件会导致更深层次的嵌套。我们将在 *使用上下文的最佳实践* 部分讨论缓解嵌套的方法。

这个示例不受我们在上一节中描述的额外重新渲染限制的影响。这是因为上下文只持有原始值。`Counter1` 和 `Counter2` 组件仅在 `count1` 和 `count2` 分别更改时重新渲染。对于每个状态创建一个提供者是很必要的；否则，`useState` 会返回一个新的元组对象，上下文将触发重新渲染。

如果你确定一个对象一次就会被使用，并且使用不会触及上下文行为的限制，将对象作为上下文值是完全可接受的。以下是一个一次性使用的 `user` 对象的示例：

```js
const [user, setUser] = useState({
  firstName: 'react',
  lastName: 'hooks'
});
```

在这种情况下，将其分割成上下文没有意义。对于 `user` 对象使用单个上下文会更好。

接下来，让我们看看另一个解决方案。

## 使用 useReducer 创建一个状态并通过多个上下文传播

第二种解决方案是创建一个单一的状态，并使用多个上下文来分配状态片段。在这种情况下，应该使用一个单独的上下文来分配更新状态的函数。

以下示例基于`useReducer`。它有三个上下文；两个用于状态片段，最后一个用于分发函数。

首先，我们为两个计数创建两个值上下文，并为将用于更新两个计数的分发函数创建一个上下文，如下所示：

```js
type Action = { type: "INC1" } | { type: "INC2" };

const Count1Context = createContext<number>(0);
const Count2Context = createContext<number>(0);
const DispatchContext = createContext<Dispatch<Action>>(
  () => {}
);
```

在这种情况下，如果我们有更多的计数，我们将创建更多的计数上下文，但分发上下文将保持只有一个。

我们将在本例的后面定义分发函数的 reducer。

接下来，我们定义一个使用两个上下文的`Counter1`组件——一个用于值，另一个用于分发函数，如下所示：

```js
const Counter1 = () => {
  const count1 = useContext(Count1Context);
  const dispatch = useContext(DispatchContext);
  return (
    <div>
      Count1: {count1}
      <button onClick={() => dispatch({ type: "INC1" })}>
        +1
      </button>
    </div>
  );
};
```

`Counter1`组件从`Count1Context`读取`count1`。

我们定义一个`Counter2`组件，它与`Counter1`类似，只是从不同的上下文中读取`count2`。代码如下所示：

```js
const Counter2 = () => {
  const count2 = useContext(Count2Context);
  const dispatch = useContext(DispatchContext);
  return (
    <div>
      Count2: {count2}
      <button onClick={() => dispatch({ type: "INC2" })}>
        +1
      </button>
    </div>
  );
};
```

`Counter1`和`Counter2`组件都使用相同的`DispatchContext`上下文。

如下所示，`Parent`组件与之前的例子相同：

```js
const Parent = () => (
  <div>
    <Counter1 />
    <Counter1 />
    <Counter2 />
    <Counter2 />
  </div>
);
```

现在，我们定义一个在本例中独特的`Provider`组件。`Provider`组件使用`useReducer`。reducer 函数处理两种动作类型——`INC1`和`INC2`。`Provider`组件包括我们之前定义的三个上下文的提供者。代码如下所示：

```js
const Provider = ({ children }: { children: ReactNode }) => {
  const [state, dispatch] = useReducer(
    (
      prev: { count1: number; count2: number },
      action: Action
    ) => {
      if (action.type === "INC1") {
        return { ...prev, count1: prev.count1 + 1 };
      }
      if (action.type === "INC2") {
        return { ...prev, count2: prev.count2 + 1 };
      }
      throw new Error("no matching action");
    },
    {
      count1: 0,
      count2: 0,
    }
  );
  return (
    <DispatchContext.Provider value={dispatch}>
      <Count1Context.Provider value={state.count1}>
        <Count2Context.Provider value={state.count2}>
          {children}
        </Count2Context.Provider>
      </Count1Context.Provider>
    </DispatchContext.Provider>
  );
};
```

由于 reducer 的存在，代码稍微长了一些，它可能更复杂。重点是嵌套提供者，提供每个状态片段和一个分发函数。

最后，`App`组件中只包含`Provider`组件和`Parent`组件，如下面的代码片段所示：

```js
const App = () => (
  <Provider>
    <Parent />
  </Provider>
);
```

本例也不受额外重新渲染限制的影响；在状态中更改`count1`只会触发`Counter1`重新渲染，而`Counter2`不受影响。

使用单个状态而不是使用多个状态在上一个示例中的好处是，单个状态可以通过一个动作更新多个片段。例如，你可以在 reducer 中添加如下内容：

```js
      if (action.type === "INC_BOTH") {
        return {
          ...prev,
          count1: prev.count1 + 1,
          count2: prev.count2 + 1,
        };
      }
```

如我们在第一个解决方案中讨论的那样，在这个解决方案中也可以为对象（例如`user`对象）创建上下文。

在本节中，我们学习了两种使用上下文处理全局状态的方法。它们是典型的方法，但会有很多变体。关键是使用多个上下文来避免额外的重新渲染。在下一节中，我们将学习一些基于多个上下文处理全局状态的最佳实践。

# 使用上下文的最佳实践

在本节中，我们将学习三种处理全局状态上下文的模式，如下所示：

+   创建自定义钩子和提供者组件

+   带有自定义钩子的工厂模式

+   使用`reduceRight`避免提供者嵌套

让我们逐一看看。

## 创建自定义钩子和提供者组件

在本章前面的示例中，我们直接使用`useContext`来获取上下文值。现在，我们将显式创建自定义钩子来访问上下文值以及提供者组件。这允许我们隐藏上下文并限制它们的用法。

下面的示例创建了自定义钩子和提供者组件。我们设置默认上下文值为`null`并在自定义钩子中检查该值是否为`null`。这检查自定义钩子是否在提供者下使用。

我们首先做的事情，就像往常一样，是创建一个上下文；这次，上下文的默认值是`null`，这表示默认值不能使用，并且总是需要提供者。代码在下面的代码片段中展示：

```js
type CountContextType = [
  number,
  Dispatch<SetStateAction<number>>
];

const Count1Context = createContext<
  CountContextType | null
>(null);
```

然后，我们定义`Count1Provider`，使用`useState`创建一个状态并将其传递给`Count1Context.Provider`，如下面的代码片段所示：

```js
export const Count1Provider = ({
  children
}: {
  children: ReactNode
}) => (
  <Count1Context.Provider value={useState(0)}>
    {children}
  </Count1Context.Provider>
);
```

注意，我们在`const [count, setCount] = useState(0);`和`return <Count1Context.Provider value={[count, setCount]}>`一行中使用了`useState(0)`。

接下来，我们定义一个`useCount1`钩子来从`Count1Context`返回一个值。在这里，我们检查从上下文值中抛出的`null`会引发一个有意义的错误。开发者经常犯错误，有明确的错误会使我们更容易检测到错误。代码在下面的代码片段中展示：

```js
export const useCount1 = () => {
  const value = useContext(Count1Context);
  if (value === null) throw new Error("Provider missing");
  return value;
};
```

接着，我们创建`Count2Context`，定义一个`Count2Provider`组件和一个`useCount2`钩子（它们与`Count1Context`、`Count1Provider`和`useCount1`相同，只是名称不同）。代码在下面的代码片段中展示：

```js
const Count2Context = createContext<
  CountContextType | null
>(null);
export const Count2Provider = ({
  children
}: {
  children: ReactNode
}) => (
  <Count2Context.Provider value={useState(0)}>
    {children}
  </Count2Context.Provider>
);
export const useCount2 = () => {
  const value = useContext(Count2Context);
  if (value === null) throw new Error("Provider missing");
  return value;
};
```

接下来，我们定义一个`Counter1`组件来使用`count1`状态并显示计数和一个按钮。注意，在下面的代码片段中，这个组件不知道上下文的存在，它在`useCount1`钩子中被隐藏：

```js
const Counter1 = () => {
  const [count1, setCount1] = useCount1();
  return (
    <div>
      Count1: {count1}
      <button onClick={() => setCount1((c) => c + 1)}>
        +1
      </button>
    </div>
  );
};
```

同样，我们定义一个`Counter2`组件，如下所示：

```js
const Counter2 = () => {
  const [count2, setCount2] = useCount2();
  return (
    <div>
      Count2: {count2}
      <button onClick={() => setCount2((c) => c + 1)}>
        +1
      </button>
    </div>
  );
};
```

注意，`Counter2`组件几乎与`Counter1`组件相同。主要区别在于`Counter2`组件使用`useCount2`钩子而不是`useCount1`钩子。

我们定义一个`Parent`组件，该组件定义了之前定义的`Counter1`和`Counter2`，如下所示：

```js
const Parent = () => (
  <div>
    <Counter1 />
    <Counter1 />
    <Counter2 />
    <Counter2 />
  </div>
);
```

最后，定义一个`App`组件来完成示例。它用两个提供者组件包裹`Parent`组件，如下所示：

```js
const App = () => (
  <Count1Provider>
    <Count2Provider>
      <Parent />
    </Count2Provider>
  </Count1Provider>
);
```

尽管这个片段不是很明确，我们通常为每个上下文有一个单独的文件，例如`contexts/count1.jsx`，并只导出自定义钩子，如`useCount1`和提供者组件，如`Count1Provider`。在这种情况下，`Count1Context`没有被导出。

## 带有自定义钩子的工厂模式

创建自定义钩子和提供者组件是一项相对重复的任务；然而，我们可以创建一个函数来完成这项任务。

下面的示例展示了`createStateContext`的具体实现。

`createStateContext` 函数接受一个 `useValue` 自定义钩子，该钩子接受一个初始值并返回一个状态。如果你使用 `useState`，它返回一个包含 `state` 值和 `setState` 函数的元组。`createStateContext` 函数返回一个包含提供者组件和用于获取状态的自定义钩子的元组。这是我们之前章节中学到的模式。

此外，这还提供了一个新特性；提供者组件接受一个可选的 `initialValue` 属性，该属性传递给 `useValue`。这允许你在运行时设置状态的初始值，而不是在创建时定义初始值。代码在下面的片段中展示：

```js
const createStateContext = (
  useValue: (init) => State,
) => {
  const StateContext = createContext(null);
  const StateProvider = ({
    initialValue,
    children,
  }) => (
    <StateContext.Provider value={useValue(initialValue)}>
      {children}
    </StateContext.Provider>
  );
  const useContextState = () => {
    const value = useContext(StateContext);
    if (value === null) throw new Error("Provider
      missing");
    return value;
  };
  return [StateProvider, useContextState] as const;
};
```

现在，让我们看看如何使用 `createStateContext`。我们定义一个自定义钩子，`useNumberState`；它接受一个可选的 `init` 参数。然后我们像下面这样使用 `useState`：

```js
const useNumberState = (init) => useState(init || 0);
```

通过将 `useNumberState` 传递给 `createStateContext`，我们可以创建任意数量的状态上下文；我们创建了两组。`useCount1` 和 `useCount2` 的类型是从 `useNumberState` 推断出来的。代码在下面的片段中展示：

```js
const [Count1Provider, useCount1] =
  createStateContext(useNumberState);
const [Count2Provider, useCount2] =
  createStateContext(useNumberState);
```

注意我们通过 `createStateContext` 避免了重复的定义。

然后，我们定义 `Counter1` 和 `Counter2` 组件。使用 `useCount1` 和 `useCount2` 的方式与上一个示例相同，如下代码片段所示：

```js
const Counter1 = () => {
  const [count1, setCount1] = useCount1();
  return (
    <div>
      Count1: {count1}
      <button onClick={() => setCount1((c) => c + 1)}>
        +1
      </button>
    </div>
  );
};
const Counter2 = () => {
  const [count2, setCount2] = useCount2();
  return (
    <div>
      Count2: {count2}
      <button onClick={() => setCount2((c) => c + 1)}>
        +1
      </button>
    </div>
  );
};
```

最后，我们创建 `Parent` 和 `App` 组件。使用 `Count1Provider` 和 `Count2Provider` 的方式也是相同的，如下所示：

```js
const Parent = () => (
  <div>
    <Counter1 />
    <Counter1 />
    <Counter2 />
    <Counter2 />
  </div>
);
const App = () => (
  <Count1Provider>
    <Count2Provider>
      <Parent />
    </Count2Provider>
  </Count1Provider>
);
```

注意我们是如何从上一个示例中减少代码的。`createStateContext` 的整个目的就是为了避免重复代码并提供相同的功能。

与使用 `useState` 的 `useNumberState` 不同，我们可以使用 `useReducer` 创建自定义钩子，如下所示：

```js
const useMyState = () => useReducer({}, (prev, action) => {
  if (action.type === 'SET_FOO') {
    return { ...prev, foo: action.foo };
  }
  // ...
};
```

我们也可以创建一个更复杂的钩子。以下示例有 `inc1` 和 `inc2` 自定义动作函数。它使用 `useEffect` 在控制台显示更新的日志：

```js
const useMyState = (initialState = { count1: 0, count2: 0 }) => {
  const [state, setState] = useState(initialState);
  useEffect(() => {
    console.log('updated', state);
  });
  const inc1 = useCallback(() => {
    setState((prev) => ({
      ...prev,
      count1: prev.count1 + 1
    }));
  }, []);
  const inc2 = useCallback(() => {
    setState((prev) => ({
      ...prev,
      count2: prev.count2 + 1
    }));
  }, []);
  return [state, { inc1, inc2 }];
};
```

我们仍然可以使用 `createStateContext` 函数为这些 `useMyState` 钩子和任何其他自定义钩子。

值得注意的是，这种工厂模式在 TypeScript 中工作得很好。TypeScript 通过类型提供额外的检查，开发者可以从类型检查中获得更好的体验。下面的代码片段展示了 `createStateContext` 和 `useNumberState` 的类型化版本：

```js
const createStateContext = <Value, State>(
  useValue: (init?: Value) => State
) => {
  const StateContext = createContext<State | null>(null);
  const StateProvider = ({
    initialValue,
    children,
  }: {
    initialValue?: Value;
    children?: ReactNode;
  }) => (
    <StateContext.Provider value={useValue(initialValue)}>
      {children}
    </StateContext.Provider>
  );
  const useContextState = () => {
    const value = useContext(StateContext);
    if (value === null){ 
     throw new Error("Provider missing");
    }
    return value;
  };
  return [StateProvider, useContextState] as const;
};
const useNumberState = (init?: number) => useState(init || 0);
```

如果我们使用 `createStateContext` 和 `useNumberState` 的类型化版本，结果也将是类型化的。

## 使用 reduceRight 避免提供者嵌套

使用 `createStateContext` 函数，创建多个状态非常容易。假设我们创建了五个，如下所示：

```js
const [Count1Provider, useCount1] =
  createStateContext(useNumberState);
const [Count2Provider, useCount2] =
  createStateContext(useNumberState);
const [Count3Provider, useCount3] =
  createStateContext(useNumberState);
const [Count4Provider, useCount4] =
  createStateContext(useNumberState);
const [Count5Provider, useCount5] =
  createStateContext(useNumberState);
```

我们的 `App` 组件将看起来像这样：

```js
const App = () => (
  <Count1Provider initialValue={10}>
    <Count2Provider initialValue={20}>
      <Count3Provider initialValue={30}>
        <Count4Provider initialValue={40}>
          <Count5Provider initialValue={50}>
            <Parent />
          </Count5Provider>
        </Count4Provider>
      </Count3Provider>
    </Count2Provider>
  </Count1Provider>
);
```

这完全正确，并且捕捉到了组件树的结构。然而，过多的嵌套在编码时并不舒适。为了减轻这种编码风格，我们可以使用 `reduceRight`。`App` 组件可以被重构，如下面的示例所示：

```js
const App = () => {
  const providers = [
    [Count1Provider, { initialValue: 10 }],
    [Count2Provider, { initialValue: 20 }],
    [Count3Provider, { initialValue: 30 }],
    [Count4Provider, { initialValue: 40 }],
    [Count5Provider, { initialValue: 50 }],
  ] as const;
  return providers.reduceRight(
    (children, [Component, props]) =>
      createElement(Component, props, children),
    <Parent />,
  );
};
```

这种技术的变体可能包括创建一个 `reduceRight` 来构建提供者树。

这种技术不仅适用于使用 Context 的全局状态，也适用于任何组件。

在本节中，我们学习了一些与使用 Context 的全局状态相关的最佳实践。这些并不是你必须遵循的规则。只要你能理解 Context 的行为及其限制，任何模式都可以很好地工作。

# 摘要

在本章中，我们学习了如何使用 React Context 创建全局状态。Context 的传播旨在避免传递 props。如果你正确理解了 Context 的行为，使用 Context 实现全局状态将非常简单。基本上，我们应该为每个状态部分创建一个 Context，以避免额外的重新渲染。一些最佳实践将有助于使用 Context 实现全局状态，特别是 `createStateContext` 的具体实现，这将有助于组织你的应用代码。

在下一章中，我们将学习另一种使用订阅实现全局状态的模式。
