

# 第三章：组件和 Props

学习目标

到本章结束时，你将能够做到以下事情：

+   构建可重用的 React 组件

+   利用名为**props**的概念来使组件可配置

+   通过组合组件和 props 构建灵活的用户界面

# 简介

在上一章中，你学习了任何基于 React 的用户界面的关键构建块：**组件**。你学习了组件的重要性、如何使用它们以及如何自己构建组件。

你还学习了 JSX，这是一种类似于 HTML 的标记，通常由组件函数返回。正是这种标记定义了最终网页上应该渲染的内容（换句话说，应该将哪个 HTML 标记放在最终提供给访客的网页上）。

# 组件能做更多吗？

然而，到目前为止，这些组件并没有太多用处。虽然你可以使用它们将网页内容分割成更小的构建块，但这些组件的实际可重用性相当有限。例如，作为整体课程目标列表的一部分，你可能有的每个课程目标都会进入其自己的组件（如果你最初决定将网页内容分割成多个组件）。

如果你这么想，这并不太有帮助；如果不同的列表项可以共享一个共同的组件，而你只需用不同的内容或属性来配置这个组件——就像 HTML 那样，那就更好了。

当编写纯 HTML 代码并使用它来描述内容时，你使用可重用的 HTML 元素，并通过不同的内容或属性来配置它们。例如，你有一个`<a>` HTML 元素，但由于`href`属性和元素子内容，你可以构建无限数量的不同锚点元素，它们指向不同的资源，如下面的代码片段所示：

```js
<a href="https://google.com">Use Google</a>
<a href="https://academind.com">Browse Free Tutorials</a> 
```

这两个元素使用完全相同的 HTML 元素（`<a>`），但会导致网页上完全不同的链接，最终指向两个完全不同的网站。

要完全释放 React 组件的潜力，如果你能够像常规 HTML 元素一样配置它们，那将非常有用。实际上，你可以通过另一个关键的 React 概念**props**来实现这一点。

# 在组件中使用 Props

你如何在组件中使用 props？你何时需要它们？

第二个问题将在稍后更详细地回答。目前，只需知道你通常会有一些可重用的组件，因此需要 props，以及一些独特的组件，可能不需要 props。

在这个阶段，问题的“如何”部分更为重要，这部分可以分解为两个互补的问题：

1.  将 Props 传递给组件

1.  在组件中消费 Props

## 将 Props 传递给组件

如果你要从头设计 React，你希望 props 和组件的可配置性如何工作？

当然，可能会有各种各样的可能解决方案，但有一个伟大的榜样可以考虑：HTML。如上所述，当使用 HTML 时，你可以在元素标签之间或通过属性传递内容和管理配置。

幸运的是，当配置组件时，React 组件的工作方式与 HTML 元素类似。Props 作为属性（传递给你的组件）或作为组件标签之间的子数据传递，你还可以混合这两种方法：

+   `<Product id="abc1" price="12.99" />`

+   `<FancyLink target="https://some-website.com">点击我</FancyLink>`

因此，配置组件相当直接——至少，如果你从消费者的角度看待它们（换句话说，在 JSX 中使用它们的角度）的话。

## 在组件中消费属性

当编写组件的内部代码时，如何获取传递给组件的 prop 值？

想象你正在构建一个`GoalItem`组件，该组件负责输出单个目标项（例如，课程目标或项目目标），这将作为整体目标列表的一部分。

父组件 JSX 标记可能看起来像这样：

```js
<ul>
  <GoalItem />
  <GoalItem />
  <GoalItem />
</ul> 
```

在`GoalItem`中，目标（无意中开玩笑）是接受不同的目标标题，以便相同的组件（`GoalItem`）可以用来输出这些不同的标题，作为显示给网站访客的最终列表的一部分。也许该组件还应接受其他一些数据（例如，用于内部的一个唯一 ID）。

正如以下示例所示，这就是`GoalItem`组件在 JSX 中的使用方式：

```js
<ul>
  <GoalItem id="g1" title="Finish the book!" />
  <GoalItem id="g2" title="Learn all about React!" />
</ul> 
```

在`GoalItem`组件函数内部，计划可能是输出动态内容（换句话说，通过属性接收到的数据），如下所示：

```js
function GoalItem() {
  return <li>{title} (ID: {id})</li>;
} 
```

但这个组件函数不会工作。它有一个问题：`title`和`id`在该组件函数内部从未被定义。因此，这段代码会导致错误，因为你正在使用一个未定义的变量。

当然，这些属性不应该在`GoalItem`组件内部定义，因为初衷是使`GoalItem`组件可重用，并从组件外部（即渲染`<GoalItem>`组件列表的组件）接收不同的`title`和`id`值 *（即未在组件内部定义的变量）*。

React 为这个问题提供了一个解决方案：一个特殊的参数值，React 自动将其传递给每个组件函数。这是一个特殊的参数，它包含在 JSX 代码中设置在组件上的额外配置数据，称为`props`参数。

上述组件函数可以（并且应该）重写如下：

```js
function GoalItem(props) {
  return <li>{props.title} (ID: {props.id})</li>;
} 
```

参数名称（`props`）由你决定，但使用`props`作为名称是一种约定，因为整体概念被称为**props**。

要理解这个概念，重要的是要记住，这些组件函数不是你在代码的其他地方调用的，相反，React 会代表你调用这些函数。由于 React 调用这些函数，它可以在调用它们时向它们传递额外的参数。

这个`props`参数确实是一个额外的参数。React 会将其传递到每个组件函数中，无论你是否在组件函数定义中将它定义为额外的参数。然而，如果你没有在组件函数中定义那个`props`参数，你当然无法在那个组件中处理`props`数据。

这个自动提供的`props`参数将始终包含一个对象（因为 React 将对象作为此参数的值传递），而这个对象的属性将是你在 JSX 代码中添加到组件的“属性”（例如`title`或`id`）。

正因如此，在这个`GoalItem`组件示例中，可以通过属性（`<GoalItem id="g1" … />`）传递自定义数据，并通过`props`对象及其属性（`<li>{props.title}</li>`）来消费这些数据。

# 组件、Props 和可复用性

多亏了这个 props 概念，组件实际上变得可复用，而不仅仅是理论上可复用。

不进行任何额外配置地输出三个`<GoalItem>`组件，只能渲染相同的目标三次，因为目标文本（以及你可能需要的任何其他数据）必须硬编码到组件函数中。

通过如上所述使用 props，相同的组件可以多次使用，具有不同的配置。这允许你一次性（在组件函数中）定义一些通用的标记结构和逻辑，然后根据需要以不同的配置多次使用。

如果这听起来很熟悉，那确实正是适用于常规 JavaScript（或任何其他编程语言）函数的相同想法。你定义逻辑一次，然后可以多次调用它，以不同的输入接收不同的结果。对于组件来说也是如此——至少当你接受这个 props 概念时。

## 特殊的“子组件”属性

之前提到过，React 会自动将这个`props`对象传递到组件函数中。这确实是事实，正如描述的那样，这个对象包含你在组件（在 JSX 中）上设置的属性的所有属性。

但 React 不仅将你的属性打包到这个对象中；它还向`props`对象添加了另一个额外的属性：特殊的`children`属性（一个名称固定的内置属性，这意味着你不能更改它）。

`children`属性包含一个非常重要的数据片段：你可能在组件的起始和结束标签之间提供的内容。

到目前为止，在上述示例中，组件大多是自闭合的。`<GoalItem id="…" title="…" />`在组件标签之间没有内容。所有数据都是通过属性传递到组件中的。

这种方法没有问题。你可以仅使用属性来配置你的组件。但对于某些数据和某些组件，坚持使用常规 HTML 约定，在组件标签之间传递这些数据可能更有意义，也更符合逻辑。`GoalItem`组件实际上是一个很好的例子。

哪种方法看起来更直观？

1.  `<GoalItem id="g1" title="Learn React" />`

1.  `<GoalItem id="g1">Learn React</GoalItem>`

你可能会觉得第二个选项看起来更直观，并且与常规 HTML 保持一致，因为在那里，你也会像这样配置一个普通的列表项：`<li id="li1">Some list item</li>`。

当你处理常规 HTML 元素时（你不能仅仅因为想要添加一个`goal`属性到`<li>`标签），你与 React 和自己的组件一起工作时确实有选择权。这完全取决于你在组件函数内部如何消费 props。两种方法都可以工作，具体取决于组件的内部代码。

然而，你可能仍然想要在组件标签之间传递某些数据片段，而特殊的`children`属性允许你做到这一点。它包含你在组件开始和结束标签之间定义的任何内容。因此，在上述列表中的示例 2 中，`children`将包含字符串`"Learn React"`。

在你的组件函数中，你可以像处理任何其他 prop 值一样处理`children`值：

```js
function GoalItem(props) {
  return <li>{props.children} (ID: {props.id})</li>;
} 
```

## 哪些组件需要 Props？

之前已经提到过，但这一点非常重要：**props 是可选的！**

React 总是会将**prop**数据传递到你的组件中，但你不必处理那个`prop`参数。如果你不打算使用它，甚至不需要在你的组件函数中定义它。

没有硬性规则可以定义哪些组件需要**props**，哪些不需要。这取决于经验，并且简单地取决于组件的角色。

你可能有一个通用的`Header`组件，用于显示静态标题（带有标志、标题等），这样的组件可能不需要外部配置（换句话说，没有“属性”或其他类型的数据传递给它）。它可以自包含，所有必需的值都硬编码到组件中。

但你也会经常构建和使用像`GoalItem`组件这样的组件（换句话说，需要外部数据才能有用的组件）。每当一个组件在你的 React 应用中被多次使用时，它很可能将利用属性。然而，反过来不一定成立。虽然你会有一次性的组件不使用属性，但你绝对也会有一些在整个应用中只使用一次但仍利用属性的组件。正如之前提到的，这取决于具体的使用案例和组件。

在这本书的整个过程中，你将看到许多示例和练习，这些将帮助你更深入地理解如何构建组件和使用属性。

## 如何处理多个属性

如前例所示，你不仅限于每个组件只有一个属性。实际上，你可以传递和使用组件需要的任何数量的属性——无论是一、一百（或更多）属性。

一旦你创建了具有两个或三个以上属性的组件，可能会出现一个新的问题：你是否必须逐个添加所有这些属性（换句话说，作为单独的属性），或者你可以传递包含分组数据（如数组或对象）的更少的属性？

确实如此。React 允许你将数组和对象作为属性值传递。事实上，任何有效的 JavaScript 值都可以作为属性值传递！

这让你可以决定你是否想要一个包含 20 个单独属性（“属性”）的组件，或者只是一个“大”属性。以下是一个示例，展示了同一个组件以两种不同的方式配置：

```js
<Product title="A book" price={29.99} id="p1" />
// or
const productData = {title: 'A book', price: 29.99, id: 'p1'};
<Product data={productData} /> 
```

当然，组件也必须在内部（换句话说，在组件函数中）进行调整，以期望单个或分组属性。但既然你是开发者，当然，这是你的选择。

在组件函数内部，你还可以使自己的生活变得更简单。

通过`props.XYZ`访问属性值没有问题，但如果你的组件接收多个属性，反复重复`props.XYZ`可能会变得繁琐，并使代码难以阅读。

你可以使用默认的 JavaScript 功能来提高可读性：**对象解构**。

对象解构允许你从对象中提取值，并将这些值一次性分配给变量或常量：

```js
const user = {name: 'Max', age: 29};
const {name, age} = user; // <-- object destructuring in action
console.log(name); // outputs 'Max' 
```

因此，你可以使用这种语法在组件函数的开始处直接提取所有属性值并将它们分配给同名的变量：

```js
function Product({title, price, id}) { // destructuring in action
  … // title, price, id are now available as variables inside this function
} 
```

你不必使用这种语法，但它可以使你的生活变得更简单。

**注意**

关于对象解构的更多信息，MDN 是一个深入了解的好地方。你可以在这里访问[`developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)。

## 属性展开

想象你正在构建一个自定义组件，该组件应作为其他组件的“包装器”——可能是内置组件。

例如，你可以构建一个自定义的`Link`组件，该组件应返回一个带有一些自定义样式或逻辑的标准`<a>`元素：

```js
function Link({children}) {
  return <a target="_blank" rel="noopener noreferrer">{children}</a>;
}; 
```

这个非常简单的示例组件返回一个预配置的`<a>`元素。这个自定义的`Link`组件配置了锚元素，使得新页面总是在新标签页中打开。你可以用这个`Link`组件替换标准`<a>`元素，在你的 React 应用中为所有链接提供开箱即用的行为。

但这个自定义组件存在一个问题：它是核心元素的包装器，但通过创建自己的组件，你移除了核心元素的配置性。如果你要在你的应用中使用这个`Link`组件，你将如何设置`href`属性来配置链接目标？

你可以尝试以下方法：

```js
<Link href="https://some-site.com">Click here</Link> 
```

然而，这个示例代码不会工作，因为`Link`不接受或使用`href`属性。

当然，你可以调整`Link`组件函数，使其使用`href`属性：

```js
function Link({children, href}) {
  return <a href={href} target="_blank" rel="noopener noreferrer">{children}</a>;
}; 
```

但如果你还想确保在需要时可以添加`download`属性呢？

嗯，确实你可以接受越来越多的属性（并将它们传递到组件内的`<a>`元素），但这会降低自定义组件的可重用性和可维护性。

一个更好的解决方案是使用标准的 JavaScript**展开操作符**（即`...`操作符）以及 React 对该操作符的支持，当与组件一起工作时。

例如，以下组件代码是有效的：

```js
function Link({children, config}) {
  return <a {...config} target="_blank" rel="noopener noreferrer">{children}</a>;
}; 
```

在这个例子中，`config`预期是一个 JavaScript 对象（即一组键值对）。当在 JSX 代码中的 JSX 元素上使用展开操作符（`...`）时，它会将该对象转换为多个属性。

考虑这个`config`值的例子：

```js
const config = { href: 'https://some-site.com', download: true }; 
```

在这种情况下，当在`<a>`上展开时（即`<a {...config}>`），结果将与以下代码相同：

```js
<a href="https://some-site.com" download={true}> 
```

另一种更常见的模式使用了另一个 JavaScript 特性：**剩余属性**。这是一个 JavaScript 模式，允许你将未解构的属性组合到一个新对象中（该对象只包含那些属性）。

```js
function Link({children, **...props**}) {
  return <a {...props} target="_blank" rel="noopener noreferrer">{children}</a>;
}; 
```

在这个例子中，当解构属性时，只有`children`属性被解构；其他属性存储在一个名为`props`的新对象中。语法与展开操作符语法非常相似：你使用三个点（`...`）。但在这里，你使用操作符在应包含所有剩余属性的前面。因此，这就是你使用该操作符的地方，它定义了它所做的事情。

你可以使用那个剩余属性（例如示例中的`props`）就像使用任何其他对象一样。在上面的例子中，它再次被用来将属性作为属性展开到`<a>`元素上。

使用这种模式可以使您更自然地使用`Link`组件，您不需要创建和使用单独的配置对象：

```js
<Link href="https://google.com">Can you google that for me?</Link> 
```

这些行为和模式可以用来构建可重用的组件，同时仍然保持它们可能包装的核心元素的可配置性。这有助于您避免长列表的预定义、接受的 props，并提高组件的可重用性。

## Prop Chains/Prop Drilling

在学习关于 props 时，还有一个值得注意的最后现象：**prop drilling**或**prop chains**。

这是每个 React 开发者迟早会遇到的问题。当您构建一个稍微复杂一些的 React 应用程序，其中包含多层嵌套组件，这些组件需要相互发送数据时，就会发生这种情况。

例如，假设您有一个`NavItem`组件，它应该输出一个导航链接。在这个组件内部，您可能还有一个嵌套组件`AnimatedLink`，它输出实际的链接（可能带有一些漂亮的动画样式）。

`NavItem`组件可能看起来像这样：

```js
function NavItem(props) {
  return <div><AnimatedLink target={props.target} text="Some text" /></div>;
} 
```

`AnimatedLink`可以这样定义：

```js
function AnimatedLink(props) {
  return <a href={props.target}>{props.text}</a>;
} 
```

在这个例子中，`target` prop 通过`NavItem`组件传递到`AnimatedLink`组件。`NavItem`组件必须接受`target` prop，因为它必须传递给`AnimatedLink`。

这就是 prop drilling/prop chains 的实质：您从一个实际上并不需要它的组件中转发一个 prop 到另一个确实需要它的组件。

在您的应用程序中存在一些 prop drilling 并不一定不好，您当然可以接受它。但是，如果您最终得到更长的 props 链（换句话说，多个**传递组件**），您可以使用将在*第十一章*，*处理复杂状态*中讨论的解决方案。

# 摘要和关键要点

+   Props 是 React 的一个关键概念，它使组件可配置，因此可重用。

+   React 会自动收集并传递 props 到组件函数中。

+   您决定（针对每个组件）是否想要使用 props 数据（一个对象）。

+   Props 像属性一样传递到组件中，或者通过特殊的`children` prop 在开标签和闭标签之间传递。

+   您可以使用 JavaScript 功能，如解构、剩余属性或扩展运算符来编写简洁、灵活的代码。

+   由于您正在编写代码，所以您决定如何通过 props 传递数据。是在标签之间还是作为属性？是一个分组属性还是多个单值属性？这取决于您。

## 接下来是什么？

Props 允许您使组件可配置和可重用。尽管如此，它们相对静态。数据和因此 UI 输出不会改变。您无法对用户事件（如按钮点击）做出反应。

但 React 的真正力量只有在您添加事件（以及对其的反应）之后才会显现出来。

在下一章中，你将学习如何在处理 React 时添加事件监听器，以及你将学习如何对事件做出反应（无意中开玩笑），并改变应用程序的（不可见和可见）状态。

## 测试你的知识！

通过回答以下问题来测试你对本章涵盖的概念的了解。然后，你可以将你的答案与在[`github.com/mschwarzmueller/book-react-key-concepts-e2/blob/03-components-props/exercises/questions-answers.md`](https://github.com/mschwarzmueller/book-react-key-concepts-e2/blob/03-components-props/exercises/questions-answers.md)上可以找到的示例答案进行比较：

1.  属性解决了哪些“问题”？

1.  属性是如何传递到组件中的？

1.  属性是如何在组件函数内部消费的？

1.  有哪些选项可以用于将（多个）属性传递到组件中？

# 应用你所学的知识

通过本章和上一章，你现在已经有了足够的基础知识来构建真正可重用的组件。

下面，你将找到一个活动，它允许你应用到目前为止所获得的所有知识，包括新的属性知识。

## 活动三.1：创建一个应用来输出本书的目标

本活动基于上一章的*活动 2.2*，*创建一个 React 应用来记录本书的目标*，并在此基础上进行。如果你跟随了那里的内容，你可以使用你现有的代码，并通过添加属性来增强它。或者，你也可以使用以下链接中可访问的解决方案作为起点：[`github.com/mschwarzmueller/book-react-key-concepts-e2/tree/02-components-jsx/activities/practice-2`](https://github.com/mschwarzmueller/book-react-key-concepts-e2/tree/02-components-jsx/activities/practice-2)。

本活动的目的是构建可配置的`GoalItem`组件，这些组件可以通过属性进行配置。每个`GoalItem`组件都应该接收并输出一个目标标题和简短描述文本，以及关于目标的其他信息。

步骤如下：

1.  完成上一章的第二项活动。

1.  用一个新的可配置组件替换硬编码的目标组件。

1.  通过属性输出具有不同标题的多个目标组件。

1.  在目标组件的打开和关闭标签之间设置每个目标的详细文本描述。

最终的用户界面可能看起来像这样：

![img](img/B31339_03_01.png)

图 3.1：最终结果：每个目标输出在下方

**注意**

你可以在以下链接中找到完整的示例解决方案：[`github.com/mschwarzmueller/book-react-key-concepts-e2/tree/03-components-props/activities/practice-1`](https://github.com/mschwarzmueller/book-react-key-concepts-e2/tree/03-components-props/activities/practice-1)。
