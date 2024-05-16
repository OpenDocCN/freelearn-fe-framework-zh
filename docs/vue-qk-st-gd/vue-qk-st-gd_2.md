# 第二章：Vue 2 的基本概念

在本章中，我们将讨论 Vue 中的数据驱动视图。我们还将研究如何使用指令来操作 DOM。接下来，我们将学习组件是什么以及如何创建它们，并涵盖与模板、方法、数据、计算属性和观察者相关的概念。

所有组件都有一个生命周期，并且我们有特殊的方法在其生命周期的某些时刻访问组件。这些方法被称为**生命周期钩子**，我们也将在本章中对它们进行讨论。

在本章中，我们将学习以下内容：

+   Vue 中的数据驱动视图

+   计算属性和方法以及如何使用它们

+   理解组件、模板和属性

+   在 Vue 中构建组件模板的方式

+   使用 Vue 组件和`v-*`指令快速原型网站

+   在 Vue 中利用观察者

+   生命周期钩子的重要性以及如何在 Vue 中插入这种功能

# Vue 中的数据驱动视图

Vue 中的数据驱动视图是通过响应性来实现的。

# 什么是响应性？

为了更好地理解这个概念，让我们看一个例子代码，其中没有响应性。我们将使用一个非常类似于我们在上一章中比较 Vue 和原生 JS 时的例子。在原始的例子中，使用 JavaScript，我们创建了一个无序列表，里面有三个列表项。三个列表项的值是从我们声明的数组中添加的，并且使用 for 循环将无序列表填充了这些列表项。

这一次，我们将做一些稍微不同的事情。要将示例作为一个 pen 查看，请访问[`codepen.io/AjdinImsirovic/pen/JZOZdR`](https://codepen.io/AjdinImsirovic/pen/JZOZdR)。

在这个非响应式的例子中，我们预先定义了数组的成员作为变量。然后我们用这些变量填充数组，并将它们作为无序列表的列表项打印到屏幕上，这个无序列表被附加到文档中：

```js
var a = 1;
var b = a + 1;
var c = b + 2;
var arr1 = [a,b,c];
var unorderedList = document.createElement('ul');
document.body.appendChild(unorderedList);
for (var i=0; i<3; i++) {
  var listItem = document.createElement('li');
  listItem.className = "list-item";
  unorderedList.appendChild(listItem);
  listItem.innerHTML = arr1[i];
}
arr1[0] = 2;
for (var i=0; i<3; i++) {
  var listItem = document.createElement('li');
  listItem.className = "list-item";
  unorderedList.appendChild(listItem);
  listItem.innerHTML = arr1[i];
}
```

然而，当我们改变数组的一个成员并再次重复 for 循环时会发生什么？正如我们在代码中所看到的，第一个和第四个列表项是不同的。第一个值是`1`，第二个值是`2`。为了更明显，这些项目是以粗体红色文本和灰色背景显示的。第一个值是`var a`的初始值。第二个值是使用这行代码更新的`var a`的值：`arr1[0] = 2`。

然而，变量`b`和`c`的值在第二个 for 循环中没有更新，尽管我们已经分别定义了变量`b`和`c`，它们分别是变量`a`增加 1 和 2。

因此，我们可以看到 JavaScript 本身并没有响应性。

就 Vue 而言，响应性是指 Vue 跟踪变化的方式。换句话说，响应性是指状态变化如何在 DOM 中反映出来。实际上，这意味着当对`data`进行更改时，该更改将传播到页面，以便用户可以看到它。因此，说*Vue 是响应式*就等同于说*Vue 跟踪变化*。作为一个概念，就是这么简单。

# Vue 是如何实现这一点的呢？

Vue 将其数据存储在`data`选项中，这可以是一个函数或一个对象：

```js
...
data: {
  // the model goes here
}
...
```

`data`模型的任何变化都会反映在视图（屏幕）上。Vue 通过 getter 和 setter 实现了这种响应性。当`data`对象被 Vue 实例接收时，`data`对象的所有属性都将被更新为 getter 和 setter。这是通过`Object.defineProperty`API 来实现的。

# 计算属性和方法

Vue 中响应性的实用性可以用计算属性和方法之间的区别来描述。

正如我们之前提到的，Vue 实例可以有计算属性、方法，或者计算属性和方法两者兼有。那么，这两者之间有什么区别呢？

方法只是在被调用时运行。另一方面，计算属性是被缓存的，这意味着它们只在基础数据模型发生变化时才会运行。这通常是以计算属性的依赖关系来描述的。此外，方法可以有参数，而计算属性则不行。

# 这些依赖关系到底是什么？

考虑这个简单的 Vue 应用程序，可以在这个链接中找到：[`codepen.io/AjdinImsirovic/pen/qKVyry`](https://codepen.io/AjdinImsirovic/pen/qKVyry)。

这是这个简单应用程序的代码：

```js
<!--HTML-->
<div id="example">
  <p>Enter owner name and the thing that is owned:
    <input v-model="ownerName" placeholder="enter owner">
    <input v-model="thing" placeholder="enter thing">
  </p>
  <span>{{ ownerName }}</span>
  <span> has a </span>
  <span>{{ thing }}</span>
</div>

// JS
var example = new Vue({
  el: '#example',
  data: {
    ownerName: 'e.g Old McDonald',
    thing: 'e.g cow'
  },
  computed: {
    // a computed getter
    ownerHasThing: function () {
      // `this` points to the Vue instance's data option
      return this.ownerName + " " + this.thing
    }
  }
})
```

这段代码将在屏幕上产生这样的输出：

![](img/63e6e381-cd03-4ea3-a4d6-3225799a5d85.png)

首先，我们可以看到视图中有这样一个奇怪的“has a”文本行。问题在于我们没有使用我们的`ownerHasThing`计算属性。换句话说，HTML 中的这三行是完全多余的：

```js
<span>{{ ownerName }}</span>
<span> has a </span>
<span>{{ thing }}</span>
```

另外，如果我们只想在两个输入字段都填写完毕并且焦点已经移出输入框，或者按下*Enter*键后才运行计算属性，该怎么办呢？

这可能看起来是一个相对复杂的事情。幸运的是，在 Vue 中非常容易实现。

让我们来看一下更新后的代码（也可以在这里找到：[`codepen.io/AjdinImsirovic/pen/aKVjqj`](https://codepen.io/AjdinImsirovic/pen/aKVjqj)）：

```js
<!--HTML-->
<div id="example">
  <p>Enter owner name:
    <input v-model.lazy="ownerName" placeholder="enter owner">
  </p>
  <p>Enter thing owned:
    <input v-model.lazy="thing" placeholder="enter thing">
  </p>
  <h1 v-if="ownerName && thing">{{ ownerHasThing }}</h1>
</div>
```

JavaScript 代码只有轻微的不同：

```js
var example = new Vue({
  el: '#example',
  data: {
    ownerName: '',
    thing: ''
  },
  computed: {
    // a computed getter
    ownerHasThing: function () {
      // `this` points to the Vue instance's data option
      return this.ownerName + " has a " + this.thing
    }
  }
})
```

我们可以得出结论，计算属性只是一些数据依赖关系，对它们进行了一些计算。换句话说，`ownerHasThing`是一个计算属性，它的依赖是`ownerName`和`thing`。

每当`ownerName`或`thing`发生变化时，`ownerHasThing`计算属性也会更新。

然而，`ownerHasThing`不会总是更新，因为它被缓存了。相反，方法总是会更新；也就是说，它总是会运行，无论数据模型是否发生了变化。

这可能看起来不是非常重要的区别，但考虑一种情况，你的方法需要从第三方 API 获取数据，或者有很多代码需要运行。这可能会减慢速度，这就是为什么在这种情况下，使用计算属性是正确的方法。

在我们结束本节之前，让我们快速回顾一下之前示例中的代码。

在 HTML 中，我们使用了`v-model.lazy`。`lazy`修饰符会等待用户要么点击输入框外部，要么按下键盘上的*Enter*键，或者离开输入框（比如按下*Tab*键）。

在 HTML 中，我们还使用了`v-if`指令，并给它传递了`ownerName && thing`。然后，我们添加了双大括号模板：`{{ ownerHasThing }}`。`v-if`指令会等待直到`ownerName`和`thing`在数据对象中更新。因此，一旦两个输入框都填写完毕并且不再聚焦，计算属性才会更新底层数据模型，然后才会在屏幕上打印出`{{ ownerHasThing }}`消息。

在下一节中，我们将看看如何使用模板和组件。

# 理解组件、模板和 props

首先，让我们看看如何在 Vue 中创建一个组件。首先，我们像这样指定组件：

```js
Vue.component('custom-article', {
  template: `
    <article>
      Our own custom article component!<span></span>
    </article>`
})
new Vue({
    el: '#app'
})
```

组件是一段我们给予自定义名称的代码块。这个自定义名称可以是我们想到的任何东西，它是*整个代码块的一个单一标签*，以自定义 HTML 标签的形式。在前面的例子中，我们将`article`和`span`标签分组，并给该自定义标签命名为`custom-article`。

组件使用 kebab-case 命名。

这个组件的代码可以在 Codepen 上找到：[`codepen.io/AjdinImsirovic/pen/xzpOaJ`](https://codepen.io/AjdinImsirovic/pen/xzpOaJ)。

现在，要创建我们组件的一个实例，我们只需在我们的 HTML 中使用`<custom-article>`开放和关闭标签，就像这样：

```js
<main id="app">
    <custom-article></custom-article>
</main>
```

我们的 custom-article 组件被称为*子*组件。

父级是实际的 Vue 实例。

请注意，即使没有组件，您也可以使用字符串模板。您只需将模板选项添加到您的 Vue 实例中，就像这样：

```js
//HTML
<main id="app"></main>
//JS
new Vue({
  el: '#app',
  template: '<article>A string template without a component!<span></span></article>'
})
```

前面例子的示例代码可以在这里找到：[`codepen.io/AjdinImsirovic/pen/RJxMae`](https://codepen.io/AjdinImsirovic/pen/RJxMae)。

接下来，我们将看到如何通过`props`和`data`选项来改进我们的组件。

# 添加 props 和 data 以改进组件

为了使我们的`custom-article`组件更有用，我们将向其添加一个`props`选项，就像这样：

```js
Vue.component('custom-article', {
  props: ['content'],
  template: '<article>{{content}}</article>'
})
new Vue({
  el: '#app'
})
```

Props 是从父级向子级传递数据的一种方式。它们是父级和子级之间数据的单向流动。Props 总是被定义为一个数组。

前面例子的代码可以在这里找到：[`codepen.io/AjdinImsirovic/pen/KeZNPr`](https://codepen.io/AjdinImsirovic/pen/KeZNPr)。

我们在组件中注册了一个 prop，现在我们可以在 HTML 中使用它作为一个名为与我们的 prop 相同的属性：

```js
<main id="app">
  <custom-article content="This component was made with the help of a prop."> 
  </custom-article>
</main>
```

当我们需要对组件进行较小的更改而不必制作一个全新的组件时，我们使用 props。它们帮助我们重复使用我们已经拥有的东西。

在下一节中，我们将使用 Vue 实例的`data`对象向我们的`custom-article`组件添加内容。

# 使用数据对象向我们的组件添加内容

这个例子的代码笔可以在[`codepen.io/AjdinImsirovic/pen/QxadmE`](https://codepen.io/AjdinImsirovic/pen/QxadmE)找到。

在我们的 HTML 中，我们将代码更改为以下内容：

```js
<main id="app">
  <custom-article v-bind:content="datacontent"> 
  </custom-article>
</main>
```

在我们的 JS 中，我们将更新我们的 Vue 代码如下：

```js
Vue.component('custom-article', {
  props: ['content'],
  template: '<article>{{content}}</article>'
})
new Vue({
    el: '#app',
    data: {
      datacontent: 'This component was made with the help of a data object in the Vue instance'
    }
})
```

在前面的例子中，我们使用`v-bind`指令将我们的`custom-article`组件中的`content`prop 绑定到我们的`data`对象的`datacontent`属性。

如果你仔细思考这段代码，你会发现 props 几乎就像是命名变量（在示例中，prop 的变量`name`是`content`）。Props 只是将从父组件接收到的数据传递给子组件。

我们还可以用另一种方式来做这件事。我们可以将数据传递给组件，而不是在 Vue 实例内部使用数据；只是这一次它必须是一个数据函数。以下是此实现的完整代码：

```js
// HTML
<main id="app">
  <custom-article></custom-article>
</main>

// JS
Vue.component('custom-article', {
  template: '<article>{{datacontent}}</article>',
  data: function() {
    return {
      datacontent: 'This component was made with the help of a data function in the Vue component called custom-article'
    }
  }
})
new Vue({
    el: '#app'
})
```

要查看上一个示例的代码，请访问[`codepen.io/AjdinImsirovic/pen/VdyQzW`](https://codepen.io/AjdinImsirovic/pen/VdyQzW)。

如果我们将数据作为对象而不是函数使用，那么响应性将适用于组件的所有实例。由于组件的主要目的是可重用的，因此重要的是要记住在这种情况下数据必须是一个函数。

Props 也可以被定义为对象，这样我们可以给它们更多的信息：验证传入的数据，设置默认值（如果没有数据传入的话），等等。

在以下示例中，我们声明我们的`custom-article`组件期望父组件传递一个名为`message`的 prop，或者是字符串类型，这是必需的：

```js
<!--HTML-->
<div id="app">
  <custom-article :message-being-passed="datacontent"></custom-article>
</div>

//JS
Vue.component('custom-article', {
  props: {
    messageBeingPassed: {
      type: String,
      required: true,
      default: 'Hello Vue'
    }
  },
  template: `<div class="thetemplate">{{ message }}</div>`
});

new Vue({
  el: "#app",
  data: function() {
    return {
      datacontent: 'This component was made with the help of a data function in the Vue component called custom-article, and the data passed was validated with the help of the props object inside the Vue component'
    }
  }
})

//CSS
.thetemplate {
  font-size: 30px;
  padding: 20px;
  color: limegreen;
  font-family: Arial;
  border: 3px solid green;
  border-radius: 10px;
}
```

此示例可在[`codepen.io/AjdinImsirovic/pen/mKpxGZ`](https://codepen.io/AjdinImsirovic/pen/mKpxGZ)找到。

假设我们注释掉了 Vue 实例的`data`函数中的`datacontent`属性。你能猜到会发生什么吗？

换句话说，如果`datacontent`没有提供正确的数据会发生什么？子组件将简单地回到`props`对象中的`default`属性。

要看到这个实例的效果，请访问此链接：[`codepen.io/AjdinImsirovic/pen/BVJxKL`](https://codepen.io/AjdinImsirovic/pen/BVJxKL)。

# Vue 中构建组件模板的其他方法

到目前为止，我们已经看过将模板定义为字符串（使用单引号或双引号）和模板字面量（使用反引号）。还有许多其他处理组件模板的方法：

+   内联模板

+   X-templates

+   渲染函数

+   单文件组件

+   JSX

它们大多都有各自的优缺点。例如，在 Vue 中使用 JSX 是可能的，但通常不被赞同，因为这不是 Vue 的做事方式。内联模板是使用 HTML 中的`inline-template`属性制作的。

如果你在 HTML 的 script 标签中添加`type=''text/x-template''`，你将创建一个 Vue x-template。以下是一个例子：

```js
// HTML
<div id="app">
  <script type="text/x-template" id="custom-article-template">
    <p>{{ name }}</p>
  </script>
</div>

// JS
Vue.component('custom-article', {
  template: '#custom-article-template',
  props: ['name']
})
new Vue({
    el: '#app'
})
```

此示例的代码笔可在此处找到：[`codepen.io/AjdinImsirovic/pen/NzXyem`](https://codepen.io/AjdinImsirovic/pen/NzXyem)。

单文件模板可能是在 Vue 中创建模板的最实用方式。您可以将所有的 HTML、JS 和样式都放在一个文件中（使用`.vue`文件扩展名），然后使用构建过程（如 Webpack）编译此文件。在后面的章节中，当我们使用 Vue-cli（借助 Vue 中 Webpack 的使用）时，我们将深入研究这一点。

# 通过组件构建简单的网页

正如我们在前一节中所看到的，Vue 中构建组件的方式有很多种，这可能会使事情看起来比必须复杂。虽然重要的是要意识到 Vue 为我们构建组件的各种方式带来的多样性，但在本节中，我们将看一种简单的使用组件构建网页的方式。

在开始构建我们的页面之前，有一件事情应该对我们清楚：Vue 中的每个组件也只是另一个 Vue 实例。这意味着每个组件都需要一个选项对象，其中包含与任何其他 Vue 实例相同的键值对。唯一的例外是根 Vue 实例具有一些额外的选项，只能在其中使用。

在这些介绍性的澄清之后，让我们看看如何将组件添加到 Vue 实例中。

# 将简单组件添加到 Vue 实例中

在开始这个示例之前，我们将从一个简单的 Vue 实例开始。

在我们的 JavaScript 文件中，让我们创建一个最简单的 Vue 实例，以`#app`元素作为其入口点：

```js
new Vue({
  el: '#app',
  data: {}
})
```

接下来，让我们在我们的 HTML 中添加一个 div，这样我们的 Vue 实例就有了页面中的一个元素来访问其 DOM：

```js
<div id="app"></div>
```

现在我们将在我们的 JavaScript 文件中添加另一个组件。让我们通过将以下代码添加到顶部来扩展我们现有的 JS 文件：

```js
Vue.component('the-header', {
  template: '<h1 class="header css classes go here">Our example header</h1>'
})
```

现在，我们可以在我们的 HTML 中简单地添加自定义的`the-header`组件：

```js
<div id="app">
  <the-header></the-header>
</div>
```

这样做将在屏幕上呈现我们的示例标题文本。

既然我们已经看到了向我们的 Vue 应用程序添加一个简单组件有多么容易，让我们再添加一个来加强这一点。

我们将从扩展我们的 JS 文件开始，添加另一个组件`the-footer`：

```js
Vue.component('the-header', {
  template: '<h1 class="header css classes go here">Our example header</h1>'
});

Vue.component('the-footer', {
  template: '<h1 class="footer css classes go here">Our example header</h1>'
});

//Root Instance
new Vue({
  el: '#app',
  data: {}
})
```

当然，我们需要更新我们的 HTML 以使其工作：

```js
<div id="app">
  <the-header></the-header>
  <the-footer></the-footer>
</div>
```

在命名自定义组件时，我们需要使用连字符。这样做是为了确保与常规 HTML 元素没有命名冲突。

本节示例代码可在此处找到：[`codepen.io/AjdinImsirovic/pen/qypBbz`](https://codepen.io/AjdinImsirovic/pen/qypBbz)。

现在我们了解了如何向 Vue 实例添加简单组件，让我们通过添加一个更复杂的例子来练习。

# 在 Vue 中创建由组件组成的更复杂的页面

首先，让我们向我们的新 Vue 实例中添加一个组件。这次，我们将在自定义组件的选项对象中使用数据选项。

这是我们开始的代码：

```js
Vue.component('the-header', {
  template: '<h1 class="h1 text-success">{{header}}</h1>',
  data: function() {
    return {
      header: 'Just another simple header'
    }
  }
});

//Root Instance
new Vue({
  el: '#app',
  data: {}
})
```

在这段代码中，我们已经在模板中添加了 mustache 语法。然后我们利用了数据选项来返回将在模板中插值的文本。mustache 语法告诉我们的组件在数据选项中查找`header`。

此示例的代码在此处可用：[`codepen.io/AjdinImsirovic/pen/wxpvxy`](https://codepen.io/AjdinImsirovic/pen/wxpvxy)。

接下来，在我们的页眉下，我们将添加一些 Bootstrap 卡片。

为简单起见，我们将使用官方 Bootstrap 文档中的现有示例，该示例位于以下 URL：[`getbootstrap.com/docs/4.0/components/card/#using-grid-markup`](https://getbootstrap.com/docs/4.0/components/card/#using-grid-markup)。

该示例提供了以下代码：

```js
<div class="row">
  <div class="col-sm-6">
    <div class="card">
      <div class="card-body">
        <h5 class="card-title">Special title treatment</h5>
        <p class="card-text">
          With supporting text below as a natural lead-in to additional 
          content.    
        </p>
        <a href="#" class="btn btn-primary">Go somewhere</a>
      </div>
    </div>
  </div>
  <div class="col-sm-6">
    <div class="card">
      <div class="card-body">
        <h5 class="card-title">Special title treatment</h5>
        <p class="card-text">
          With supporting text below as a natural lead-in to additional 
          content.
        </p>
        <a href="#" class="btn btn-primary">Go somewhere</a>
      </div>
    </div>
  </div>
</div>
```

尽管 Bootstrap 框架不是本书的主题，但对我们来说，给出一个在实践中使用 Vue 组件的真实例子将是有用的。由于 Bootstrap 基本上已成为前端框架的行业标准，它是展示 Vue 组件不仅如何一般使用，而且如何与其他前端技术结合的完美候选者。

现在让我们看看如何向我们的 Vue 网页示例中添加一个单个卡片。这是要添加到我们的 JS 中的代码：

```js
Vue.component('the-card', {
  template: '<div class="card"><div class="card-body"><h5 class="card-title">Special title treatment</h5><p class="card-text">With supporting text below as a natural lead-in to additional content.</p><a href="#" class="btn btn-primary">Go somewhere</a></div></div></div>',
});
```

我们代码开发的这个阶段的代码在这里可用：[`codepen.io/AjdinImsirovic/pen/VByYeW`](https://codepen.io/AjdinImsirovic/pen/VByYeW)。

接下来，让我们将我们的卡片组件添加到我们的 HTML 中。完整的更新代码将如下所示：

```js
<div id="app">
 <div class="container">
    <the-header></the-header>
    <div class="row">
      <div class="col-sm-6">
        <the-card></the-card>
      </div>
      <div class="col-sm-6">
        <the-card></the-card>
      </div>
    </div>
</div>
```

将上述代码添加到我们的 HTML 中，并根据之前描述的进行 JS 更新，我们将得到以下结果：

![](img/063612e2-9c2b-4121-8243-2e3a46dac0d3.png)

我们在 JS 中添加了一个单独的卡片组件；然而，正如我们在之前的例子中看到的，我们现在可以根据需要在 HTML 中重复使用它。

这为我们提供了一个绝佳的机会，借助 Vue 快速原型设计完整的网页。

我们甚至可以进一步进行，正如我们将在下一节中看到的。

# 使用 v-for 改进我们基于 Vue 的布局

在这一部分，我们将通过 Vue 指令来改进我们现有的网页。

我们的具体目标是尝试在组件实例中使用数据选项，并结合 Vue 指令的功能来进一步改进我们的 Vue 应用程序。

此部分的代码可在[`codepen.io/AjdinImsirovic/pen/Epoamy`](https://codepen.io/AjdinImsirovic/pen/Epoamy)中找到。

让我们使用 ES6 的反引号语法使我们的 JS 更容易阅读。这种语法允许我们编写跨多行的 JavaScript 字符串：

```js
Vue.component('the-header', {
  template: '<h1 class="h1 text-success">{{header}}</h1>',
  data: function() {
    return {
      header: 'Just another simple header'
    }
  }
});

Vue.component('the-card', {
  template: `
    <div class="card">
      <div class="card-body">
        <h5 class="card-title">Special title treatment</h5>
        <p class="card-text">
          With supporting text below as a natural lead-in to addtional 
          content.
        </p>
        <a href="#" class="btn btn-primary">Go somewhere</a>
      </div>
    </div>`,
});

//Root Instance
new Vue({
  el: '#app',
  data: {}
})
```

现在，让我们将`data`选项添加到`the-card` Vue 组件中：

```js
  data: function() {
    return {
      customCard: [{
        heading: 'John Doe',
        text: 'John.doe@acme.org'
      }, 
      {
        heading: 'John Doe',
        text: 'John.doe@acme.org'
      }
     ]}
  }
```

正如我们在前面的代码中所看到的，我们返回了一个包含特定`heading`和`text`的`customCard`对象数组。

接下来，我们可以在我们的模板中使用`v-for`指令，就像这样：

```js
Vue.component('the-card', {
  template: `
    <div class="card">
      <div class="card-body" v-for="customCard in customCards">
        <h5 class="card-title">{{customCard.heading}}</h5>
        <p class="card-text">
          {{customCard.text}}
        </p>
        <a href="#" class="btn btn-primary">Go somewhere</a>
      </div>
    </div>`,
```

我们在具有`card-body`类的`div`中引入`v-for`指令。我们循环遍历我们的`customCards`集合中的每个`customCard`，并为我们`customCard`数组中的每个对象的`h5`文本内容插入`customCard.heading`。

最后，让我们在 HTML 中添加一个 Bootstrap 类，这样我们网页的`h1`标签就不会紧贴在视口的顶部。为此，我们将使用 Bootstrap 的间距实用程序。您可以在这里阅读有关它们的信息：[`getbootstrap.com/docs/4.0/utilities/spacing/`](https://getbootstrap.com/docs/4.0/utilities/spacing/)。

我们的 HTML 中的更改将是最小的，只是添加了另一个 CSS 类：`mt-5`。

最后，以下是改进页面的完整 JS 代码。首先，我们注册主标题组件：

```js
//Register main title component
Vue.component("main-title-component", {
  template: '<h1 class="text-center mt-5 mb-4">{{title}}</h1>',
  data: function() {
    return {
      title: "Just another title"
    };
  }
});
```

然后我们注册`list group`组件：

```js
//Register list group component
Vue.component("list-group-component", {
  template: `
    <ul class="list-group">
      <li class="list-group-item" v-for="item in items">{{item.description}}</li>
    </ul>`,
  data: function() {
    return {
      items: [
        {
          description: "Description one"
        },
        {
          description: "Description two"
        },
        {
          description: "Description three"
        }
      ]
    };
  }
});
```

之后，我们注册了`card`组件：

```js
// Register card component
Vue.component("card-component", {
  template: `
    <div class="card">
      <div class="card-body">
        <h5 class="card-title">{{title}}</h5>
        <p class="card-text">{{text}}</p>
        <a href="#" class="btn btn-primary">Go somewhere</a>
      </div>
    </div>`,
  data: function() {
    return {
      title: "This is the card title",
      text: "This is the card text"
    };
  }
});
```

我们还添加了`root instance`：

```js
//root Instance
new Vue({
  el: "#app",
    data: {}
});
```

这是 HTML：

```js
<div id="app">
  <div class="container mt-5 mb-5">
    <main-title-component></main-title-component>
    <div class="row">
      <div class="col">
        <list-group-component></list-group-component>
      </div>
      <div class="col">
        <card-component></card-component>
      </div>
    </div>
  </div>
</div>
```

添加上述代码的结果可以在此截图中看到：

![](img/fe9ea47d-d62d-4da1-88a5-c7a3f0c12356.png)

在这一部分，我们已经看过组件以及如何开始使用它们。接下来，我们将讨论 Vue 中的观察者。

# Vue 中的观察者

Vue 中的每个组件都有一个观察者。

为了理解这是如何工作的，让我们从本章的一个早期例子开始。这个例子来自*计算属性*部分，链接在这里：[`codepen.io/AjdinImsirovic/pen/qKVyry`](https://codepen.io/AjdinImsirovic/pen/qKVyry)。这是我们的起始代码。正如我们从前一节知道的，这里有两个输入字段，我们正在打印出这些输入字段中输入的值在表单下的一些 span 标签中。

让我们扩展我们的例子。初始代码是一样的；我们只会添加一个观察者。更新后的代码可以在这个 Codepen URL 找到：[`codepen.io/AjdinImsirovic/pen/jprwKe`](https://codepen.io/AjdinImsirovic/pen/jprwKe)。

可以观察到，我们对原始笔记本唯一的更新是添加了观察者选项，如下所示：

```js
  watch: {
    ownerName(previousValue,currentValue) {
      console.log(`The value in the first input has changed from:   
        ${previousValue} to: ${currentValue}`);
    }
  },
```

之前的观察者是如何工作的？它允许我们使用一个方法，该方法的名称必须与我们在 HTML 中观察的计算属性相同。观察者有可选参数，我们可以将这些参数传递给它，在方法体中使用；在这种情况下，我们给我们的可选参数一些好的和描述性的名称：`previousValue`和`currentValue`。

在`watch`方法的主体中，我们正在记录输入值的更改到 JavaScript 控制台。测试这样工作的一个优雅的方法是，例如，突出显示第一个输入字段的初始值的*例如*部分，然后简单地擦除它，只留下输入中的*Old McDonald*的值。

这样做会导致以下句子被记录到控制台中：

```js
The value in the first input has changed from: e.g Old McDonald to: Old McDonald.
```

在下一节中，我们将看看如何在组件的生命周期的各个阶段挂钩，并在那个特定点用自定义代码改变其行为。

# 生命周期钩子

生命周期钩子是让我们在组件生命周期的各个阶段改变组件行为的方法。

# 什么是组件的生命周期？

这只是组件*生命周期*的自然进展。

因此，我们可以说生命周期钩子是这个旅程中每个组件都需要经历的*点*。在组件生命周期的这些特定*点*，我们可以使用这些方法来改变组件的行为。

Vue 团队为这些生命周期方法选择了非常描述性的名称。接下来是按组件生命周期的自然进展顺序组织的生命周期钩子列表：

+   `beforeCreate`

+   `created`

+   `beforeMount`

+   `mounted`

+   `beforeUpdate`

+   `updated`

+   `activated`

+   `deactivated`

+   `beforeDestroy`

+   `destroyed`

这个组件生命周期的可视化表示可以在这个地址找到：[`vuejs.org/images/lifecycle.png`](https://vuejs.org/images/lifecycle.png)。

请注意，打印出这张图片并随身携带直到完全理解它所传达的信息将是有益的。这将对更深入地理解 Vue 总体以及其组件生命周期特别有帮助。

正如我们所看到的，组件的生命周期有五个不同的阶段，每个阶段在特定阶段开始之前有一个生命周期钩子，以及在完成后有另一个生命周期钩子。

重要的是要注意，一个组件可以根据数据模型的变化被多次挂载。这在之前的提示框中引用的生命周期图中是可以验证的。然而，同样重要的是要理解，当底层数据发生变化时，DOM 重新渲染可能会导致组件被有效地*卸载*，尽管这在生命周期图中没有明确提到。

# 我们如何使用生命周期钩子？

让我们看一个简单的例子，可以在这个 Codepen 网址找到：[`codepen.io/AjdinImsirovic/pen/jprmoa`](https://codepen.io/AjdinImsirovic/pen/jprmoa)。

首先，让我们添加 HTML：

```js
<div> Lorem ipsum dolor sit amet</div>
<div id="app">
  <custom-article :message="datacontent"></custom-article>
</div>
```

接下来，让我们添加 CSS：

```js
div,.thetemplate {
 font-size: 30px;
 padding: 20px;
 color: limegreen;
 font-family: Arial;
  border: 3px solid green;
  border-radius: 10px;
}
```

最后，JS 部分：

```js
Vue.component('custom-article', {
  props: {
    message: {
      type: String,
      required: true,
      default: 'Hello Vue'
    }
  },
  template: `<div class="thetemplate">{{ message }}</div>`
});

new Vue({
  el: "#app",
  beforeCreate() {
    alert("Lifecycle hook beforeCreate has been run");
  },
  created() {
    setTimeout(function(){
      alert('This message is showing 5 seconds after the \'created\' life cycle hook');
    },5000);
  },
  data: function() {
    return {
      datacontent: 'This component was made with the help of a data function in the Vue component called custom-article, and the data passed was validated with the help of the props object inside the Vue component'
    }
  }
});
```

如在提供的 Codepen 中所示，很容易在 Vue 中钩入生命周期方法。只需要在 Vue 实例中的生命周期钩子方法名称中提供所需的代码（功能）即可。

在前面的示例中，我们展示了`beforeCreate()`方法的警报，并且在`created()`方法运行后 5 秒钟显示另一个警报。

生命周期钩子还有许多有用的用途，这将在接下来的章节中介绍。

# 总结

在本章中，我们了解了 Vue 中的一些基本概念。我们描述了为什么这些概念很重要以及它们如何被使用。我们还看了一些在实践中使用这些概念的简单例子。

我们了解了 Vue 中的数据驱动视图以及响应性作为跟踪数据模型变化的一种方式。我们看了使用计算属性和方法、指令及其修饰符的方法。我们已经看到了一些关于组件、模板和 props 的实际例子，以及在 Vue 中构建组件模板的不同方法。

我们学习了如何使用 Vue 组件和指令原型化网站，并在本章中结束时，我们看了一下 watchers 和生命周期钩子，作为改变组件行为的强大方式，可以在它们的生命周期的任何时刻进行。

在下一章中，我们将进一步深入研究 Vue 中的响应式编程，重点关注组件、props 和插槽。
