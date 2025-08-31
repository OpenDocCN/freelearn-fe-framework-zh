# 第六章：介绍全局状态库

我们已经学习了用于在组件间共享状态的几种模式。本书的剩余部分将介绍使用这些模式的各种全局状态库。

在深入探讨库之前，我们将回顾与全局状态相关的挑战，并讨论库的两个方面：状态存储的位置以及如何控制重新渲染。有了这些知识，我们将能够理解全局状态库的特点。

在本章中，我们将涵盖以下主题：

+   处理全局状态管理问题

+   使用以数据和组件为中心的方法

+   优化重新渲染

# 技术要求

预期您对 React 有一定的了解，包括 React 钩子。请参考官方网站[`reactjs.org`](https://reactjs.org)以获取更多信息。

要运行代码片段，您需要一个 React 环境，例如，Create React App ([`create-react-app.dev`](https://create-react-app.dev))或 CodeSandbox ([`codesandbox.io`](https://codesandbox.io))。

# 处理全局状态管理问题

React 的设计围绕组件的概念。在组件模型中，一切都被期望是可重用的。全局状态是存在于组件之外的东西。通常情况下，我们应该尽量避免使用全局状态，因为它需要一个额外的组件依赖。然而，全局状态有时非常方便，并允许我们更高效地工作。对于某些应用程序需求，全局状态非常适合。

设计全局状态时有两个挑战：

+   第一个挑战是如何读取全局状态。

    全局状态通常包含多个值。通常情况下，使用全局状态的组件不需要其中的所有值。如果一个组件在全局状态改变时重新渲染，但改变后的值与组件无关，这将是一个额外的渲染。额外的渲染是不希望的，全局状态库应该提供解决方案。避免额外渲染有几种方法，我们将在*优化重新渲染*部分更详细地讨论它们。

+   第二个挑战是如何编写或更新全局状态。

    再次强调，全局状态可能包含多个值，其中一些可能是嵌套对象。拥有一个单独的全局变量并接受任意的突变可能不是一个好主意。下面的代码块展示了全局变量和一个任意的突变示例：

    ```js
    let globalVariable = {
      a: 1,
      b: {
        c: 2,
        d: 3,
      },
      e: [4, 5, 6],
    };
    globalVariable.b.d = 9;
    ```

    示例中的突变`globalVariable.b.d = 9`可能对全局状态不起作用，因为没有方法可以检测变化并触发 React 组件重新渲染。

要更好地控制全局状态如何编写，我们通常会提供更新全局状态的函数。通常还需要在闭包中隐藏一个变量，以便不能直接修改该变量。下面的代码块展示了在闭包中创建用于读取和写入变量的两个函数的示例：

```js
const createContainer = () => {
  let state = { a: 1, b: 2 };
  const geState = () => state;
  const setState = (...) => { ...  };
  return { getState, setState };
};
const globalContainer = createContainer();
globalContainer.setState(...);
```

`createContainer` 函数创建了 `globalContainer`，它包含 `getState` 和 `setState` 函数。`getState` 是一个读取全局状态的函数，而 `setState` 是一个更新全局状态的函数。实现如 `setState` 这样的更新全局状态的函数有几种方式。我们将在接下来的章节中具体探讨。

全局状态管理与通用状态管理

本书专注于 *全局* 状态管理；*通用* 状态管理不在此书的范畴之内。在通用状态管理的领域，流行的方法包括像 Redux ([`redux.js.org`](https://redux.js.org)) 那样的单向数据流方法，以及像 XState ([`xstate.js.org`](https://xstate.js.org)) 那样的基于状态机的方法。通用状态管理方法不仅对全局状态有用，对局部状态也很有用。

关于 Redux 和 React Redux 的注意事项

Redux 在全局状态管理领域一直是一个重要角色。Redux 通过全局状态的单向数据流解决了状态管理问题。然而，Redux 本身与 React 没有关系。是 React Redux ([`react-redux.js.org`](https://react-redux.js.org)) 将 React 和 Redux 绑定在一起。虽然 Redux 本身没有避免额外重新渲染的能力或概念，但 React Redux 有这样的能力。

由于 Redux 和 React Redux 非常受欢迎，过去有些人过度使用它们。这主要是因为 React 16.3 之前缺乏 React Context，并且没有其他流行的选择。这样的人（错误地）主要使用 React Redux 来（遗留）Context，而不需要单向数据流。自从 React 16.3 引入 React Context 和 React 16.8 引入的 `useContext` 钩子以来，我们可以轻松解决避免 prop 传递和额外重新渲染的用例。这使我们转向了微状态管理——本书的重点。

因此，从技术角度讲，React Redux 减去 Redux 就属于本书的范畴。Redux 本身是一个优秀的通用状态管理解决方案，与 React Redux 结合使用，它解决了本节讨论的全局状态问题。

在本节中，我们讨论了全局状态库的一般挑战。接下来，我们将学习状态驻留的位置。

# 使用以数据为中心和以组件为中心的方法

从技术上来说，全局状态可以分为两种类型：以数据为中心和以组件为中心。

在接下来的章节中，我们将详细讨论这两种方法。然后，我们还将讨论一些例外情况。

## 理解以数据为中心的方法

当你设计一个应用时，你可能在应用中有一个作为单例的数据模型，并且你可能已经有了处理数据。在这种情况下，你会定义组件并将数据与组件连接起来。数据可以从外部更改，例如由其他库或其他服务器。

对于以数据为中心的方法，模块状态会更为合适，因为模块状态位于 React 之外的 JavaScript 内存中。模块状态可以在 React 开始渲染之前存在，甚至在所有 React 组件卸载之后。

使用数据为中心的方法的全局状态库将提供 API 来创建模块状态并将模块状态连接到 React 组件。模块状态通常被封装在一个`store`对象中，该对象有访问和更新`state`变量的方法。

## 理解以组件为中心的方法

与数据为中心的方法不同，使用以组件为中心的方法，你可以首先设计组件。在某个时刻，某些组件可能需要访问共享信息。正如我们在*第二章*中的*有效使用本地状态*部分所讨论的，*使用本地和全局状态*，我们可以提升状态并通过 props（即属性钻取）向下传递。如果属性钻取不能作为解决方案，那么我们就可以引入全局状态。当然，我们可以先设计数据模型，但在以组件为中心的方法中，数据模型与组件紧密相关。

对于以组件为中心的方法，组件状态，它在组件生命周期中持有全局状态，更为合适。这是因为当所有相应的组件都卸载时，全局状态也随之消失。这种能力使我们能够在 JavaScript 内存中有两个或更多全局状态存在，因为它们位于不同的组件子树（或不同的 portals）中。

使用数据为中心的方法的全局状态库提供了一个工厂函数来创建初始化全局状态以在 React 组件中使用的方法。工厂函数并不直接创建全局状态，但通过使用生成的函数，我们让 React 处理全局状态的生命周期。

## 探索两种方法的例外情况

我们所描述的是典型的用例，但总会有一些例外。以数据为中心的方法和以组件为中心的方法并不是同一枚硬币的两面。实际上，你可以使用这两种方法中的一种，或者两种方法的混合。

模块状态通常被用作单例模式，但你可以为子树创建多个模块状态。你甚至可以控制它们的生命周期。

组件状态通常用于在子树中提供状态，但如果你在树的根处放置提供者组件，并且 JavaScript 内存中只有一个树，它可以被看作是单例模式。

组件状态通常使用`useState`钩子实现，但如果我们需要一个可变的变量或`store`，可以使用`useRef`钩子实现。这种实现可能比使用`useState`更复杂，但它仍然属于组件生命周期的一部分。

在本节中，我们学习了两种使用全局状态的方法。模块状态主要用于数据为中心的方法，而组件状态主要用于组件为中心的方法。接下来，我们将学习几种优化重新渲染的模式。

# 优化重新渲染

避免额外的重新渲染是全局状态时的一个主要挑战。在设计 React 的全局状态库时，这是一个需要考虑的重要点。

通常，全局状态有多个属性，它们可以是嵌套对象。以下是一个例子：

```js
let state = {
  a: 1,
  b: { c: 2, d: 3 },
  e: { f: 4, g: 5 },
};
```

使用这个`state`对象，假设有两个组件`ComponentA`和`ComponentB`，分别使用`state.b.c`和`state.e.g`。以下是两个组件的伪代码：

```js
const ComponentA = () => {
  return <>value: {state.b.c}</>;
};
const ComponentB = () => {
  return <>value: {state.e.g}</>;
};
```

现在，让我们假设我们按照以下方式更改`state`：

```js
++state.a;
```

这会改变`state`的`a`属性，但不会改变`state.b.c`或`state.e.g`。在这种情况下，两个组件不需要重新渲染。

优化重新渲染的目标是指定组件中使用了`state`的哪个部分。我们有几种方法来指定`state`的部分。本节描述了三种方法：

+   使用选择器函数

+   检测属性访问

+   使用原子

我们现在将讨论这些内容。

## 使用选择器函数

一种方法是使用选择器函数。选择器函数接受一个`state`变量并返回`state`变量的一部分。

例如，让我们假设我们有一个`useSelector`钩子，它接受一个选择器函数并返回`state`的一部分：

```js
const Component = () => {
  const value = useSelector((state) => state.b.c);
  return <>{value}</>;
};
```

如果`state.b.c`是`2`，那么`Component`将显示`2`。既然我们知道这个组件只关心`state.b.c`，我们就可以在`state.a`改变时避免额外的重新渲染。

`useSelector`将在每次`state`改变时比较选择器函数的结果。因此，当给定相同的输入时，选择器函数返回的引用相等的结果非常重要。

选择器函数非常灵活，不仅可以返回`state`的一部分，还可以返回任何派生值。例如，它可以返回一个双倍值，如下所示：

```js
const Component = () => {
  const value = useSelector((state) => state.b.c * 2);
  return <>{value}</>;
};
```

关于选择器和记忆化的注意事项

如果选择器函数返回的值是原始值，如数字，则没有问题。然而，如果选择器函数返回一个派生对象值，我们需要确保使用所谓的记忆化技术返回一个引用相等的对象。你可以在[`en.wikipedia.org/wiki/Memoization`](https://en.wikipedia.org/wiki/Memoization)上了解更多关于记忆化的信息。

由于选择器函数是一种显式指定组件将使用哪一部分的手段，我们将其称为手动优化。

## 检测属性访问

我们能否在不使用选择器函数显式指定组件中要使用哪个状态部分的情况下自动进行渲染优化？有一种称为状态使用跟踪的技术，用于检测属性访问并使用检测到的信息进行渲染优化。

例如，假设我们有一个具有状态使用跟踪能力的`useTrackedState`钩子：

```js
const Component = () => {
  const trackedState = useTrackedState();
  return <p>{trackedState.b.c}</p>;
};
```

这是因为`trackedState`可以检测到`.b.c`属性被访问，而`useTrackedState`只有在`.b.c`属性值发生变化时才会触发重新渲染。这是自动渲染优化，而`useSelector`是手动渲染优化。

为了简化，之前的代码块示例是人为设计的。这个例子可以很容易地通过使用`useSelector`和手动渲染优化来实现。让我们看看另一个使用两个值的例子：

```js
const Component = () => {
  const trackedState = useTrackedState();
  return (
    <>
      <p>{trackedState.b.c}</p>
      <p>{trackedState.e.g}</p>
    </>
  );
};
```

现在令人惊讶的是，使用单个`useSelector`钩子来实现这一点非常困难。如果我们编写一个选择器，它将需要记忆化或自定义相等函数，这些是复杂的技术。然而，如果我们使用`useTrackedState`，它无需这些复杂技术就能工作。

`useTrackedState`的实现需要使用代理([`developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy))来拦截对`state`对象的属性访问。如果正确实现，它可以替代大多数`useSelector`的使用场景，并可以进行自动渲染优化。然而，存在一个微妙的情况，自动渲染优化并不完美。让我们在下一节中更详细地探讨。

### `useSelector`和`useTrackedState`的区别

有一些使用场景中，`useSelector`比`useTrackedState`表现更好。因为`useSelector`可以创建任何派生值，它可以派生更简单的状态值。

通过一个简单的例子，我们可以看到`useSelector`和`useTrackedState`的工作方式之间的区别。以下是一个使用`useSelector`的示例组件：

```js
const Component = () => {
  const isSmall = useSelector((state) => state.a < 10);
  return <>{isSmall ? 'small' : 'big'}</>;
};
```

如果我们用`useTrackedState`创建相同的组件，它将是以下内容：

```js
const Component = () => {
  const isSmall = useTrackedState().a < 10;
  return <>{isSmall ? 'small' : 'big'}</>;
};
```

从功能上讲，这个带有`useTrackedState`的组件工作得很好，但它会在每次`state.a`发生变化时触发重新渲染。相反，使用`useSelector`时，只有在`isSmall`发生变化时才会触发重新渲染，这意味着它具有更好的渲染优化。

## 使用原子

另一种方法，我们称之为使用原子。原子是用于触发重新渲染的最小状态单元。与订阅整个全局状态并尝试避免额外重新渲染不同，原子允许你进行粒度更细的订阅。

例如，假设我们有一个只订阅原子的`useAtom`钩子。一个`atom`函数会创建这样的单元（即`atom`）的`state`对象：

```js
const globalState = {
  a: atom(1),
  b: atom(2),
  e: atom(3),
};
const Component = () => {
  const value = useAtom(globalState.a);
  return <>{value}</>;
};
```

如果原子完全分离，几乎等同于拥有单独的全局状态。然而，我们可以使用原子创建派生值。例如，假设我们想要对`globalState`值求和。伪代码如下：

```js
const sum = globalState.a + globalState.b + globalState.c;
```

要使这可行，我们需要跟踪依赖关系，并在依赖原子更新时重新评估派生值。我们将仔细研究如何在*第八章*，“用例场景 2 – Jotai”中实现这样的 API。

使用原子的方法可以看作是手动方法和自动方法之间的某种折中。虽然原子和派生值的定义是明确的（手动），但依赖跟踪是自动的。

在本节中，我们学习了优化重新渲染的各种模式。对于全局状态库来说，设计如何优化重新渲染是很重要的。这通常会影响库的 API，理解如何优化重新渲染对于库用户来说也是值得的。

# 摘要

在本章中，在深入探讨全局状态库的实际实现之前，我们了解了一些与之相关的基本挑战，以及一些用于区分全局状态库的分类。在选择全局状态库时，我们可以看到库如何让我们读取全局状态和写入全局状态，库存储全局状态的位置，以及库如何优化重新渲染。这些是理解哪些库适用于特定用例的重要方面，它们应该有助于你选择适合你需求的库。

在下一章中，我们将学习关于 Zustand 库的内容，这是一个采用数据为中心的方法并使用选择器函数优化重新渲染的库。
