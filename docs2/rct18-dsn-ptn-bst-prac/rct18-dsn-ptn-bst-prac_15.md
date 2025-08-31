# 15

# 提高应用程序的性能

网络应用程序的有效性能对于提供良好的用户体验和提高转化率至关重要。React 库实现了不同的技术来快速渲染我们的组件，并尽可能少地接触**文档对象模型**（**DOM**）。对 DOM 的应用通常代价高昂，因此最小化操作数量至关重要。

然而，有一些场景 React 无法优化过程，这就需要开发者实现特定的解决方案来使应用程序运行顺畅。

在本章中，我们将学习 React 的基本概念，并学习如何使用一些 API 来帮助库找到更新 DOM 的最佳路径，而不会降低用户体验。我们还将看到一些可能损害我们的应用程序并使其变慢的常见错误。

我们应该避免仅仅为了优化而优化我们的组件，并且只有在需要时才应用以下章节中将要看到的技巧。

在本章中，我们将涵盖以下主题：

+   如何实现协调以及我们如何可以使用键来帮助 React 做得更好

+   常见的优化技术和常见的性能相关错误

+   有用的工具和库，使我们的应用程序运行更快

+   使用不可变数据意味着什么以及如何实现

# 技术要求

要完成本章，你需要以下内容：

+   Node.js 19+

+   Visual Studio Code

你可以在本书的 GitHub 仓库中找到本章的代码：[`github.com/PacktPublishing/React-18-Design-Patterns-and-Best-Practices-Fourth-Edition/tree/main/Chapter15`](https://github.com/PacktPublishing/React-18-Design-Patterns-and-Best-Practices-Fourth-Edition/tree/main/Chapter15)。

# 如何实现协调

大多数情况下，React 默认情况下就足够快，你不需要做任何事情来提高你应用程序的性能。React 利用不同的技术来优化屏幕上组件的渲染。

当 React 必须显示一个组件时，它会调用其`render`方法以及其子组件的递归`render`方法。组件的`render`方法返回一个 React 元素的树，React 使用这个树来决定必须执行哪些 DOM 操作以更新 UI。

每当组件状态发生变化时，React 会在节点上再次调用`render`方法，并将结果与之前的 React 元素树进行比较。库足够智能，可以找出应用预期更改所需的最小操作集。这个过程被称为**协调**，并且由 React 透明地管理。正因为如此，我们可以轻松地以声明性方式描述我们的组件在特定时间点应该如何看起来，并让库完成其余的工作。

React 试图在 DOM 上应用尽可能少的操作，因为接触 DOM 是一个昂贵的操作。

然而，比较两个元素树也不是免费的，React 做出了两个假设来减少其复杂性：

+   如果两个元素具有不同的类型，它们将渲染不同的树。

+   开发者可以使用键来标记在不同渲染调用中保持稳定的子元素。

从开发者的角度来看，第二点很有趣，因为它给我们提供了一个工具，可以帮助 React 更快地渲染我们的视图。

默认情况下，当返回到 DOM 节点的子元素时，React 会同时迭代两个子元素列表，并且每当有差异时，它就会创建一个突变。

让我们看看一些例子。在将以下两个树转换为添加到子元素末尾的元素时，将工作得很好：

```js
<ul>
  <li>Carlos</li>
  <li>Javier</li>
</ul>
<ul>
  <li>Carlos</li>
  <li>Javier</li>
  <li>Emmanuel</li>
</ul> 
```

两个`<li>Carlos</li>`树通过 React 与两个`<li>Javier</li>`树匹配，然后它会插入`<li>Emmanuel</li>`树。

如果天真地实现，在开始处插入一个元素会产生较差的性能。如果我们看例子，当在这两个树之间转换时，它表现得非常糟糕：

```js
<ul>
  <li>Carlos</li>
  <li>Javier</li>
</ul>
<ul>
  <li>Emmanuel</li>
  <li>Carlos</li>
  <li>Javier</li>
</ul> 
```

每个子元素都将被 React 突变，而不是它意识到它可以保持子树`<li>Carlos</li>`和`<li>Javier</li>`的完整性。这可能是一个问题。当然，这个问题可以解决，解决方法是使用 React 支持的`key`属性。让我们看看下一个。

# 使用键

子元素具有键，这些键被 React 用于匹配后续树和原始树之间的子元素。通过在我们的前一个示例中添加键，可以使树转换更高效：

```js
<ul>
  <li key="2018">Carlos</li>
  <li key="2019">Javier</li>
</ul>
<ul>
  <li key="2017">Emmanuel</li>
  <li key="2018">Carlos</li>
  <li key="2019">Javier</li>
</ul> 
```

React 现在知道`2017`键是新的，而`2018`和`2019`键只是移动了。

找到一个键并不难。你将要显示的元素可能已经有一个唯一的 ID。因此，键可以直接来自你的数据：

```js
<li key={element.id}>{element.title}</li> 
```

你可以给你的模型添加一个新的 ID，或者键可以由内容的一些部分生成。键必须在它的兄弟元素中是唯一的；它不需要在全局范围内是唯一的。可以将数组中的项目索引作为键传递，但现在这被认为是一种坏做法。然而，如果项目从未被记录，这可以很好地工作。重新排序将严重影响性能。

如果你使用`map`函数渲染多个项目，并且没有指定`key`属性，你会得到这个消息：*警告：数组或迭代器中的每个子元素都应该有一个唯一的* *key 属性*。

让我们在下一节学习一些优化技术。

# 优化技术

重要的是要注意，在这本书的所有示例中，我们使用的是要么是用**create-react-app**创建的，要么是从零开始创建的，但总是使用 React 的开发版本。

使用 React 的开发版本对于编码和调试非常有用，因为它提供了修复各种问题的所有必要信息。然而，所有的检查和警告都有代价，我们希望在生产中避免这些代价。

因此，我们应该对应用程序做的第一个优化是构建包，将`NODE_ENV`环境变量设置为`production`。这在使用 webpack 时很容易，只需使用以下方式中的`DefinePlugin`即可：

```js
new webpack.DefinePlugin({
  'process.env': {
   NODE_ENV: JSON.stringify('production')
  }
}) 
```

为了达到最佳性能，我们不仅希望创建带有`production`标志的包，还希望将我们的包拆分，一个用于我们的应用程序，另一个用于`node_modules`。

要做到这一点，您需要使用 webpack 中的新`optimization`节点：

```js
optimization: {
  splitChunks: {
   cacheGroups: {
    default: false,
    commons: {
     test: /node_modules/,
     name: 'vendor',
     chunks: 'all'
    }
   }
  }
} 
```

Webpack 有两种模式，*开发*和*生产*。默认情况下，生产模式是启用的，这意味着当您使用生产模式编译包时，代码将被压缩和压缩；您可以使用以下代码块指定它：

```js
{
  mode: process.env.NODE_ENV === 'production' ? 'production' : 'development',
} 
```

您的`webpack.config.ts`文件应如下所示：

```js
module.exports = {
  entry: './index.ts',
  optimization: {
   splitChunks: {
    cacheGroups: {
     default: false,
     commons: {
      test: /node_modules/,
      name: 'vendor',
      chunks: 'all'
     }
    }
   }
  },
  plugins: [
   new webpack.DefinePlugin({
    'process.env': {
     NODE_ENV: JSON.stringify('production')
    }
   })
  ],
  mode: process.env.NODE_ENV === 'production' ? 'production' : 'development'
} 
```

使用此 webpack 配置，我们将获得非常优化的包；一个用于我们的供应商，另一个用于实际的应用程序。

# 工具和库

在下一节中，我们将介绍几种技术、工具和库，我们可以将这些应用到我们的代码库中以监控和改进性能。

## 不可变性

新的 React Hooks，如`React.memo`，对 props 使用浅比较方法，这意味着如果我们传递一个对象作为 props，并且我们更改其值之一，我们将不会得到预期的行为。

实际上，浅比较无法在属性上找到突变，并且组件永远不会重新渲染，除非对象本身发生变化。解决此问题的一种方法是通过使用不可变数据，这种数据一旦创建，就不能被更改。

例如，我们可以按以下模式设置状态：

```js
const [state, setState] = useState({})
const obj = state.obj
obj.foo = 'bar'
setState({ obj }) 
```

即使对象的`foo`属性值已更改，对象的引用仍然是相同的，浅比较也不会识别出来。

我们可以做的另一件事是每次更改对象时创建一个新的实例，如下所示：

```js
const obj = Object.assign({}, state.obj, { foo: 'bar' })
setState({ obj }) 
```

在这种情况下，我们得到一个新的对象，其`foo`属性设置为`bar`，浅比较将能够找到差异。使用 ES6 和 Babel，还有另一种以更优雅的方式表达相同概念的方法，即使用对象扩展运算符：

```js
const obj = {
  ...state.obj,
  foo: 'bar'
} 
setState({ obj }) 
```

这种结构比之前的结构更简洁，并且会产生相同的结果，但截至写作时，它需要将代码转换为浏览器内执行。

React 提供了一些不可变性辅助工具，使处理不可变对象变得容易，还有一个名为`immutable.js`的流行库，它具有更强大的功能，但需要您学习新的 API。

## Babel 插件

此外，还有一些有趣的**Babel**插件，我们可以安装并使用它们来提高我们 React 应用程序的性能。它们使应用程序运行更快，在构建时优化代码的部分。

第一个是我们可以选择使用的 React **常量元素转换器**，它找到所有不依赖于 props 的静态元素，并将它们从 `render`（或函数组件）中提取出来，以避免不必要的 **_jsx** 调用。

使用 Babel 插件非常简单。我们首先使用 `npm` 安装它：

```js
npm install --save-dev @babel/plugin-transform-react-constant-elements 
```

您需要创建 `.babelrc` 文件，并添加一个 `plugins` 键，其值为一个数组，包含我们想要激活的插件列表：

```js
{
   "plugins": ["@babel/plugin-transform-react-constant-elements"]
} 
```

第二个 Babel 插件，我们可以选择使用来提高性能的是 React 内联元素转换，它将所有的 JSX 声明（或 **_jsx** 调用）替换为更优化的版本，以使执行更快。

使用以下命令安装插件：

```js
npm install --save-dev @babel/plugin-transform-react-inline-elements 
```

接下来，您可以轻松地将插件添加到 `.babelrc` 文件中的插件数组中，如下所示：

```js
{
  "plugins": ["@babel/plugin-transform-react-inline-elements"]
} 
```

这两个插件应该只在生产环境中使用，因为它们会使开发模式中的调试更加困难。到目前为止，我们已经学习了许多优化技术以及如何使用 webpack 配置一些插件。

# 摘要

我们的性能之旅已经结束，现在我们可以优化我们的应用程序，为用户提供更好的用户体验。

在本章中，我们学习了协调算法的工作原理以及 React 如何始终尝试采取最短路径来应用更改到 DOM。我们还可以通过使用键来帮助库优化其工作。一旦找到瓶颈，就可以应用本章中我们看到的技术之一来解决问题。

我们已经学习了如何通过重构和正确设计组件的结构来提供性能提升。我们的目标是拥有小型组件，它们以最佳方式完成单一任务。在章节末尾，我们讨论了不可变性，并看到了为什么不要修改数据对于使 `React.memo` 和 `shallowCompare` 正常工作很重要。最后，我们回顾了不同的工具和库，它们可以使您的应用程序更快。

在下一章中，我们将探讨使用 Jest、React 测试库和 React DevTools 进行测试和调试。

# 加入我们的 Discord 社区

加入我们社区的 Discord 空间，与作者和其他读者进行讨论：

[`packt.link/React18DesignPatterns4e`](https://packt.link/React18DesignPatterns4e)

![二维码](img/QR_Code2450023176943770109.png)
