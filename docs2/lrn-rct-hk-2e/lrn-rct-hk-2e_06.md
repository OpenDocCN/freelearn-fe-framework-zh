# 4

# 使用 Reducer 和 Effect Hooks

在使用 State Hook 开发我们自己的博客应用程序之后，我们现在将学习 React 提供的另外两个非常重要的 Hooks——**Reducer** 和 **Effect** Hooks。我们首先将学习何时应该使用 Reducer Hook 而不是 State Hook。然后，我们将学习如何将现有的 State Hook 转换为 Reducer Hook，以便在实践中掌握这个概念。最后，我们将学习 Effect Hooks 的用途以及如何在我们的博客应用程序中实现它们。

本章将涵盖以下主题：

+   Reducer Hooks 与 State Hooks 的比较

+   使用 Reducer Hooks

+   使用 Effect Hooks

# 技术要求

应该已经安装了相当新的 Node.js 版本。Node 包管理器 (`npm`) 也需要安装（它应该与 Node.js 一起提供）。有关如何安装 Node.js 的更多信息，请查看他们的官方网站：[`nodejs.org/`](https://nodejs.org/)。

在这本书的指南中，我们将使用 **Visual Studio Code** （**VS Code**），但在任何其他编辑器中都应该以类似的方式工作。有关如何安装 VS Code 的更多信息，请参阅他们的官方网站：[`code.visualstudio.com`](https://code.visualstudio.com)。

在这本书中，我们使用以下版本：

+   Node.js v22.14.0

+   npm v10.9.2

+   Visual Studio Code v1.97.2

虽然安装新版本不应该有问题，但请注意，某些步骤在较新版本上可能会有所不同。如果您在这本书提供的代码和步骤中遇到问题，请尝试使用提到的版本。

您可以在 GitHub 上找到本章的代码：[`github.com/PacktPublishing/Learn-React-Hooks-Second-Edition/tree/main/Chapter04`](https://github.com/PacktPublishing/Learn-React-Hooks-Second-Edition/tree/main/Chapter04)。

强烈建议您自己编写代码。不要简单地运行书中提供的代码示例。自己编写代码对于正确学习和理解代码非常重要。然而，如果您遇到任何问题，您始终可以参考代码示例。

# Reducer Hooks 与 State Hooks 的比较

在上一章中，我们学习了处理局部和全局状态。我们在这两种情况下都使用了 State Hooks，这对于简单的状态更改来说是可行的。然而，如果我们的状态逻辑变得更加复杂，我们需要确保保持状态的一致性。为此，我们应该使用 Reducer Hook，而不是多个 State Hooks，因为维护多个相互依赖的 State Hooks 之间的同步性更困难。作为替代方案，我们可以将所有状态保存在一个 State Hook 中，但这时我们必须确保不会意外地覆盖我们的状态的一部分。

## State Hook 的局限性

状态钩子已经支持传递复杂对象和数组给它，并且它可以完美地处理它们的状态变化。然而，我们始终需要直接更改状态，这意味着我们需要使用大量的解构来确保我们没有覆盖状态的其他部分。例如，想象我们有一个如下所示的状态钩子：

```js
const [config, setConfig] = useState({
  filter: 'all',
  expandPosts: true,
}) 
```

现在，假设我们想按照以下方式更改过滤器：

```js
setConfig({
  filter: {
    author: 'Daniel Bugl',
    fromDate: '2024-10-02',
  },
}) 
```

如果我们只是这样做，我们就会从状态对象中删除`expandPosts`设置！因此，我们需要使用扩展运算符，如下所示：

```js
setConfig(**config** **=>** **(**{
 **...config,**
filter: {
    author: 'Daniel Bugl',
    fromDate: '2024-10-02',
  }
}**)**) 
```

现在，如果我们想将`fromDate`过滤器更改为不同的日期，我们需要使用两次扩展运算符，以避免意外删除`author`过滤器：

```js
setConfig(config => ({
  ...config,
  filter: {
 **...config.**filter,
    fromDate: '2024-10-03',
  }
})) 
```

但如果我们这样做时`filter`状态仍然是字符串，就像原始对象中那样（`filter: 'all'`），会发生什么？我们将得到以下结果：

```js
{
  filter: {
    '0': 'a',
    '1': 'l',
    '2': 'l',
    fromDate: '2024-10-03',
  },
  expandPosts: true
} 
```

什么？为什么突然出现了三个新的键——`'0'`、`'1'`和`'2'`？答案是，扩展运算符也适用于字符串，字符串以这种方式展开，每个字母都根据其在字符串中的索引获得一个键。

如您所想象，使用扩展运算符和直接改变状态对象对于较大的状态对象来说可能会变得非常繁琐。此外，我们始终需要确保不会引入任何错误，并且我们需要在我们的应用程序的多个地方检查错误。

## Reducers

我们不是直接改变状态，而是可以创建一个处理状态变化的函数。这样的函数被称为**reducer**，它的工作方式如下：

```js
const newState = reducer(currentState, action) 
```

如您所见，我们不是直接改变状态对象，而是调用一个函数，该函数接受当前状态和动作对象，并返回一个新的状态对象。在我们定义函数之前，让我们首先更详细地看看动作。

## Actions

**动作**是具有包含动作名称的`type`属性的对象，并且可以包含有关动作的一些附加信息。

让我们回顾一下之前的状态对象：

```js
{
  filter: 'all',
  expandPosts: true,
} 
```

如果我们想改变`expandPosts`状态，我们会使用`TOGGLE_EXPAND`动作，这个动作不需要任何额外的信息。动作将如下所示：

```js
{ type: 'TOGGLE_EXPAND' } 
```

如果我们想改变过滤器，我们会使用`CHANGE_FILTER`动作，该动作还包含有关应更改的过滤器的信息。例如，我们可以使用以下动作以不同的方式更改过滤器：

```js
{ type: 'CHANGE_FILTER', all: true }
{ type: 'CHANGE_FILTER', fromDate: '2024-10-02' }
{ type: 'CHANGE_FILTER', author: 'Daniel' }
{ type: 'CHANGE_FILTER', fromDate: '2024-10-03' } 
```

第二、第三和第四个动作会将`filter`状态从字符串更改为对象，并设置相应的键。如果对象已经存在，它将简单地调整动作中定义的键。在这些动作之后，状态将按以下方式改变：

```js
{ filter: 'all' }
{ filter: { fromDate: '2024-10-02' } }
{ filter: { fromDate: '2024-10-02', author: 'Daniel' } }
{ filter: { fromDate: '2024-10-03', author: 'Daniel' } } 
```

现在，让我们假设我们应用了以下动作：

```js
{ type: 'CHANGE_FILTER' all: true } 
```

在这个动作之后，过滤器将回到初始状态的`'all'`字符串。

如果你之前使用过 Redux 库，你将已经熟悉状态、动作和 reducer 的概念。

## 定义 reducer

我们定义的动作的 reducer 函数可能看起来如下：

```js
function reducer(state, action) {
  switch (action.type) { 
```

在这里，我们定义了函数，并根据`action.type`决定对状态进行什么操作。首先，我们处理`TOGGLE_EXPAND`动作：

```js
 case 'TOGGLE_EXPAND':
      return { ...state, expandPosts: !state.expandPosts } 
```

现在，处理`CHANGE_FILTER`函数，其中如果动作定义了`all: true`过滤器，我们将过滤器重置为字符串`'all'`：

```js
 if (action.all) {
        return { ...state, filter: 'all' }
      } 
```

如果过滤器仍然是一个字符串，我们初始化一个空对象；否则，我们重用现有的对象：

```js
 let filter = typeof state.filter === 'string' ? {} : state.filter 
```

现在我们可以根据动作中定义的设置`fromDate`和`author`过滤器：

```js
 if (action.fromDate) {
        filter.fromDate = action.fromDate
      }
      if (action.author) {
        filter.author = action.author
      } 
```

最后，状态被返回为新的过滤器：

```js
 return { ...state, filter }
    } 
```

如果动作类型未知，我们将抛出一个错误：

```js
 default:
      throw new Error('unknown action')
  }
} 
```

在默认情况下抛出错误与我们在 Redux reducer 中做的不同，在 Redux reducer 中，我们会在默认情况下简单地返回当前状态。React Reducer Hooks 不会在单个全局对象中存储所有状态，我们只为某些状态对象处理某些动作，因此我们可以为未知动作类型抛出错误。

当我们仍在 reducer 函数中使用一些扩展运算符时，它并不像之前那样深层嵌套。此外，所有状态处理都在一个地方，我们通过动作一次只更改状态的一部分，这使得代码更容易维护且更少出错。

## Reducer Hook

现在我们有了 reducer 函数，我们只需要定义一个初始状态：

```js
const initialState = { filter: 'all' } 
```

使用 reducer 函数和初始状态，我们可以创建一个 Reducer Hook：

```js
const [state, dispatch] = useReducer(reducer, initialState) 
```

现在可以通过从 Hook 返回的`state`对象访问当前状态。使用`dispatch`函数允许我们调用传递给 Reducer Hook 的 reducer 函数。动作可以通过`dispatch`函数**分发**。例如：

```js
dispatch({ type: 'TOGGLE_EXPAND' }) 
```

分发一个动作将调用 reducer 函数，并带上当前状态和分发的动作，并将返回的状态设置为 Reducer Hook 的新状态。

如果我们想在动作中添加额外的信息，我们只需将其添加到对象中：

```js
dispatch({ type: 'CHANGE_FILTER', fromDate: '2024-10-03' }) 
```

如我们所见，使用动作和 reducer 处理状态变化比直接调整状态对象更容易阅读和维护。

现在我们已经了解了 Reducer Hooks 以及何时使用它们而不是 State Hooks，让我们开始使用它们。

# 使用 Reducer Hooks

在了解动作、reducer 和 Reducer Hook 之后，我们将在我们的博客应用中使用它。任何现有的 State Hook 都可以在状态或状态变化变得过于复杂时转换为 Reducer Hook。

如果有多个`setState`函数总是在同一时间被调用，这是一个很好的提示，它们应该被组合在一个单独的 Reducer Hook 中。

全局状态通常适合使用 Reducer Hook，而不是 State Hook，因为它的更改可以在应用的任何地方发生。当状态更改只在一个函数中处理，并且组件分发动作而不是直接修改状态时，处理状态更改要容易得多。将所有状态更改逻辑放在一个地方使得维护和修复错误变得更容易，而不会因为忘记更新逻辑而在任何地方引入新的错误。

## 将 State Hook 转换为 Reducer Hook

在我们的博客应用中，我们有两个全局 State Hook：

+   `username` 状态 – 包含当前登录用户的用户名

+   `posts` 状态 – 包含我们动态中的所有帖子

`username` 状态相当简单，它只包含一个用户名的字符串。因此，目前将此转换为 Reducer Hook 没有意义，因为状态更改是直接的：

+   在登录/注册时：设置用户名

+   在注销时：清除用户名

对于 `posts` 状态，我们之前已经需要使用扩展运算符来避免在创建新帖子时意外地从动态中删除帖子。因此，它似乎是一个很好的 Reducer Hook 候选，特别是考虑到它可能在未来扩展（获取新帖子、更新帖子、删除帖子等）。

现在，让我们开始用 Reducer Hook 替换 `posts` State Hook。

### 定义动作

我们首先为我们的 Reducer Hook 定义动作。目前，我们只考虑 `CREATE_POST` 动作：

```js
{
  type: 'CREATE_POST',
  post: {
    title: 'React Hooks',
    content: 'The greatest thing since sliced bread!',
    author: 'Daniel Bugl',
  },
} 
```

接下来，我们将实现 reducer 函数。

### 实现 reducer

目前，我们将把我们的 reducer 放在 `src/reducers.js` 文件中。稍后，如果我们有多个 reducer，可能有必要创建一个单独的 `src/reducers/` 文件夹，并为每个 reducer 函数创建单独的文件。

让我们开始实现 reducer 函数：

1.  通过执行以下命令将 `Chapter03_2` 文件夹复制到新的 `Chapter04_1` 文件夹：

    ```js
    $ cp -R Chapter03_2 Chapter04_1 
    ```

1.  在 VS Code 中打开新的 `Chapter04_1` 文件夹。

1.  创建一个新的 `src/reducers.js` 文件，在其中定义并导出 `postsReducer` 函数：

    ```js
    export function postsReducer(state, action) { 
    ```

1.  我们使用 `switch` 语句来处理不同的动作类型：

    ```js
     switch (action.type) { 
    ```

1.  接下来，处理 `CREATE_POST` 动作，将新帖子（来自 `action.post`）插入数组的开头，如下所示：

    ```js
     case 'CREATE_POST':
          return [action.post, ...state] 
    ```

1.  目前，这将是我们要处理的唯一动作类型，因此我们现在可以定义 `default` 语句，当遇到未知动作类型时抛出错误：

    ```js
     default:
          throw new Error('Unknown action type')
      }
    } 
    ```

在定义 reducer 函数后，我们现在可以使用它来定义 Reducer Hook。

### 定义 Reducer Hook

要定义 Reducer Hook，请按照以下步骤操作：

1.  编辑 `src/App.jsx` 并导入 `useReducer` 和 `postsReducer` 函数：

    ```js
    import { useState**, useReducer** } from 'react'
    **import** **{ postsReducer }** **from****'./reducers.js'** 
    ```

1.  *删除* 以下 State Hook：

    ```js
    export function App() {
    **const** **[posts, setPosts] =** **useState****(defaultPosts)** 
    ```

*替换* 它为 Reducer Hook：

```js
export function App() {
**const** **[posts, dispatch] =** **useReducer****(postsReducer, defaultPosts)** 
```

1.  然后，而不是 `setPosts`，将 `dispatch` 函数传递给 `CreatePost` 组件，并 *删除* `setPosts` 属性：

    ```js
     {username && <CreatePost username={username} **dispatch****=****{dispatch}** />} 
    ```

1.  接下来，编辑 `src/post/CreatePost.jsx` 并将 `setPosts` 属性替换为 `dispatch` 属性：

    ```js
    export function CreatePost({ username, **dispatch** }) { 
    ```

1.  现在不再以命令式的方式添加新帖子，而是在 `handleSubmit` 函数内部分发一个动作：

    ```js
     function handleSubmit(e) {
        e.preventDefault()
        const form = e.target
    const title = form.elements.title.value
    const content = form.elements.content.value
    const newPost = { title, content, author: username }
    **dispatch****({** **type****:** **'CREATE_POST'****,** **post****: newPost })**
        form.reset()
      } 
    ```

对于更复杂的动作，定义将创建动作对象的函数（所谓的 **动作创建器**）可能是有意义的。例如，一个 `createPostAction(post)` 函数可以创建并返回 `CREATE_POST` 动作对象。动作创建器可以帮助确保动作对象的一致结构，使我们更容易创建它们，并允许在未来轻松调整这种结构。

现在的 `posts` 状态使用 Reducer 钩子而不是 State 钩子，但它的功能与之前相同！如果我们想在以后添加更多用于管理帖子的逻辑，例如删除和编辑帖子，现在将更容易做到这一点。

**示例代码**

本节的示例代码可以在 `Chapter04/Chapter04_1` 文件夹中找到。请检查文件夹内的 `README.md` 文件，以获取设置和运行示例的说明。

在学习了 Reducer 钩子之后，让我们继续学习 Effect 钩子。

# 使用 Effect 钩子

Effect 钩子是一个重要的钩子，用于将组件与外部系统（如外部 API 或浏览器 API）同步。然而，在 React 代码中它经常被过度使用。如果没有外部系统参与，你不应该使用 Effect 钩子。

在我们博客的例子中，我们将在 `Logout` 组件中实现一种检查用户是否有管理员角色的方法。为了简单起见，并专注于 Effect 钩子本身，我们只是简单地模拟这个检查，但想象一下这是由外部 API 完成的。

## 记得 componentDidMount 和 componentDidUpdate 吗？

如果你之前使用过较老的 React 版本，你可能已经使用过 `componentDidMount` 和 `componentDidUpdate` 生命周期方法。例如，如果我们想使用 React 类组件将网页标题设置为给定的属性，我们需要添加以下生命周期方法：

```js
import React from 'react'
class App extends React.Component {
**componentDidMount****() {**
**const** **{ title } =** **this****.****props**
**document****.****title** **= title**
 **}**
render() {
    return <div>Example App</div>
  }
} 
```

这工作得很好。然而，当 `title` 属性更新时，变化并没有反映在我们的网页标题上。为了解决这个问题，我们需要定义 `componentDidUpdate` 生命周期方法，如下所示：

```js
import React from 'react'
class App extends React.Component {
  componentDidMount() {
    const { title } = this.props
document.title = title
  }
**componentDidUpdate****(****prevProps****) {**
**const** **{ title } =** **this****.**props
**if** **(title !== prevProps.****title****) {**
**document****.****title** **= title**
 **}**
 **}**
render() {
    return <div>Example App</div>
  }
} 
```

你可能已经注意到我们正在做完全相同的事情两次；因此，我们可以创建一个新的方法来处理标题的更新，然后从两个生命周期方法中调用它。在下面的代码块中，更新的代码以粗体显示：

```js
import React from 'react'
class App extends React.Component {
**updateTitle****() {**
**const** **{ title } =** **this****.****props**
**document****.****title** **= title**
 **}**
componentDidMount() {
**this****.****updateTitle****()**
  }
  componentDidUpdate(prevProps) {
    if (this.props.title !== prevProps.title) {
**this****.****updateTitle****()**
    }
  }
  render() {
    return <div>Example App</div>
  }
} 
```

然而，我们仍然需要调用 `this.updateTitle()` 两次。当我们稍后更新代码，例如向 `this.updateTitle()` 传递一个参数时，我们始终需要记得将其传递给函数的两个调用。如果我们忘记更新其中一个生命周期方法，我们可能会引入错误。此外，我们需要在 `componentDidUpdate` 中添加一个 `if` 条件，以避免在 `title` 属性没有变化时调用 `this.updateTitle()`。

## 从生命周期方法到 Effect 钩子

在 Hooks 的世界中，`componentDidMount` 和 `componentDidUpdate` 生命周期方法被合并到 `useEffect` 钩子中，当不指定 **依赖数组** 时，它会在每次重新渲染时触发。我们将在下一小节中了解更多关于依赖数组的内容。

因此，我们不再需要使用类组件，现在我们可以定义一个带有 Effect 钩子的函数组件，它将完成与之前相同的事情：

```js
import { useEffect } from 'react'
function App({ title }) {
**useEffect****(****() =>** **{**
**document****.****title** **= title**
 **})**
return <div>Example App</div>
} 
```

这就是我们需要做的全部！每当组件重新渲染时，Effect 钩子都会调用提供的函数。

自 React 19 以来，通过在任意组件中定义 `<title>`（或 `<link>` 或 `<meta>`）元素，可以更改网页的标题（或任何元数据标签）。然后，这些元素将自动提升到 `<head>` 部分。

## 仅在特定 props 发生变化时触发效果

如果我们想确保我们的效果函数仅在 `title` prop 发生变化时被调用，我们可以指定应该触发变化的值，作为 `useEffect` 钩子的第二个参数——依赖数组：

```js
useEffect(() => {
  document.title = title
}**, [title]**) 
```

依赖数组不仅限于 props；我们还可以使用从组件体内可用的任何值，包括在组件内部定义的变量和其他 Hook 的值，例如 State Hook 或 Reducer Hook：

```js
const [title, setTitle] = useState('')
useEffect(() => {
  document.title = title
}, [title]) 
```

如我们所见，使用 Effect 钩子比处理生命周期方法要简单得多。我们只需要指定 Effect 钩子应依赖于哪些值。每当这些值中的任何一个发生变化时，效果函数会自动再次被调用。

## 仅在挂载时触发效果

如果我们只想添加 `componentDidMount` 生命周期钩子的行为，而不在 props 发生变化时触发，我们可以通过将空数组作为 `useEffect` 钩子的第二个参数来做到这一点：

```js
useEffect(() => {
  document.title = title
}, **[]**) 
```

传递空数组意味着我们的效果函数仅在组件挂载时触发一次，并且当 props 发生变化时不会触发。然而，与组件的挂载相比，使用 Hooks，我们应该考虑效果依赖。在这种情况下，效果没有依赖，这意味着它只会触发一次。如果一个效果有指定的依赖，那么当任何依赖发生变化时，它将再次触发。

## 清理效果

有时在组件卸载时需要清理效果。为此，我们可以从 Effect 钩子中返回一个函数。这个返回的函数与 `componentWillUnmount` 生命周期方法的工作方式类似：

```js
useEffect(() => {
  const updateInterval = setInterval(
    () => console.log('fetching update'),
    updateTime,
  )
**return****() =>****clearInterval****(updateInterval)**
}, [updateTime]) 
```

突出的代码被称为 **清理函数**。当组件卸载并再次运行效果之前，清理函数会被调用。这避免了例如 `updateTime` prop 发生变化时的错误。在这种情况下，先前的效果将被清理，并定义一个新的 `updateTime` 间隔。

## 在我们的博客应用中实现 Effect 钩子

现在我们已经了解了 Effect Hook 的工作原理，我们将在我们的博客应用中使用它，以实现当用户已经登录时检查用户角色的方法。

让我们按照以下步骤实现一个 Effect Hook：

1.  通过执行以下命令将 `Chapter04_1` 文件夹复制到新的 `Chapter04_2` 文件夹：

    ```js
    $ cp -R Chapter04_1 Chapter04_2 
    ```

1.  在 VS Code 中打开新的 `Chapter04_2` 文件夹。

1.  编辑 `src/user/Logout.jsx` 并导入 `useState` 和 `useEffect` 函数：

    ```js
    import { useState, useEffect } from 'react' 
    ```

1.  然后，定义一个函数，该函数模拟一个检查用户角色的外部 API：

    ```js
    function getRole(username) { 
    ```

1.  为了简化，我们说任何名为 `admin` 的用户都具有 `admin` 角色。所有其他用户都具有 `user` 角色：

    ```js
     if (username === 'admin') return 'admin'
    return 'user'
    } 
    ```

1.  现在，我们需要为角色定义一个 State Hook：

    ```js
    export function Logout({ username, setUsername }) {
    **const** **[role, setRole] =** **useState****(****'user'****)** 
    ```

1.  接下来，定义一个通过调用“外部 API”并使用其响应来设置角色的 Effect Hook：

    ```js
     useEffect(() => {
        setRole(getRole(username))
      }, [username]) 
    ```

最好的做法是在依赖数组中列出所有在 Effect Hook 中使用的值（和函数）。这确保了在值看起来是静态的当前时刻，它们在以后变得动态时不会出现意外的错误。幸运的是，React Hooks ESLint 插件（已经在我们的项目中设置）会警告我们，如果我们忘记添加依赖项。

1.  最后，我们显示用户具有特殊角色时的角色：

    ```js
     return (
        <form onSubmit={handleSubmit}>
          Logged in as: <b>{username}</b>
    **{role !==** **'user'** **?** **` (role:****${role}****)`** **:** **''****}** 
    ```

1.  通过执行以下命令启动博客应用：

    ```js
    $ npm run dev 
    ```

尝试使用用户名 `admin` 登录。你会看到角色现在显示在用户名旁边！

**示例代码**

本节示例代码位于 `Chapter04/Chapter04_2` 文件夹中。请检查文件夹内的 `README.md` 文件，了解如何设置和运行示例。

# 摘要

在本章中，我们首先了解了动作、reducer 和 Reducer Hook。我们还学习了何时应该使用 Reducer Hook 而不是 State Hook。然后，我们将现有的 `posts` 状态的全局 State Hook 替换为 Reducer Hook。接下来，我们学习了 Effect Hook，以及它们如何替代 `componentDidMount` 和 `componentDidUpdate` 生命周期方法。最后，我们通过使用 Effect Hook 在我们的博客应用中实现了角色验证。

在下一章中，我们将学习 React Context 以及如何使用 Hooks。然后，我们将向我们的应用添加 Context Hooks，以避免在多个组件层传递 props。

# 问题

为了回顾本章所学内容，尝试回答以下问题：

1.  State Hook 常见的问题有哪些？

1.  动作是什么？

1.  Reducer 是什么？

1.  我们应该在什么情况下使用 Reducer Hook 而不是 State Hook？

1.  需要哪些步骤才能将 State Hook 转换为 Reducer Hook？

1.  我们如何更轻松地创建动作？

1.  Effect Hook 在类组件中的等效物是什么？

1.  与类组件相比，使用 Effect Hook 的优点是什么？

1.  依赖数组是什么，它是如何工作的？

1.  如何使用 Effect Hooks 与清理函数一起使用？

# 进一步阅读

如果你对本章学到的概念感兴趣，想了解更多，请查看以下链接：

+   关于 Reducer Hook 的官方文档：[`react.dev/reference/react/useReducer`](https://react.dev/reference/react/useReducer)

+   使用 Effect Hooks 的官方文档和技巧：[`react.dev/reference/react/hooks#effect-hooks`](https://react.dev/reference/react/hooks#effect-hooks)

+   关于何时不应使用 Effect Hook 的更多信息：[`react.dev/learn/you-might-not-need-an-effect`](https://react.dev/learn/you-might-not-need-an-effect)

+   关于 Redux 的更多信息，这是一个提供更广泛动作和 reducer 版本的库：[`redux.js.org`](https://redux.js.org)

# 在 Discord 上了解更多

要加入这本书的 Discord 社区——在那里你可以分享反馈、向作者提问，并了解新版本——请扫描下面的二维码：

`packt.link/wnXT0`

![](img/image_%283%29.png)
