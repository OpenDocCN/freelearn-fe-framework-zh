# *第十章*：使用 React 构建网站

在本书的最后一章，我们将一般性地讨论 *React*，特别是 *React* 在网页开发中所扮演的角色。我们将从三个角度来探讨这个问题，看看 *React* 如何将资源整合起来构建一个网站。首先，我们将看看 *React* 如何拥抱新的 *JavaScript ES6* 功能，如箭头函数和模板字符串。然后，我们将通过使用 `styled-JSX` 和 `styled-components` 等库的 *CSS-in-JS* 方法来展示如何对组件进行样式化。最后，但同样重要的是，我们还将了解 JSX 代码以及它是如何被用来将类似 *HTML* 的行转换为 *JavaScript* 表达式的。

在本章中，我们将涵盖以下主题：

+   *React* 的功能

+   拥抱 *JavaScript* *ES6*

+   采用 *CSS-in-JS* 方法

+   从 *HTML* 到 JSX

+   问题和答案

# 探索 React 的功能

在本节中，我们将从探索 React 在网站开发领域所起的作用开始。通过这样做，我们希望对 React 如何帮助我们有一个更准确的描述。

本书致力于介绍在 *React* 中将状态引入函数组件，特别是钩子机制。我们希望通过阅读本书，你能了解如何正确地设计钩子。

当将 *React* 与其他 UI 框架进行比较时，你是否听说过有时人们将其称为工具库而不是框架？对于什么应该被称为框架以及什么不应该被称为框架并没有明确的定义。根据 *React* 源代码的数量以及它与其它系统的连接方式，我们几乎不能将其视为一个工具库。但与此同时，我们也应该记住 *React* 在一开始就设定的目标，因为了解这一点将帮助你正确地使用这项技术。让我们看看用法：

```js
const rootEl = document.getElementById('root')
ReactDOM.render(<App />, rootEl)
```

以下代码是我们开始所有 *React* 项目的步骤。它搜索一个 *DOM* 元素，并在其下渲染一个组件。此外，在该元素之后，该元素下的屏幕将由 *React* “管理”。这意味着 *React* 会覆盖该元素下的所有内容，并在组件内部任何状态变化时刷新。

因此，前面的 `ReactDOM.render` 方法是将 *React* 集成到你的项目的关键行。正如你所见，这一行非常强大。实际上，它可以在一个项目中多次使用。考虑以下 *HTML* 页面：

```js
<div class="slider" value="3">loading…</div>
<div cla"s="sli"er" val"e""5">loading...</div>
```

我们不想控制整个网站，而是想用功能更丰富的组件替换每个 `slider` 实例：

```js
  const elements = document.querySelectorA'l('.sli'er')
  elements.forEach(el => {
    const value = el.getAttribu'e('va'ue')
    ReactDOM.render(<Slider value={value} />, el)
  })
```

我们可以在 *HTML* 中遍历与 `slider` 实例匹配的元素。对于找到的每个元素，我们取其 `value` 属性并发送到一个使用 `ReactDOM.render` 的组件。哇！所有滑块都由 *React* 管理。

虽然拥有多个 `render` 实例不是一种典型的方法，但我们构建了一个 **单页应用程序** (**SPA**)。这确实给我们一个重要的信息；那就是从技术角度来看，*React* 的目标是替换和管理屏幕上的 *DOM* 元素。为了实现这一点，每个元素都会在内部创建一个新的根纤维，以便在接收到分发时，引擎知道从哪里开始更新。

到现在为止，我们已经知道了 *React* 的设计目的是什么。让我们快速看看完成这个任务需要多少努力——例如，调用 `render` 语句的所有依赖项是什么？

```js
const { render } = require('react-dom')
```

要启动引擎，我们需要由 `react-dom` 包提供的 `render` 函数。组件可以设计在其他地方，这需要 `react` 包的实用工具，如 `createElement` 和 `useState`：

```js
const { createElement, useState } = require('react')
const Slider = () => {
  const [value] = useState(0)
  ...
}
```

除了 `react-dom` 和 `react` 包，我们还需要定位和操作 *DOM* 元素的 `document` 对象。但通常情况下，只要我们有一个浏览器会话，我们就假设 `document` 是可用的。

简而言之，有两个依赖包。`react` 包用于定义组件。使用它，组件定义可以输出为引擎可以理解的格式。`react-dom` 包用于将组件渲染和管理到屏幕上。这意味着在启动引擎之前，你不需要 `react-dom`。大部分的开发时间都花在用 `react` 包定义我们的组件上。从这个练习中，我们应该清楚地看到 *React* 作为实用库的功能。

要用 *React* 建立一个网站，我们需要做所有网络开发者需要做的事情，即用 *HTML* 设计网站布局，用 *CSS* 使网站看起来美观，并用 *JavaScript* 添加业务逻辑和交互来吸引用户。因此，问题变成了所有这些构建块如何适应新的组件生态系统。让我们逐一看看它们。

# 拥抱 JavaScript ES6

*React* 是用 *JavaScript* 语言编写的。*JavaScript* 在过去 20 年中一直在发展。随着在 jQuery、Angular、*React* 和 Node.js 等框架中的应用，*JavaScript* 成为了最受欢迎和最有影响力的编程语言之一。

最新的 *JavaScript* 基于 ECMAScript 2015，也称为 *ES6*。这给 *JavaScript* 带来了一些重要的特性，而 *React* 也迅速很好地采用了这些特性。以下只是其中的一些特性：

+   箭头函数

+   扩展和剩余参数

+   对象增强

+   模板字符串

+   解构

+   `let` 和 `const`

+   模块

+   符号

如果你正在考虑将 *JavaScript* 作为你的主要语言，建议你在空闲时间回顾所有前面的材料，因为它们在 *React* 应用中很常见。我们将在接下来的章节中逐一介绍这些特性。

## 箭头函数

箭头函数是函数表达式的全新语法。一个典型的函数可以用 `function` 关键字定义：

```js
function abc(v) {
}
```

*ES6* 引入了一种更紧凑的方式来定义前面的函数，使用箭头（`=>`）：

```js
const abc = v => {  
}
```

之前的箭头版本省略了单词 `function`，使函数看起来更像一个对象。箭头函数通常用作回调：

```js
arr.map(v => v * 2)
```

之前的代码返回一个新数组，每个元素都加倍。当我们进行 *React* 的调度时，我们也倾向于使用箭头语法：

```js
setCount(v => v + 1)
```

注意，如果箭头函数返回一个单一的表达式，则可以省略 `return` 语句。我们可以将上一行与常规函数形式进行比较：

```js
setCount(function (v) {
  return v + 1
})
```

显然，箭头函数在作为对象工作时往往更紧凑。作为一个对象意味着我们可以通过函数输入参数自由地将其传递到任何地方。有时，你也会看到链式版本，如下所示：

```js
const fn = a => b => a + b
```

上一行等价于以下行：

```js
const fn = (a) => {
  return (b) => {
    return a + b
  }
}
```

你可能会觉得箭头函数非常具有表现力。这本书基本上只使用这种格式。只有在一个情况下，这本书才会回到常规函数格式：

```js
fn()
function fn() {
}
```

通常情况下，我们可以在声明之前调用函数，编译器不会抱怨。然而，这对于箭头函数来说是不行的：

```js
fn()
const fn = () => {
}
```

这是因为 `fn` 需要先声明，然后才能被引用。

与常规函数相比，尽管箭头函数更紧凑、更具表现力，但也存在一些需要注意的问题：

+   它在创建时设置 `this` 对象，而不是在调用时。

+   它在调用后不会设置 `arguments` 对象。

+   它不能用作 `constructor` 函数。

## 展开和剩余

对于 *JavaScript* 语言来说相当新颖，展开是一种表达一个项目所有部分的方式，而剩余则是表达除了前几个部分之外的项目剩余部分的方式。

*ES6* 引入了许多特性，使我们的代码更具表现力。其中一个特性是使用 `...` 关键字，因为几乎没有人会费心去记住确切的特性名称。让我们来看一个例子：

```js
const a = [1, 2, 3, 4, 5]
const b = [...a, 6]
```

在前面的代码中，变量 `b` 从数组 `a` 中获取所有元素，将它们展开成五个元素，然后添加新的元素，使得新的数组总共包含六个元素。你也可以用对象来做这件事：

```js
const a = { first: 'John', last: 'Doe' }
const b = { ...a, age: 23 }
```

在前面的代码中，变量 `b` 从对象 `a` 中获取所有属性，展开它们，然后添加新的 `age` 属性，使得新的对象总共包含三个属性。

注意，这两种用法都创建了一个新的变量，要么是一个数组，要么是一个对象。当涉及到从现有变量快速创建另一个变量时，这种新语法变得相当流行。

*React* 在组件设计中经常使用这个特性：

```js
const App = ({ title, ...props }) => {
  return <div {...props}>Hello</div>
}j
```

之前组件使用 `props` 对象来捕获除 `title` 之外的其他属性列表。这有几个有用的原因：

+   我们不必关心列表中其他属性的数量。

+   我们可以将这个列表发送到子组件中。

另一个与 `spread` 操作符行为相似的 *ES6* 功能是 `rest` 操作符。这是当 `...` 应用于函数的输入参数列表时：

```js
function abc(a, b, ...rest) {  
}
abc(a, b, 1)     // rest = [1]
abc(a, b, 1, 2)  // rest = [1, 2]
```

在前面的 `abc` 函数中，我们使用了 `...` 剩余操作符来获取额外的参数。`rest` 变量包含一个数组，当我们用超过两个参数调用函数时，额外的参数会被填充到这个数组中。

## 对象增强

在 *ES6* 中，对象被扩展以支持相当多的功能。让我们看看其中的一些。其中一个功能是，在给对象属性赋值时，我们可以使用简写版本：

```js
const first = "John"
const last = "Doe"
const name = { first, last }
```

如果我们用旧的方式编写前面的代码，它将等同于以下代码：

```js
const name = { first: first, last: last }
```

如果属性名与赋值变量名匹配，*ES6* 允许我们只写一次属性名。这在实践中非常有用，因此，人们常常通过创建一个临时变量来匹配属性名来利用这一点：

```js
const onClick = () => {
  const first = "John"
  const name = { first }
}
```

对象的另一个增强是支持将表达式用作属性名：

```js
const name = e.target.name
const value = e.target.value
return { [name]: value }
```

前面的代码是我们用来支持表单提交的代码。不要将那段代码的最后一行与以下版本混淆：

```js
return { name: value }
```

注意，这两个之间的区别在于 `name` 周围的括号。如果没有括号，`name` 将会将值赋给 "`name`" 属性。然而，在 `[name]` 版本中，它将会将值赋给一个名为 `name` 变量中存储的属性名。如果 `name` 包含了 "`first`" 这个词，那么 `[name]` 版本将变成以下这样：

```js
return { first: value }
```

基本上，这使得我们可以将任何 *JavaScript* 表达式用作属性键。在此之前这是不可能的，所以我们通常编写以下代码来解决这个问题：

```js
  obj[name] = value
```

## 模板字符串

在 *JavaScript* 中构建长字符串而不会牺牲字符串的原始格式曾经很麻烦。为了解决这个问题，*ES6* 添加了模板字符串，允许字符串被反引号（`'`）包围，这是我们处理 *Markdown* 文件时经常使用的奇怪字符。使用这种语法，字符串可以按照以下方式编写：

```js
'This is a string'
```

模板字符串有趣的地方，除了使用 `"` 或 `'` 的常规引号字符串之外，还支持跨多行编写的文本段落，同时保留换行符：

```js
'
This 
is
a 
string
'
```

这使得在不需要格式化内容的情况下引入一段长文本变得很有用。模板字符串也支持任何 *JavaScript* 表达式：

```js
const what = "This"
const str = '
${what}
is 
a
string
'
```

在前面的代码中，我们将模板中的 `${what}` 替换成了 `"This"` 字符串。本质上，`${}` 内部可以写任何 *JavaScript* 表达式。

模板字符串的一个常见用法是将另一种格式转换为字符串：

```js
const n = 3
const str = '${n}'
const url = 'http://foo.org/bar?id=${n}'
```

你可以在前面的代码中看到，当我们需要组装一个如动态 `url` 这样的字符串时，这变得非常有用。

我们可以将前面的代码应用于更复杂的情况，例如*CSS*内容：

```js
const color = 'red'
const Button = css'
  display: inline-block;
  color: ${color};
'
```

前面的代码使用了一种不同的形式，称为标签模板，`css`被称为标签函数。这里还有一个例子：

```js
function image(strings) {
  return '<img src="img/${strings[0]" />'
}
const s = image'
  http://google.com
'
```

前面的代码定义了一个`image`标签函数，它将`strings`转换为另一种格式——在我们的例子中，是一个图像元素声明。从这一点可以看出，标签模板可以作为工具，减轻生成字符串的痛苦。在接下来的*Adopting CSS-in-JS*方法部分，我们将看到更多这样的例子。

## 解构赋值

“解构赋值”这个词听起来有些别扭，也许我们甚至找不到它在字典中的条目。

然而，你可能过去已经多次使用过这个特性：

```js
const arr = [1, 2, 3]
const [a, b] = arr
```

实际上，解构赋值允许你解构一个对象，并将拆分的元素分配给变量。前面的代码是应用于数组的解构赋值用法，相当于以下代码：

```js
const a = arr[0]
const b = arr[1]
```

你可以看到，在解构过程中，我们遵循给定的结构来选择所需的元素。同样，它也可以应用于对象：

```js
const name = { first: 'John', last: 'Doe' }
const { first, last } = name
```

前面的声明可以翻译为以下内容：

```js
const first = name.first
const last = name.last
```

虽然我们还可以继续使用旧方法，但解构赋值使用起来要容易得多，也快得多。请记住，你选择的元素必须存在；否则，你可能会得到一个`undefined`，如下面的例子所示：

```js
const name = { first: 'John' }
const { second } = name
```

解构赋值的一个有用特性是，如果你想用另一个名字存储属性，你可以重命名它：

```js
const { first: firstName } = name
```

前面的行可以翻译为以下内容：

```js
const firstName = name.first
```

实际上，`firstName`变量用于赋值，而不是`first`，尽管`first`是对象下的属性名。

解构赋值经常与其他*ES6*特性结合使用，例如扩展运算符：

```js
const { first } = { ...name, last }
```

我们在*React*代码中经常看到这个前面的声明，所以让我解释一下它的每个部分：

+   `...name`是将`name`对象的全部属性展开。

+   `last`是增强的对象语法，用于添加`last`属性。

+   `{ ...name, last }`是从现有对象中创建一个新的对象。

+   `first`是从新对象中解构出`first`属性。

哇，这里有很多事情在进行中！也许我们可以用旧方法写一个等效版本：

```js
const temp = Object.assign({}, name)
temp.last = last
const first = temp.first
```

实际上，新语法只需要一行代码，而不是三行，但令人惊讶的是，结果要准确得多，表达也更丰富。

如果你对这个特性还不熟悉，不要犹豫，在编写每个部分时放慢一点速度。这样做的原因是，你不仅会了解新语法提供了什么知识，而且也不会不小心错过任何逻辑。

## let 和 const

在过去使用*JavaScript*时，你可能对`var`、`let`和`const`关键字感到困惑。你应该使用哪一个来声明一个变量？

首先要明确的一点是，`var` 已经不再常用；它主要存在是为了向后兼容，因为它使用了一个奇怪的作用域规则。相反，我们应该都使用 `let` 和 `const`，因为它们基于块作用域，这是开发者更习惯使用的：

```js
function abc() {
  let a = 1
  if (true) {
    const b = 2
    for (let i = 1; i < 3; i++) {
      ...
    }
  }
}
```

在前面的代码中，`a` 变量存在于函数的作用域中，而 `b` 变量存在于条件语句的作用域中。这两个引用在由 `{}` 父包围块指定的作用域内都是有效的。如果你试图在它们定义的作用域之外引用 `a` 或 `b`，编译器现在会抛出一个错误。同样适用于使用 `let` 在循环中定义的 `i` 变量。如果我们使用 `var`，它会使 `i` 在整个函数体中可访问。

正如名称所暗示的，你需要使用 `let` 来表示稍后需要改变的变量，而使用 `const` 来表示你预期不会改变的项。以下是一个例子：

```js
let x = 5
x = 6            // valid
const n = "abc"
n = "def"        // compiler error
```

在前面的代码中，如果你稍后更改 `x` 为 `6`，这是完全正常的。但是，如果你尝试将 `n` 更改为 `"def"`，编译器会抛出一个错误。这适用于任何原始值，如数字或字符串。

当涉及到非原始值，如对象或数组时，事情会变得有些复杂：

```js
let obj = { a: 1, b: 2 }
const obj = { a: 1, b: 2 }
```

你能区分前两行之间的区别吗？什么是变量对象，什么是常量对象？

如果一个对象是用 `const` 声明的，这意味着只能使用该对象本身来指向另一个内存空间：

```js
const obj = {}
obj = {}      // compiler error
obj.key = ''  // valid
```

在前面的代码中，我们定义了一个 `obj` 常量。稍后，如果我们尝试重写 `obj`，编译器会抛出一个错误。然而，如果我们通过键修改其内容，它仍然是有效的。同样，我们可以期待数组有类似的行为：

```js
const arr = []
arr = []      // compiler error
arr.push('A') // valid
```

你可能会觉得这种行为有点奇怪，但实际上，它在所有对象和数组初始化、比较等方面设计得相当一致。

如果你看到用 `let` 语句定义的数组或对象，它应该告诉你它们可以在稍后重写：

```js
let obj = {}
let arr = []
```

这是 `let` 和 `const` 语句之间的主要区别。

## 模块

现在的开发者无法离开的一个东西就是从另一个文件导入的代码。然而，*JavaScript* 直到 *ES6* 才引入了一个类似于 *AMD* 或 *CommonJS* 这样的模块系统。其理念是我们可以从一个文件中导出一些内容以供重用，如下例所示：

```js
const Title = () => <div>Hello World</div>
export default Title
```

前面的代码在 *React* 应用程序中相当常见。基本上，它在一个单独的文件中定义了一个 `Title` 组件，并将其作为默认值导出。从另一个文件中，我们可以通过 `import` 导入它并使用它，如下所示：

```js
import Title from './Title'
const App = () => <Title />
```

这是将所有文件拉在一起并编译成单个 `index.js` 文件的主要机制，因为它实际上导入了所有这些文件。

有时，我们还想与 `default` 一起导出其他材料：

```js
const Title = () => <div>Hello World</div>
export default Title
const TitleType = "Component"
export { TitleType }
```

在前面的代码中，我们将`Title`导出为`default`，同时将`TitleType`作为非默认导出。这意味着我们可以挑选事物并单独导入它们：

```js
import Title, { TitleType } from './Title'
```

当我们在单个文件中存储大量相关实用功能并将它们逐个导出时，这种能力变得非常方便：

```js
const fn1 = () => {}
const fn2 = () => {}
export { fn1, fn2 }
```

为了避免名称冲突，在导入时，我们可以使用`as`关键字进行别名导入：

```js
Import { fn1 as aliasFn1 } from './fns'
// we can use aliasFn1
```

## 符号

符号是一个听起来像游戏术语的新*JavaScript*特性。实际上，它是一种特殊的原始类型。通常，我们可以将字符串作为属性键：

```js
obj['name'] = 3
```

任何编写上述行的人都可以访问存储在`name`属性下的值。但如果我们只想限制只有知道钥匙的人才能访问，你会问：“你这是什么意思？`"name"`字符串不是对任何人都是公开的吗？”这正是我们要表达的意思——我们想要创建一个不是每个人都能轻易重新创建的钥匙。以下是我们将要做的：

```js
const Name = Symbol('name')
obj[Name] = 3
```

在前面的代码中，创建了一个带有"`name`"字符串的`Symbol`作为钥匙。如果我们有确切的钥匙，我们仍然可以访问`obj`下的这个符号化属性。但如果你创建另一个类似的钥匙并尝试访问属性，那么它就不会起作用，如下所示：

```js
const Name = Symbol('name')
obj[Name] = 4
```

嗯，我们不是又用同样的代码了吗？为什么我们无法访问相同的键？答案在于以下比较：

```js
Symbol('name') !== Symbol('name')
```

两个`Symbol('name')`语句之间的比较返回`false`！这意味着你不能期望通过再次编写它来重新创建`"name"`键；你只能拥有最初创建的原始键，或者通过`Symbol.for`从系统中所有注册的键中查询它：

```js
Symbol.for('name') === Symbol.for('name')
```

一旦我们在系统中找到该键，我们就可以访问该属性：

```js
const Name = Symbol.for('name')
obj[Name] = 4
```

哎。这很有趣，不是吗？

符号是一个无法重新创建的原始类型，并且保证是唯一的！这意味着它可以作为一个独特的门钥匙。如果钥匙不存在，那么门就无法打开。

当我们不希望任何开发者意外访问某些内存或分配值时，这很有用。它还可以以独特的方式将此键转移到另一个项目或仓库中。我将在下一节中展示*React*如何使用它的一个例子。

到目前为止，我们已经讨论了*ES6*的一些主题，包括箭头函数、对象增强、模板字符串、解构、`let`和`const`、模块和符号。但实际上*ES6*还有更多，比如 promise、proxy、generators 和 weak map。当我们提到 API 时使用了 promise，在*第九章*中使用了 proxy，*使用自定义* *Hooks 来重用逻辑*。

一件事可以肯定的是，*React*确实充分利用了最新的*JavaScript*语言。如果你经常练习，你会发现代码变得更加表达，同时不失其准确性。

在下一节中，我们将看到*React*如何使用来自非*JavaScript*语言的材料，例如*CSS*。

# 采用 CSS-in-JS 方法

当我们在 *React* 中构建应用程序时，在某个时候，我们需要找到一种方法来将 *CSS* 样式应用到我们的代码中，对吧？如果你过去有 *CSS* 经验，我们熟悉的一种方法是使用 *CSS* 类来设置样式。考虑以下存储在具有 .`css` 扩展名的文件中的 *CSS* 片段：

```js
h1 {
  color: red;
}
```

我们可以通过一个名为 `className` 的属性将此样式应用到 *React* 元素上：

```js
const Title = () => {
  return <h1 className="title">Hello</h1>
}
```

注意

`class` 是 *JavaScript* 的保留关键字，因此不能使用。相反，*React* 选择使用不同的单词，`className`。

虽然这个旧方法仍然有效，但关于 `title` 类的唯一性问题。*CSS* 可以应用到屏幕上所有的 `title` 元素，但它不能跳过我们不想被样式的组件内部的元素。从 *CSS* 的角度来看，`title` 类被暴露出来以进行全局样式化。

使用组件设计，我们自然会想要只将样式应用到这个组件上！例如，我们定义的 `title` 样式只能在 `Title` 组件内有效。信不信由你，有作用域是组件设计的基础。

使样式对组件独特的一种方法是将样式内联与元素一起使用 `style` 属性：

```js
const Title = () => {
  return <h1 style={{ color: "red" }}>Hello</h1>
}
```

但前面硬编码方法的问题也很明显。直接在内联中编写大量样式是不切实际的。这正是 *CSS-in-JS* 方法发挥作用的地方。想法是利用模板字符串使用旧方法编写 *CSS*。好吧，我们刚刚从 *ES6* 的一个特性中介绍了模板字符串：

```js
const css = '
  h1 { color: red; }
'
```

使用前面字符串的学习曲线几乎不存在。因此，这种方法很快被社区采用，并被几个库实现，例如 `styled-JSX` 和 `styled-components`。我们将在本节中介绍这两个库，让我们开始吧。

## styled-SX

`styled-JSX` 是一个 *CSS-in-JS* 库，它允许我们为组件编写作用域内的 *CSS*，这样样式就不会影响到其他组件，从而允许我们修改样式而不用担心会影响到屏幕上的其他组件。

让我们用相同的 `Title` 组件来看看如何使用 `styled-JSX` 来实现：

```js
const Title = () => (
  <>
    <h1>Hello</h1>
    <style jsx>{'
      h1 { color: red; }
    '}</style>  
  </>
)
```

它引入了一个特殊的 `style` 标签，带有 `jsx` 属性，在其下方，样式可以写成 *CSS* 代码。即使 `Title` 组件被多次使用，样式也只注入一次。

当涉及到原型设计应用程序时，这种半内联的样式方法可以非常高效。我们不必将 *CSS* 与组件内联，我们可以将样式放在一个单独的文件中，如下所示：

```js
import css from 'styled-jsx/css'
export default css'
  h1 {
    color: red; 
  }
'
```

我们可以将其导入到 `Title` 组件中：

```js
import titleStyle from '../titleStyle'
const Title = () => (
  <>
    <h1>Hello</h1>
    <style jsx>{titleStyle}</style>  
  </>
)
```

这给了我们一些自由来编写 CSS，无论是内联还是单独的文件。

有一个重要的事情需要注意，我们迄今为止编写的样式默认情况下不适用于子组件：

```js
const Child = () => <span>World</span>
const Title = () => (
  <>
    <h1>Hello <Child /></h1>
    <style jsx>{'
      span { color: green; }
    '}
  </>
}
```

上述代码不会使任何 `span` 元素以绿色颜色显示。为了使其工作，我们可以使用一个 `global` 属性：

```js
const Child = () => <span>World</span>
const Title = () => (
  <>
    <h1>Hello <Child /></h1>
    <style jsx global>{'
      h1 { color: red; }
      span { color: green; }
    '}
    </style>  
  </>
)
```

`styled-JSX` 是一个相当独特的库，它使得在 *React* 中进行样式设计变得简单。此外，由于样式实际上是用 *JavaScript* 字符串编写的，因此可以在运行时进行操作。我们将在下一个包 `styled-components` 中展示这一功能，因为该功能适用于这两个包。

## styled-components

另有一个名为 `styled-components` 的库实现了 *CSS-in-JS* 策略。本书为所有需要样式的示例采用了这种方法。它不是使用 `style` 标签，而是实际上允许我们在单独的组件中定义 *CSS*：

```js
import styled from 'styled-components'
const TitleStyle = styled.h1'
  color: red;
'
const Title = () => {
  return (
    <TitleStyle>
      Hello World
    </TitleStyle>
  )
}
```

在上述代码中，`styled.h1` 是一个标签模板函数，它输出一个包含嵌入式 `h1` 元素的组件。

此外，`styled-components` 允许 *CSS* 默认应用于子元素，这与 `styled-JSX` 包不同：

```js
const TitleStyle = styled.h1'
  color: red;
  span { color: green; }
'
const Child = () => <span>World</span>
const Title = () => {
  return (
    <TitleStyle>
      Hello <Child />
    </TitleStyle>
  )
}
```

上述代码设置了 `span` 元素的绿色颜色。这让我们想起了 Sass 或 SCSS 代码。

*CSS-in-JS* 解决方案提供的一个有趣的好处是，*JavaScript* 字符串可以与其他 *JavaScript* 表达式混合，以支持运行时动态样式：

```js
const fontSize = (props) => 
  props.big ? '3em' : '1.5em'
const TitleStyle = styled.h1'
  font-size: ${fontSize};
'
const Title = ({ big }) => {
  return (
    <TitleStyle big={big}>
      Hello World!
    </TitleStyle>
  )
}
```

上述代码定义了一个 `big` 属性，可以发送到 `TitleStyle` 组件，根据 `big` 的值，`fontSize` 可以在 `3em` 和 `1.5em` 之间切换。

动态 *CSS* 支持使得在 *React* 中轻松创建可主题化的组件成为可能，同时也为需要随时间变化值以进行动画的组件打开了大门。

既然我们已经看到了 *React* 如何将 *CSS* 带入 *JavaScript* 以融合这两种技术，那么让我们继续探讨 *HTML*，看看它如何被引入 *React*。

# 从 HTML 到 JSX

对于一个开发者来说，采用 *React* 是一个障碍，尤其是如果他们习惯于使用其他网络技术的话。因为 *HTML* 已经不再被导入到项目中了。除了入口文件 `index.html`，没有其他带有 `.html` 扩展名的文件，而这个入口文件通常只包含一行 *HTML*，如下所示：

```js
<div id="root">loading...</div>
```

但仅此而已。如果有一个项目经理喜欢审查 *HTML* 或甚至参与其中，他们现在不能再这样做。缺少的 *HTML* 文件可能是团队犹豫采用 *React* 的原因之一。

然而，*HTML* 与 *CSS* 有着类似的问题。它们在编程语言中拥有的作用域在这里并不存在。更糟糕的是，一旦写好一段 *HTML*，几乎会立即转换为 *DOM* 元素。因此，基于组件的系统必须想出一种方法在中间添加一层。

*React*是如何解决这个问题？它选择了接受*HTML*，这并不令人惊讶。但*React*所做的令人惊讶的工作之一是，它使编写这些语句的体验尽可能接近*HTML*。技术上，*React*使将一段*HTML*转换成一段*JavaScript*代码的平滑转换成为可能。有时，我们并没有注意到这一点，也无法区分它们：

```js
  return <h1 title="Title">Hello World</h1>
```

上述代码是我们在一个函数组件内部使用的。格式看起来像一段*HTML*。编译器看到的是以下内容：

```js
  return React.createElement(
    'h1', { title: "Title" }, "Hello World"
  )
```

在编译器转换之后，它变成了一个`createElement`函数的*JavaScript*表达式，该函数接受三个输入参数。

这种转换发生的魔法是通过一个带有*Babel*插件的编译器完成的。当编译器构建代码时，Babel 将代码转换成抽象语法树、标记格式，然后将它们重新拼接并放入*JavaScript*格式中。

注意

如果你对了解编译器做了什么感兴趣，可以访问[`babeljs.io/repl`](https://babeljs.io/repl)并亲自尝试。

我们现在将仔细研究这个`createElement`函数。`createElement`函数的第一个输入参数是元素的`type`，它来自元素标签`h1`。第二个是`props`，它是一个包含`title`属性的对象。最后但同样重要的是，第三个是`children`；在我们的例子中，它是一个包含`"Hello World"`的字符串。让我们更详细地了解一下这些输入参数，因为它们都是*React*的必要组成部分！

## 属性

`props`输入参数是*React*最重要的机制之一。我们一直在谈论属性，这就是它们接收第一手值的地方。这是因为`createElement`是一个*JavaScript*函数，我们可以将任何*JavaScript*表达式连接到属性上：

```js
const Title = ({ title }) => {
  const [count] = useState(0)
  return React.createElement(
    'h1', { title, count }, "Hello World"
  )
}
```

在上述代码中，现在很明显，`title`属性和`count`状态是如何作为属性的一部分发送给`createElement`的。

这也解释了为什么我们不能使用`class`保留字作为属性来样式化组件：

```js
// code below wouldn't work
const Title = () => {
  return React.createElement(
    'h1', { class: "title" }, "Hello World"
  )
}
```

上述代码会因为`class`键而引发编译器错误。

## 子元素

`children`输入参数是我们可以在一个元素下嵌套另一个元素的原因：

```js
  return (
    <h1>
      <span>
        Hello World
      </span>
    </h1>
  )
```

编译器看到的是以下内容：

```js
  return React.createElement(
    'h1', null, React.createElement(
      'span', null, "Hello World"
    )
  )
```

注意从上述输出中可以看到，`createElement`有两种用法，一个用于`h1`元素，另一个用于子`span`元素。第二个`createElement`函数被用作第一个`createElement`函数的`children`参数。

现在，不难看到通过`createElement`函数从组件中返回的嵌套元素的长列表。技术上，即使没有编译器的帮助，你也可以手动使用这种嵌套编写代码。

尽管单个元素可以嵌套在下面，但有时可能需要将多个元素嵌套在另一个元素下面：

```js
  return (
    <ul>
      {['Apple', 'Orange'].map(v => <li>{v}</li>)}
    </ul>
  )
```

对于前述涉及数组的*JavaScript*表达式，该数组将被发送到`createElement`的`children`：

```js
  return React.createElement(
    'ul',
    null,
    ['apple', 'orange'].map((v) =>
      React.createElement('li', null, v)
    )
  )
```

实际上，`children`输入参数可以支持以下格式：

+   一个`"Hello World"`字符串

+   通过`createElement`创建单个元素

+   以前述格式之一为格式的元素数组

*React*还允许你以另一种方式添加任意数量的子元素：

```js
  return React.createElement(
    'ul',
    null,
    React.createElement('li', null, 'apple'),
    React.createElement('li', null, 'orange')
  )
```

在前述代码中，我们可以从第三个输入参数开始，在输入参数列表中堆叠子元素。

## 元素类型

元素类型，`createElement`的第一个输入参数，可以是一个简单的字符串，表示一个*HTML*标签，例如`h1`和`span`。然而，它也可以采用其他格式，例如一个函数：

```js
const Child = () => <h1>Hello World</h1>
const Title = () => {
  return <Child />
}
```

编译器看到的前述代码如下：

```js
const Child = () =>
  React.createElement("h1", null, "Hello World")
const Title = () => React.createElement(Child, null)
```

注意前述代码中的`Child`——函数本身被发送到`createElement`作为元素类型！这就是*React*允许你从自定义组件创建元素的方式。正因为如此，这意味着我们可以在运行时支持动态元素类型：

```js
const Child1 = () => <h1>Hello</h1>
const Child2 = () => <h1>World</h1>
const Title = ({ flag }) => {
  const Child = flag ? Child1 : Child2
  return <Child />
}
```

在前述代码中，我们不是返回`<Child1 />`或`<Child2 />`，而是首先确定`Child`组件的类型，然后从这个`<Child />`组件返回实例。这一切都是因为组件类型是一个*JavaScript*变量：

```js
const Child1 = () => React.createElement(
  "h1", null, "Hello"
)
const Child2 = () => React.createElement(
  "h1", null, "World"
)
const Title = ({ flag }) => {
  const Child = flag ? Child1 : Child2
  return React.createElement(Child, null)
}
```

前述代码确认了编译器看到的内容。`Child`组件类型是在运行时确定的。

现在我们已经看到了`createElement`函数及其三个输入参数，让我们仔细看看它返回的内容。

## React 元素

如`createElement`的名称所示，该函数返回一个带有其下元素树的根元素。

这个元素是*DOM*元素吗？不，它被称为*React*元素。尽管*React*文档没有过多地谈论它，但我们将在这里简要地揭示它：

```js
{
  $$typeof: Symbol.for('react.element'),
  type: type,
  props: props,
}
```

前述代码是*React*元素定义的简短定义。正如你所见，它基本上是一个对象。我们已经介绍了`type`和`props`，但`$$typeof`是什么东西呢？显然，这不是*React*希望任何人都能篡改的东西。

事实证明，*React*支持各种元素类别。由于没有更好的词，让我们把`$$typeof`称为另一种类型，即内部类型。我们最常用的一个是`react.element`，它被定义为`Symbol`。我们之前已经介绍了`Symbol`。基本上，`react.element`是一个独特的原始值，一旦创建，就可以使用但不能更改。

你可能会问，“还有其他类型吗？”实际上，有二十多种；这里只是列出一些可能对你感兴趣的：

+   `react.element`

+   `react.portal`

+   `react.fragment`

+   `react.provider`

+   `react.suspense`

+   `react.memo`

+   `react.lazy`

为什么我们需要所有这些不同的类型？

大多数应用程序实现或多或少都是围绕更新`react.element`进行的，但当涉及到特殊情况时，它需要一个不同的更新算法。让我们看看以下示例：

```js
import { memo } from 'react'
const App = memo(() => {
  return (
    <Provider value={3}>
      <>
        <div>One</div>
        <div>Second</div>
      </>
    </Provider>
  )
})
```

在前面的代码中，`Provider` 创建了一个具有 `react.provider` 类型的元素。在其下方，`<>` 创建了一个具有 `react.fragment` 类型的元素，再下面，`div` 创建了两个具有 `react.element` 类型的元素。整个组件被一个具有 `react.memo` 类型的 `memo` 元素包裹。这给你一个大致的概念，了解不同的 *React* 元素在哪里被使用。

简而言之，从 JSX 代码块返回的是 *React* 元素。这些 *React* 元素是输入到 *React* 引擎中的内容。

# 摘要

在本章中，我们从 *React* 在 Web 开发中的作用开始。我们探讨了它的三个方面。首先，我们了解了 *JavaScript* 的最新 *ES6* 功能，如箭头函数和模板字符串。接下来，我们学习了将 *CSS* 带入 *JavaScript* 的 *CSS-in-JS* 方法，使用如 `styled-JSX` 和 `styled-components` 这样的库。最后，但同样重要的是，我们学习了如何将类似 *HTML* 的 JSX 代码转换并返回为 *React* 元素。总的来说，我们看到了 *React* 如何将这些资源（包括 *JavaScript*、*CSS* 和 *HTML*）整合在一起，帮助我们构建网站。

# 问题和答案

以下是一些问题和答案，以帮助你巩固知识：

1.  什么是 *React*？

    这是一个允许我们使用渲染引擎设计和更新组件的工具。

1.  什么是 *JavaScript ES6*？

    *JavaScript* 包含了作为 *ES6* 发布的所有最新功能。*React* 利用这些功能，使用箭头函数、模板字符串和结构化等特性。在项目中使用它们可以使你的代码更高效、更易于表达和维护。

1.  什么是 *CSS-in-JS*？

    *CSS-in-JS* 指的是一种有见地但流行的将样式应用于 *React* 组件的方法。应用的样式仅限于组件，不会与其他组件冲突。此外，样式可以与任何 *JavaScript* 表达式连接，以支持运行时动态样式。

1.  JSX 代码是什么？

    *React* 允许我们使用 JSX 代码编写类似 *HTML* 的代码。实际上，它们看起来非常相似，只不过 JSX 允许我们将这些语句转换为接受元素类型、属性和子元素从输入参数的本地 *JavaScript* 表达式，并返回引擎可以生效的 *React* 元素。
