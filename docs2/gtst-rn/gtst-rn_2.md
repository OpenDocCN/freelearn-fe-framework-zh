# 第二章：理解 React Native 基础知识

你可能对 React Web 的工作原理不太熟悉，所以我们将在本章中介绍其基础知识。我们还将解释 React Web 在底层是如何工作的核心原则。一旦你对基础知识有了扎实的理解，我们将深入探讨 React Web 的工作原理以及移动端和 Web 端之间的细微差别。到本章结束时，你将具备开始构建示例应用程序所需的技能。

在本章中，我们将涵盖以下主题：

+   虚拟 DOM

+   介绍组件和 JSX

+   编写我们的第一个组件

+   组件的属性和状态

# 虚拟 DOM

你知道如何编写 JavaScript 函数吗？如果你知道，那太好了！你正走在理解 React 和 React Native 底层工作原理的道路上。我们具体指的是什么？当你研究 React 的工作原理时，你最终会遇到有人用以下方式解释它：

```js
UI = f(data)
```

你可能会说，*警告：这有什么用？* 好吧，这是说你的 UI 是你的数据的函数。用更熟悉的话来说，让我们说：

```js
var todos = function(data) { return data.join( " -- " ) }
```

你可以用数据数组调用该函数，例如：

```js
var ui = todos( ["wake up", "get out of bed", "drag a comb across my head"] );
console.log(ui);
```

这不是特别震撼人心的代码；然而，你现在正在渲染一些内容，在这种情况下是到控制台。

如果所有的 UI 渲染代码都能如此可预测呢？它可以！让我们开始变得稍微高级一些。除了我们的`todos()`函数外，如果我们还有一个名为`todoItem()`的函数，例如：

```js
var todoItem = function(data) { return "<strong>" + data + "</strong>" }
```

这看起来很像我们的原始`UI`函数，不是吗？：

```js
UI = f(data)
```

假设我们开始组合我们的`todos()`和`todoItems()`，例如：

```js
var ui = todos( [todoItem("wake up"), todoItem("get out of bed")] );
```

你可以开始想象，我们可以通过组合简单的函数来渲染越来越复杂的输出。

如果我们想开始将内容渲染到浏览器中呢？我敢肯定你能想象将我们的`todoItem()`改为使用 jQuery 添加元素到 DOM；然而，在这种情况下，我们将开始重复很多次，有很多`appendChild()`调用和 jQuery 选择器的实例。如果我们真的很聪明，我们可能会编写一个框架来抽象 DOM 操作，这样我们就可以编写对我们应用程序重要的代码，而不仅仅是针对浏览器的代码。

好吧，现在假设我们神奇地获得了一个框架，它允许我们将 UI 表示为一个`data`函数，我们不必考虑内容是如何渲染到 DOM 中的。我们可以不断地更改我们的数据，并观察 DOM 的更新！从理论上讲，这听起来很棒，但当我们有数十个深度嵌套的`div`元素时，底层的 DOM 操作变得复杂且效率低下。

如果我们的魔法框架有一个 DOM 的中间表示形式怎么办？让我们称它为虚拟 DOM，并且让我们说，我们不是对 DOM 进行每一个小更改，而是将更改批量处理在一起。我们甚至可以比较虚拟 DOM 的当前和之前状态。找出差异，减少我们需要执行的真正 DOM 操作次数。现在我们真的开始有所作为了！

因此，我们现在可以将我们的 UI 表达为数据的一个函数。我们不必考虑底层的 DOM 操作代码，而且我们的 UI 既美观又迅速，因为底层框架非常智能，减少了它需要执行的 DOM 操作次数。有一个能够为我们做这件事的框架将会非常棒，但你知道吗，什么会真正酷？如果 DOM 不需要是浏览器 DOM 怎么办？如果那个允许我们编写对应用有意义的代码的相同抽象能够用来，比如说，更新原生移动组件怎么办？这就是 React Native。

# 组件

现在有一个有趣的问题；我们遇到了这个伟大的框架，用于快速在虚拟 DOM 和其原生组件之间进行差异比较。我们如何告诉 React Native 要表示什么 UI 或何时更改它？一个 React Native 组件是一个简单、可重用的、类似函数的对象，使我们能够描述我们想要渲染的原生移动组件。它们将始终包含属性、状态和一个渲染方法。让我们从创建我们自己的组件开始，从非常简单的地方开始。

## 创建您的第一个组件

在 React Native 中创建一个新的组件将类似于以下内容：

```js
import React, {
  Text,
  View
  } from 'react-native';
class HelloComponent extends React.Component {
  render () {
    return (
    <View>
      <Text>Hello React</Text>
    <View>
  );
  }
}
```

### 小贴士

记得导入 React Native 模块。在这里，我们使用 ES6 导入语句；它类似于 node require 模块的工作方式。

*等等…* 这些奇怪的 XML 元素在我的 JavaScript 代码中做什么？Facebook 为描述 React 组件在 JavaScript 上创建了自己的语法扩展。以下是完全相同的代码，但用普通的 JavaScript 编写：

```js
var HelloComponent = React.createClass({displayName: "HelloComponent"}, render: function () {
  return (
    React.createElement(View, null,
      React.createElement(Text, null, "Hello React")
  )
));
```

虽然只使用 JavaScript 编写 React Native 应用程序是可能的，但之前的语法为开发者提供了许多额外的优势。

## JSX

**JavaScript XML（JSX**）是 ECMAScript 规范的一个类似 XML 的扩展。它将组件逻辑（JavaScript）和标记（DOM 或原生 UI）合并到一个文件中。

JSX 元素将具有以下形式：

```js
var element = (
  <JSXElement>
    <SubJSXElement />
    <SubJSXElement />
    <SubJSXElement />
  <JSXElement />
);
```

JSX 规范还定义了以下内容：

+   JSX 元素可以是自闭合的`<JSXElement></JSXElement>`或自闭合的`<JSXElement />`。

+   以表达式`{}`或字符串`""`的形式接受属性`<Component attr="attribute">`。表达式是 JavaScript 片段。

+   子元素可以是文本、表达式或元素。

### 注意

**如果你有多个组件或组件列表怎么办？**

只能有一个根元素；这意味着如果你有多个组件，你必须将它们包裹在一个父组件中。

这很酷！我们已经从深层嵌套和命令式的 JavaScript 代码转变为描述我们希望在组件中看到的精确元素的声明性格式。由于我们的逻辑与我们的标记耦合，因此没有关注点的分离，这使得组件更容易调试和测试。由于你总是可以将相同的组件包含在多个其他组件中，因此根本不需要重复代码。

注意，JSX 仅应作为预处理器使用，不建议在生产构建中进行转译。有关 JSX 的更多信息，请参阅官方 React 文档[`facebook.github.io/react/docs/jsx-in-depth.html`](https://facebook.github.io/react/docs/jsx-in-depth.html)或官方 JSX 规范[`facebook.github.io/jsx/`](https://facebook.github.io/jsx/)。

## 回到我们的第一个组件

在我们的组件中，我们忽略了一些事情。**View**和**Text**是 React Native 提供的许多组件中的一部分，用于构建 UI。这些不是在 JavaScript 层渲染的常规组件，它们可以直接映射到其原生容器部分！View 组件映射到 iOS 中的`UIView`和 Android 中的`android.view`，而 Text 是分别在每个平台上显示文本的通用组件。**View**和**Text**支持各种功能，如布局、样式和触摸处理。

反复显示相同的静态文本并不令人兴奋。让我们扩展这个简单的组件并添加一些更多功能。

## 属性和状态

在这个阶段，你可能想知道随着组件数量的增长形成组件层次结构时，React Native 是如何处理组件操作和通信的。组件层次结构类似于树，从根组件开始，可以包含许多子组件。React Native 提供了两种数据传递方法；一种用于组件层次结构中的数据流向下传递，另一种用于维护内部状态。

### 属性

同一组件层次结构中的组件之间是如何进行通信的呢？数据通过通常称为**属性**的属性向下传递。按照惯例，属性被认为是不可变的，不应直接修改。要将属性传递给组件，只需在组件中添加一个驼峰式命名的属性即可：

```js
<HelloComponent text="Hello React" />
```

可以通过`this.props`在组件内部访问属性：

```js
import React, {
  Text,
  View
} from 'react-native';

class HelloComponent extends React.Component {
  render () {
    return (
      <View>
        <Text>{this.props.text}</Text>
      View>
    );
  }
}
```

### 小贴士

**如果我想要传递很多属性怎么办？**

使用 ES7 扩展运算符`<HelloComponent {...props} />`可以将属性数组传递给组件。

并非总是需要将属性包含在组件中，但如果你需要为属性指定默认值，可以将`defaultProps`对象分配给组件类的构造函数。

```js
HelloComponent.defaultProps = {text: "Default Text!"};
```

#### 验证属性

如果你打算将你的组件公开给公众，限制开发者使用它的方式是有意义的。为了确保你的组件被正确使用，可以使用 **PropTypes** 模块来验证传入的任何 props。如果某个 prop 未通过 `propType` 验证，则会在控制台中向开发者显示警告。`PropTypes` 覆盖了广泛的 JavaScript 类型和中型，包括嵌套对象。你可以在组件的类构造函数上定义 `propTypes`：

```js
HelloComponent.propTypes = {text: React.PropTypes.string};
```

更多关于 `propTypes` 的信息，请访问 React 文档的 Prop 验证部分 [`facebook.github.io/react/docs/reusable-components.html`](https://facebook.github.io/react/docs/reusable-components.html)。

### 状态

因此，现在我们可以传递数据，但如果数据发生变化，我们如何向用户显示这些变化呢？组件可以包含可选的状态，这是一个可变且私有的数据集。状态是跟踪用户输入、异步请求和事件的绝佳方式。当用户与组件交互时，让我们更新我们的组件以添加额外的文本：

```js
import React, {
  Text,
  View,
  Component
  } from 'react-native';
  class HelloComponent extends React.Component{
    constructor (props) {
      super(props);
      this.state = {  // Set Initial State
      appendText: ''
    };
  }
  render () {
    return (
      <View>
        <Text onPress={() => setState({text: ' Native!'})}>{this.props.text + this.state.appendText}</Text>
        <View>
    );
  }
}
```

触摸 `Text` 组件将触发其 `onPress` prop 中的函数。我们正在利用 ES6 箭头语法将我们的功能与文本组件一起放在一行中。

### 注意

使用 ES6 箭头语法将自动将 `this` 绑定到函数上。对于任何非箭头函数，如果你需要访问 `this`，那么你需要在 `<Text onPress={this.myFunction.bind(this)}>` 的 props 表达式中将值绑定到函数上。

`setState` 函数将你传递给第一个参数的对象与组件的当前状态合并。调用 `setState` 将触发一个新的渲染，其中 `this.state.appendText` 将在原始从 props 传递的文本值上追加 **Native!**，最终结果是 `"Hello React" + " Native!"`，生成 `"Hello React Native!"`。

永远不要尝试直接修改这个状态值。直接更改状态可能导致下一次 `setState` 调用期间数据丢失，并且不会触发另一个重新渲染。

# 摘要

现在，希望你能理解 React 在实现性能方面所采取的激进新方向。虚拟 DOM 在幕后处理所有的 DOM 操作。同时，它使用高效的差异算法来最小化对 DOM 的调用次数。我们也看到了 JSX 如何允许我们声明式地表达我们的组件，并将应用程序逻辑合并到一个文件中。通过使用 props 和 state，我们可以通过组件传递数据并动态更新它们。

希望你现在能够利用本章学到的信息，说服你的老板立即开始使用 React Native！
