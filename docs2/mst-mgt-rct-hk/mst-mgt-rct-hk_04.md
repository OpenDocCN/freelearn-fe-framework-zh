# 第二章：*第二章*：使用局部和全局状态

React 组件形成一个树结构。在树结构中，在整个子树中创建状态是直接的；您只需在树中的较高组件中创建一个局部状态，并在该组件及其子组件中使用该状态。这在局部性和可重用性方面是好的，这也是为什么通常推荐遵循这种策略的原因。

然而，在某些场景中，我们在树中相隔甚远的两个或多个组件中有一个状态。在这种情况下，这就是全局状态发挥作用的地方。与局部状态不同，全局状态在概念上不属于特定的组件，因此我们存储全局状态是一个需要考虑的重要点。

在本章中，我们将学习关于局部状态的知识，包括一些值得考虑的提升模式。提升是一种将信息放在组件树更高的位置的技巧。然后，我们将深入研究全局状态，并考虑何时使用它们。

我们将涵盖以下主题：

+   理解何时使用局部状态

+   有效使用局部状态

+   使用全局状态

# 技术要求

要运行本章中的代码片段，您需要一个 React 环境——例如，Create React App ([`create-react-app.dev`](https://create-react-app.dev)) 或 CodeSandbox ([`codesandbox.io`](https://codesandbox.io))。

预期您对 React 和 React hooks 有基本了解，特别是关于组件树 ([`reactjs.org/docs/components-and-props.html`](https://reactjs.org/docs/components-and-props.html)) 和 `useState` 钩子 ([`reactjs.org/docs/hooks-reference.html#usestate`](https://reactjs.org/docs/hooks-reference.html#usestate)) 的概念。

本章中的代码可在 GitHub 上找到，地址为 [`github.com/PacktPublishing/Micro-State-Management-with-React-Hooks/tree/main/chapter_02`](https://github.com/PacktPublishing/Micro-State-Management-with-React-Hooks/tree/main/chapter_02)。

# 理解何时使用局部状态

在我们考虑 React 之前，让我们看看 JavaScript 函数是如何工作的。JavaScript 函数可以是纯函数或不纯函数。纯函数只依赖于其参数，只要参数相同，就返回相同的值。状态持有参数之外的价值，依赖于状态的函数变为不纯。React 组件也是函数，可以是纯函数。如果我们在一个 React 组件中使用状态，它将是不纯的。然而，如果状态是组件本地的，它不会影响其他组件，我们称这种特性为“封装”。

在本节中，我们将学习 JavaScript 函数，以及 React 组件与 JavaScript 函数的相似之处。然后，我们将讨论局部状态的概念实现方式。

## 函数和参数

在 JavaScript 中，一个函数接受一个参数并返回一个值。例如，这里有一个简单的函数：

```js
const addOne = (n) => n + 1;
```

这是一个总是为相同的参数返回相同值的纯函数。通常情况下，纯函数更受欢迎，因为它们的行为是可预测的。

函数可以依赖于全局变量，如下所示：

```js
let base = 1;
const addBase = (n) => n + base;
```

只要`base`没有改变，`addBase`函数的工作方式与`addOne`完全相同。然而，如果在某个时刻我们将`base`改为`base=2`，它的行为就会不同。这根本不是一件坏事，实际上这是一个强大的特性，因为你可以从外部改变函数的行为。缺点是，如果你不知道它依赖于外部变量，就不能简单地抓取`addBase`函数并在其他地方随意使用。正如你所看到的，这是一个权衡。

如果`base`是一个单例（内存中的单个值），则这不是一个首选模式，因为代码的可重用性会降低。为了避免单例并稍微减轻其缺点，可以采用更模块化的方法来创建一个容器对象，如下所示：

```js
const createContainer = () => {
  let base = 1;
  const addBase = (n) => n + base;
  const changeBase = (b) => { base = b; };
  return { addBase, changeBase };
};
const { addBase, changeBase } = createContainer();
```

这不再是一个单例，你可以创建任意数量的容器。与使用作为单例的`base`全局变量不同，容器是隔离的，并且更易于重用。你可以在代码的一部分使用一个容器，而不会影响到使用不同容器的代码的其他部分。

简短说明：尽管容器中的`addBase`不是一个数学上纯函数，但如果`base`没有改变，你可以通过调用`addBase`得到相同的结果（这个特性有时被称为**幂等**）。

## React 组件和 props

React 在概念上是一个将状态转换为**用户界面**（**UI**）的函数。当你用 React 编码时，React 组件实际上是一个 JavaScript 函数，其参数被称为 props。

显示数字的函数组件看起来如下：

```js
const Component = ({ number }) => {
  return <div>{number}</div>;
};
```

此组件接受一个`number`参数，并在屏幕上返回一个`number`。

什么是 JSX 元素？

JSX 是一种带有尖括号的语法，用于生成 React 元素。React 元素是一个数据结构，用于表示 UI 的一部分。我们可能将 React 元素称为 JSX 元素，尤其是在 React 元素使用 JSX 语法时。

现在，让我们创建另一个组件，它显示`number + 1`，如下所示：

```js
const AddOne = ({ number }) => {
  return <div>{number + 1}</div>;
};
```

此组件接受`number`并返回`number + 1`。这与上一节中的`addOne`行为完全相同，这是一个纯函数。唯一的区别是参数是一个 props 对象，返回值是 JSX 格式。

## 理解 useState 用于局部状态

如果我们使用`useState`来处理局部状态会怎样？让我们将`base`设为状态，并显示一个可以添加到其中的`number`，如下所示：

```js
const AddBase = ({ number }) => {
  const [base, changeBase] = useState(1);
  return <div>{number + base}</div>;
};
```

此函数在技术上不是纯函数，因为它依赖于`base`，而`base`不在函数参数中。

`AddBase` 中的 `useState` 做了什么？让我们回顾一下上一节中的 `createContainer`。由于 `createContainer` 返回 `base` 和 `changeBase`，`useState` 返回一个元组（意味着两个或更多值的结构——在这种情况下，两个）。我们在这个代码中并没有明确看到 `base` 和 `changeBase` 是如何创建的，但从概念上讲是相似的。

如果我们假设 `useState` 的行为，即除非更改，否则返回 `base`，那么 `AddBase` 函数是幂等的，就像我们看到的 `createContainer` 一样。

这个使用 `useState` 的 `AddBase` 函数被包含在内，因为 `changeBase` 只在函数声明的范围内可用。在函数外部无法更改 `base`。这种 `useState` 的用法是本地状态，因为它被包含并且不影响组件外部的内容，这确保了局部性；在适当的时候，这种用法是首选的。

## 本地状态的限制

何时本地状态不合适？当我们想要打破局部性时，它就不合适。在 `AddBase` 组件示例中，这是当我们想要从完全不同的代码部分更改 `base` 时。如果你需要从函数组件外部更改 `state`，那么全局状态就出现了。

状态变量在概念上是一个全局变量。全局变量对于从函数外部控制 JavaScript 函数的行为是有用的。同样，全局状态对于从组件外部控制 React 组件的行为也是有用的。然而，使用全局状态会使组件行为变得不那么可预测。这是一个权衡。我们不应该比需要的时候更多地使用全局状态。考虑将本地状态作为主要手段，并且仅将全局状态作为次要手段。在这种情况下，了解本地状态可以覆盖多少使用案例是很重要的。

在本节中，我们学习了 React 中的本地状态，以及 JavaScript 函数。接下来，我们将学习一些使用本地状态的模式。

# 有效使用本地状态

为了能够有效地使用本地状态，你应该知道一些模式。在本节中，我们将学习如何提升状态，这意味着在组件树中定义一个更高的状态，以及提升内容，这意味着在组件树中定义一个更高的内容。

## 提升状态

假设我们有两个计数器组件，如下所示：

```js
const Component1 = () => {
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
const Component2 = () => {
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

由于在两个组件中定义了两个单独的本地状态，这两个计数器是独立工作的。如果我们想共享状态并使其为一个共享计数器工作，我们可以创建一个父组件并将状态提升上去。

下面是一个包含 `Component1` 和 `Component2` 作为子组件的单个父组件示例，并将属性传递给它们：

```js
const Component1 = ({ count, setCount }) => {
  return (
    <div>
      {count}
      <button onClick={() => setCount((c) => c + 1)}>
        Increment Count
      </button>
    </div>
  );
};
const Component2 = ({ count, setCount }) => {
  return (
    <div>
      {count}
      <button onClick={() => setCount((c) => c + 1)}>
        Increment Count
      </button>
    </div>
  );
};
const Parent = () => {
  const [count, setCount] = useState(0);
  return (
    <>
      <Component1 count={count} setCount={setCount} />
      <Component2 count={count} setCount={setCount} />
    </>
  );
};
```

由于计数状态在 `Parent` 中只定义了一次，因此状态在 `Component1` 和 `Component2` 之间是共享的。这仍然是在组件中的本地状态；其子组件可以使用父组件的状态。

这种模式在大多数使用局部状态的场景中都会有效；然而，有一点关于性能的担忧。如果我们提升状态，`Parent` 将会重新渲染，以及整个子树，包括所有子组件。在某些用例中，这可能会成为性能问题。

## 提升内容向上

在复杂的组件树中，我们可能有一个不依赖于我们提升的状态的组件。

在以下示例中，我们向前面示例中的 `Component1` 添加一个新的 `AdditionalInfo` 组件：

```js
const AdditionalInfo = () => {
  return <p>Some information</p>
};
const Component1 = ({ count, setCount }) => {
  return (
    <div>
      {count}
      <button onClick={() => setCount((c) => c + 1)}>
        Increment Count
      </button>
      <AdditionalInfo />
    </div>
  );
};
const Parent = () => {
  const [count, setCount] = useState(0);
  return (
    <>
      <Component1 count={count} setCount={setCount} />
      <Component2 count={count} setCount={setCount} />
    </>
  );
};
```

如果计数发生变化，`Parent` 会重新渲染，然后 `Component1`、`Component2` 和 `AdditionalInfo` 也会重新渲染。然而，在这种情况下，`AdditionalInfo` 不必重新渲染，因为它不依赖于 `count`。这是一个额外的重新渲染，如果它对性能有影响，应该避免。

为了避免额外的重新渲染，我们可以提升内容。在这种情况下，`Parent` 组件会随着 `count` 的变化而重新渲染，因此，我们创建 `GrandParent`，如下所示：

```js
const AdditionalInfo = () => {
  return <p>Some information</p>
};
const Component1 = ({ count, setCount, additionalInfo }) => {
  return (
    <div>
      {count}
      <button onClick={() => setCount((c) => c + 1)}>
        Increment Count
      </button>
      {additionalInfo}
    </div>
  );
};
const Parent = ({ additionalInfo }) => {
  const [count, setCount] = useState(0);
  return (
    <>
      <Component1
        count={count}
        setCount={setCount}
        additionalInfo={additionalInfo}
      />
      <Component2 count={count} setCount={setCount} />
    </>
  );
};
const GrandParent = () => {
  return <Parent additionalInfo={<AdditionalInfo />} />;
};
```

`GrandParent` 组件有 `additionalInfo`（一个 JSX 元素），它被传递给子组件。通过这样做，当 `count` 变化时，`AdditionalInfo` 不会重新渲染。这是一种我们不仅应该考虑性能，还应该考虑组织组件树结构的技巧。

这种方法的变体是使用 `children` 属性。以下使用 `children` 属性的示例与前面的示例等效，但具有不同的编码风格：

```js
const AdditionalInfo = () => {
  return <p>Some information</p>
};
const Component1 = ({ count, setCount, children }) => {
  return (
    <div>
      {count}
      <button onClick={() => setCount((c) => c + 1)}>
        Increment Count
      </button>
      {children}
    </div>
  );
};
const Parent = ({ children }) => {
  const [count, setCount] = useState(0);
  return (
    <>
      <Component1 count={count} setCount={setCount}>
        {children}
      </Component1>
      <Component2 count={count} setCount={setCount} />
    </>
  );
};
const GrandParent = () => {
  return (
    <Parent>
      <AdditionalInfo />
    </Parent>
  );
};
```

`children` 是一个特殊的属性名，在 JSX 格式中表示为嵌套的子元素。如果你有多个元素要传递，给属性命名会更好。这主要是一个风格选择，开发者可以采取他们喜欢的任何方法。

在本节中，我们学习了如何有效地使用局部状态的一些模式。如果我们正确地提升状态和内容，我们应该能够仅使用局部状态解决各种用例。接下来，我们将学习如何使用全局状态。

# 使用全局状态

在本节中，我们将再次学习什么是全局状态以及何时应该使用它。

## 什么是全局状态？

在这本书中，全局状态仅仅意味着它**不是**局部状态。如果一个状态在概念上属于单个组件并且被该组件封装，那么它是一个局部状态。因此，如果一个状态不属于单个组件并且可以被多个组件使用，那么它是一个全局状态。

可能存在一个应用范围内的局部状态，所有组件都依赖于它。在这种情况下，应用范围内的局部状态可以被视为全局状态。从这个意义上说，我们无法清楚地划分局部状态和全局状态。在大多数情况下，如果你考虑状态在概念上属于哪里，你可以确定它是局部还是全局。

当人们谈论全局状态时，有两个方面，如下所述：

+   其中一个是单例，意味着在某些上下文中，状态只有一个值。

+   另一个是共享状态，这意味着状态值在多个组件之间共享，但不必是 JavaScript 内存中的单个值。非单例的全局状态可以有多个值。

为了说明非单例全局状态的工作原理，这里有一个例子来展示 JavaScript 中的非单例变量：

```js
const createContainer = () => {
  let base = 1;
  const addBase = (n) => n + base;
  const changeBase = (b) => { base = b; };
  return { addBase, changeBase };
};
const container1 = createContainer();
const container2 = createContainer();
container1.changeBase(10);
console.log(container1.addBase(2)); // shows "3"
console.log(container2.addBase(2)); // shows "12"
```

在这个例子中，`base`是一个容器中的局部变量。由于`base`在每个容器中都是隔离的，所以在`container1`中更改`base`不会影响`container2`中的`base`。

在 React 中，概念是相似的。如果全局状态是一个单例，我们在内存中只有一个值。如果全局状态不是单例，我们可能为组件树的不同部分（子树）有多个值。

## 何时使用全局状态

当我们需要在 React 中使用全局状态时，有两个指导原则，如下：

+   当传递属性不希望时

+   当我们已经在 React 外部有一个状态时

让我们讨论每一个。

### 属性传递是不希望的

如果你需要在组件树中相距较远的两个组件中使用状态，在公共根组件中放置状态并将状态传递到这两个组件可能不是最佳选择。

例如，如果我们的树有三层深，并且需要将状态提升到顶部，它看起来会是这样：

```js
const Component1 = ({ count, setCount }) => {
  return (
    <div>
      {count}
      <button onClick={() => setCount((c) => c + 1)}>
        Increment Count
      </button>
    </div>
  );
};
const Parent = ({ count, setCount }) => {
  return (
    <>
      <Component1 count={count} setCount={setCount} />
    </>
  );
};
const GrandParent = ({ count, setCount }) => {
  return (
    <>
      <Parent count={count} setCount={setCount} />
    </>
  );
};
const Root = () => {
  const [count, setCount] = useState(0);
  return (
    <>
      <GrandParent count={count} setCount={setCount} />
    </>
  );
};
```

这完全没问题，并且推荐用于局部性；然而，让你的中间组件用于传递属性可能会变得过于繁琐。通过多层中间组件传递属性可能不会带来良好的开发者体验，因为这可能看起来像是额外的不必要工作。此外，当状态更新时，中间组件会重新渲染，这可能会影响性能。

在这种情况下，拥有全局状态更为合适，并且不需要中间组件处理状态传递。

这里有一些伪代码展示了如何使用上一个例子中的全局状态：

```js
const Component1 = () => {
  // useGlobalCountState is a pseudo hook 
  const [count, setCount] = useGlobalCountState();
  return (
    <div>
      {count}
      <button onClick={() => setCount((c) => c + 1)}>
        Increment Count
      </button>
    </div>
  );
};
const Parent = () => {
  return (
    <>
      <Component1 />
    </>
  );
};
const GrandParent = () => {
  return (
    <>
      <Parent />
    </>
  );
};
const Root = () => {
  return (
    <>
      <GrandParent />
    </>
  );
};
```

在这个例子中，唯一使用全局状态的组件是`Component1`。与局部状态和属性传递不同，中间组件`Parent`和`GrandParent`并不知道全局状态。

### 已经在 React 外部有一个状态

在某些情况下，你已经在 React 外部有一个全局状态，因为在外部拥有全局状态更直接。例如，你的应用可能有一些通过某种方式获得的用户认证信息，而不是通过 React。在这种情况下，全局状态应该存在于 React 外部，认证信息可以存储在全局状态中。

这里有一些伪代码展示了这样的例子：

```js
const globalState = {
  authInfo: { name: 'React' },
};
const Component1 = () => {
  // useGlobalState is a pseudo hook
  const { authInfo } = useGlobalState();
  return (
    <div>
      {authInfo.name}
    </div>
  );
};
```

在这个例子中，`globalState`存在并且是在 React 外部定义的。`useGlobalState`是一个钩子，它会连接到`globalState`，并且可以在`Component1`中提供`authInfo`。

在本节中，我们了解到全局状态是一种不能是局部状态的状态。全局状态主要作为局部状态的补充使用，并且有两种情况下使用全局状态效果良好：一种是在属性传递没有意义的情况下，另一种是在应用中已经存在全局状态的情况下。

# 摘要

在本章中，我们讨论了局部状态和全局状态。尽可能情况下，局部状态是首选的，并且我们学习了一些有效使用局部状态的技术。然而，全局状态在局部状态无法发挥作用的地方扮演着角色，这就是为什么我们要探讨何时应该使用全局状态而不是局部状态。

在接下来的三章中，我们将学习三种在 React 中实现全局状态的模式；在下一章中，我们将具体从利用 React 上下文开始。
