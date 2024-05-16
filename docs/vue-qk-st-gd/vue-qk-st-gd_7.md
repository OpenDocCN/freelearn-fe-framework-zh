# 第七章：使用 Vuex

在本章中，我们将学习如何通过使用 Vuex 在 Vue 中管理复杂状态。Vuex 有助于处理 Vue 应用程序中状态管理和深度嵌套组件的问题。

在本章结束时，您将了解 Vuex 解决了什么问题以及它是如何解决这些问题的，您应该了解所有移动部分的适用位置。您还将了解如何构建一个简单的 Vuex 应用程序以及在考虑扩展它时应采取的方法。

具体来说，我们将讨论以下主题：

+   理解状态

+   状态管理、数据存储和单向数据流

+   热重载

+   构建一个非常简单的 Vuex 应用程序

+   如何从 Vue DevTools 的 Vuex 选项卡更新状态

+   构建一个更复杂的 Vuex 应用程序

让我们首先了解状态到底是什么。

# 什么是状态？

应用程序的状态是其在某一时间点的所有数据。由于我们通常关注当前应用程序的状态，我们可以将其重新表述为以下内容：状态是应用程序当前的数据，是由应用程序采取的先前步骤和应用程序内部的函数对用户交互做出响应而产生的。

那么，在我们的应用程序中是什么改变了它的状态呢？当然是函数。用户与我们的应用程序交互，触发函数来将当前状态更改为其他状态。

然而，随着我们的应用程序增长，组件嵌套几层是很常见的。如果我们说状态是应用程序在任何给定时间应该显示在屏幕上的“真相之源”，那么让我们尽可能地使这个真相之源易于理解和简单易用对我们来说是有益的。

很不幸，在复杂的应用程序中，这并不容易。我们应用的任何部分，应用内的任何组件都可能影响应用的任何其他部分。在我们的应用程序中管理状态有点像玩打地鼠游戏：应用程序中的一个部分的交互会导致应用程序的其他部分出现问题。

关于如何在前端应用程序中管理复杂状态的最佳实践的思考导致了诸如“数据存储”和“单向数据流”等概念。

# 状态管理、数据存储和单向数据流

管理复杂状态问题的常见解决方案是存储的概念：一个保持应用程序状态所有数据的唯一真相源。一旦我们有了这个中心位置—**存储**—我们就可以更容易地理解状态，因为现在只需要将状态数据发送到那些在应用程序生命周期中任何时候都需要它的组件。

为了使状态更新更简单，我们需要限制这些更新的方式。这就是单向数据流的作用。通过单向数据流，我们规定了数据在应用程序内部流动的规则，这意味着数据（状态）只能以有限的方式流动，使得在需要时更容易理解状态和调试状态。这种方法也是一个很好的时间节省者，因为现在作为开发者，我们知道可以期待什么；也就是说，要寻找我们知道状态是**可变**的地方。

# Vuex 状态管理模式

Vuex 是 Vue 的一个插件，由 Vue 的核心团队开发。设置非常简单。如果您需要一个快速的原型，您可以简单地从 CodePen 在线编辑器的设置中添加 Vuex 库，如第一章中所述，*介绍 Vue*。

您还可以通过 npm 安装它，使用以下命令：

```js
npm install --save vuex
```

当试图理解 Vuex 的工作原理时，通常会在网上找到描述 Vuex 作为受 Flux 强烈影响的状态管理模式的参考资料。这在一定程度上是正确的，但有趣的是 Flux 本身受到了 Elm 架构的启发。不管怎样，在 Vuex 中，数据流如下：

+   **Vue 组件**到动作

+   **动作**到变化

+   **变化**到状态

+   **状态**到 Vue 组件

数据总是以一种方式流动，最终回到起点，更新组件，然后*分发动作*，然后*提交变化*，然后*改变状态*，然后*渲染组件*，循环重复。因此，从略微不同的角度看单向数据流，我们可以重新表述它，专注于动词来描述存储中数据发生的情况：

+   动作被*分发*

+   变化被*提交*

+   状态被*改变*

+   组件被*渲染*

再次看一下这种单向数据流，我们现在可以用这些名词来描述数据流：*组件*、*动作*、*突变*和*状态*。用动词描述数据流，我们可以将这个过程视为：*分发*、*提交*、*突变*和*渲染*。

在 Vuex 中查看数据流的这两种方式是同一个硬币的两面，是状态更新的同一个循环，因此将这两个简短的列表都记住并不会有害，因为它将有助于加快对基本 Vuex 概念的理解。

为了在视觉上加强这些解释，可以在官方的 Vuex 文档中找到这种单向数据流的图表，网址是：[`vuex.vuejs.org/vuex.png`](https://vuex.vuejs.org/vuex.png)。

你可能会问，为什么要采取这种间接的方式？为什么组件不能直接改变状态？这样做有两个主要原因：首先，在 JavaScript 世界中，异步代码只是一个事实，因此选择在 Vuex 中分离异步和同步操作。因此，动作被设置为异步的，所以它们可以，例如，从服务器获取一些数据，只有在这个异步数据获取完成后才能*提交* *突变*；由于突变是严格同步的，在服务器调用完成之前调用它们是没有意义的。其次，这种分离关注点的方式使得更容易跟踪状态变化，甚至包括时间旅行调试：按时间顺序重新运行突变以跟踪状态的变化并追踪错误。

在 Vuex 状态管理模式中，组件永远不能直接改变全局状态。突变会这样做。

在接下来的部分，我们将看看这些构建块中的每一个。

# store

**store**需要添加到 Vue 实例的根部，以便所有组件都可以共享这个集中的全局状态。通常，我们将 store 声明为`const`，然后在代码中稍后将其添加到作为参数传递给 Vue 构造函数的对象文字中，就像这样：

```js
const store = new Vuex.Store({ 
  // store details go here
})
new Vue({
 el: '#app',
 store: store,
 // etc
})
```

接下来，我们将学习有关获取器的知识。

# Vuex store 中的获取器

我们的 store 也可以有获取器。获取器允许我们在模板中从状态中返回值。它们有点像计算值。它们是只读的，意味着它们不能改变状态。它们的责任只是读取它并对其进行一些非破坏性的操作。然而，底层数据没有被突变。

因此，我们在存储中使用`getters`对全局状态执行一些非破坏性工作。那么我们接下来该怎么做呢？我们如何使用它们？我们在应用程序的另一侧使用它们——在组件内部——我们使用`computed`并从存储中返回`getters`的值。

# Vuex 存储变化

变化，顾名思义，改变状态，并且是同步的。改变状态的函数接收参数：现有状态和有效负载。有效负载参数是可选的。它们负责直接更新 Vuex 中的状态。您可以使用以下语法从操作中执行变化：`state.commit`。

# Vuex 存储中的操作

操作以异步和间接的方式更新状态，通过调用我们在存储中定义的一个或多个变化。因此，操作调用所需的变化。另一方面，在组件内部，要执行一个操作，我们使用存储的分派值，使用以下语法：`store.dispatch`。

现在让我们扩展我们的样板代码，包括我们刚刚讨论的内容：

```js
const store = new Vuex.Store({ 
  // store details go here; they usually have:
  state: {
    // state specified here
  },
  getters: {
    // getters are like computed values - they don't mutate state
 },
 mutations: {
   // they mutate the state and are synchronous, 
   // functions that mutate state can have arguments; these arguments are called 'payload'
 },
 actions: {
   // asynchronous functions that commit mutations
 }
})
new Vue({
 el: '#app',
 store,
 // etc
})
```

在 Vue 构造函数中，我们可以看到，使用 ES6 语法，可以简化构造函数对象字面参数中的`store: store`键值对，只需使用`store`。

# 热重载

另一个由 Webpack 的兴起带来的流行概念是热重载。当您的应用程序正在运行时，更新文件时，例如，在组件的一个文件中添加一些变化的作用域样式，Webpack 将热重载更新的文件，而不使用您应用程序中的状态。换句话说，它不会重新加载整个页面，而只会重新加载受更改影响的应用程序部分。这是有用的原因是，使用热模块替换，状态将被保留，如果刷新页面是不可能的。这带来了更快的开发时间和浏览器中更新的无缝体验的额外好处。

# 使用 Vuex 构建水果计数器应用程序

我们将构建的应用程序只是一个简单的水果计数器应用程序。目标是帮助用户确保每天吃五份水果，我们将设置一个简单的应用程序，开始时有五份水果可供食用，每次点击按钮时，它将减少`1`的数量。这样，我们可以跟踪我们的健康饮食目标。

我们将通过设置初始状态来开始我们的应用程序，其中只有一个键值对：

```js
const store = new Vuex.Store({
  state: {
    count: 5
  },
```

接下来，我们将设置`getters`。正如我们已经学过的，`getters`只返回状态：

```js
  getters: {
    counter(state) {
      return state.count;
    }
  },
```

接下来，我们将添加两个 mutations：第一个 mutation，`decrementCounter`，将通过减去 payload 参数中存储的值来操作计数器。我们将递减 state.count 的值，直到达到`0`。为了确保`state.count`的值不能小于`0`，我们将使用三元语句进行检查，并相应地设置其值。

第二个 mutation，`resetCounter`，将重置计数器的值为初始状态：

```js
  mutations: {
    decrementCounter(state, payload) {
      state.count = state.count - payload;
      state.count<0 ? state.count=0 : state.count
    },
    resetCounter(state) {
      state.count = 5;
    }
  },
```

接下来，我们将设置两个操作，`decrement` 和 `reset`：

```js
  actions: {
    decrement(state, payload) {
      state.commit("decrementCounter", payload);
    },
    reset(state) {
      state.commit("resetCounter");
    }
  }
```

最后，我们正在设置我们的应用，并在其 Vue 构造函数中指定`el`、`store`、`computed`和`methods`选项。

```js
const app = new Vue({
  el: "#app",
  store: store,
  computed: {
    count() {
      return store.getters.counter;
    }
  },
  methods: {
    eatFruit(amount) {
      store.dispatch("decrement", amount);
    },
    counterReset() {
      store.dispatch("reset");
    }
  }
});
```

接下来，在我们的 HTML 中，我们设置了我们简单应用的结构：

```js
<div id="app">
 <h1>Fruit to eat: {{count}}</h1>
 <button v-on:click="eatFruit(1)">Eat fruit!</button>
 <button v-on:click="counterReset()">Reset the counter</button>
</div>
```

可以在以下网址找到工作示例：[`codepen.io/AjdinImsirovic/pen/aRmENx`](https://codepen.io/AjdinImsirovic/pen/aRmENx)。

# 使用 Vue DevTools 插件跟踪我们的 Vuex 状态

如果你在 Chrome 扩展程序网上商店的搜索栏中输入`vuejs devtools`，你会得到一些结果。第一个结果是官方插件的稳定版本。第二个结果是 Vue DevTools 扩展的 beta 版本。要查看正在开发的所有选项，并了解这个插件的发展方向，最好安装 beta 版本。有趣的是，一旦在 Chrome DevTools 中打开，两个版本显示相同的信息。目前，消息显示为`Ready. Detected Vue 2.5.17-beta.0`。

与常规版本相比，实验版本多了一些标签，即`routing`和`performance`。然而，即使是现有的标签也有一些非常有用的改进。例如，Vuex 标签具有直接从 DevTools 内部更新状态的功能。要访问该功能，只需按下*F12*键打开 Chrome DevTools。将 DevTools 定位到使用 Vue 扩展的最佳方法是将其设置为`Dock to bottom`选项。这个选项可以通过按下位于 DevTools 窗格右上角的 DevTools 关闭图标旁边的三个垂直点图标（*自定义和控制 DevTools*图标）来访问。

启用底部停靠后，打开 Vue 标签，然后在其中激活 Vuex 标签，您将看到两个窗格。最初，左窗格列出了基本状态。这个窗格列出了所有的变化，并允许我们进行时间旅行调试。右窗格列出了实际的负载、状态和变化，因此它给了我们更细粒度的视图，让我们了解在任何给定的变化中发生了什么。要进行任何特定变化的时间旅行，只需将鼠标悬停在其上，然后单击“时间旅行”图标。您还可以选择在列出的任何变化上运行“提交”或“还原”。正如您可能猜到的那样，当执行“提交”命令时，将对当前悬停的变化执行提交，而“还原”命令将撤消特定变化的提交。

另一个有用且有趣的功能是能够直接从 Vuex 标签更新状态。例如，假设我们点击了“吃水果！”按钮几次。现在，我们可以点击变化窗格中的任何给定的“减少计数”变化，然后在右窗格中得到以下信息：

```js
▼ mutation
    payload: 1
    type: ''decrementCounter''
▼ state
    count: 1
▼ getters
    counter: 1
```

使用这个窗格非常简单。如果我们需要更新我们的状态，在“状态”条目内悬停在“计数：1”上将触发四个图标的出现：编辑值图标、减号图标、加号图标和“复制值”图标，显示为三个垂直点。在这里，我们还可以看到“getter”是只读的证据。悬停在“getter”条目上不会显示任何编辑图标。与此相反，“状态”和“变化”条目都可以从这个窗格中进行编辑。

# 改进我们的水果计数应用程序

在本节中，我们将对我们的水果计数应用程序进行一些改进。目标是看看我们如何使用 Vuex 来扩展我们的应用程序。

我们将通过添加额外的功能来更新我们的应用程序。即，我们将为不同的水果添加按钮：苹果和梨。要吃的水果数量以及吃的水果的数量和种类也将出现在我们的应用程序中。

这是更新后的 JS 代码。我们首先定义存储中的状态：

```js
const store = new Vuex.Store({
  state: {
    count: 5,
    apples: 0,
    pears: 0
  },
```

接下来，我们设置 getter：

```js
  getters: {
    counter(state) {
      return state.count;
    },
    appleCount(state) {
      return state.apples;
    },
    pearCount(state) {
      return state.pears;
    }
  },
```

在定义变化时，我们需要`decrementWithApplesCounter`和`decrementWithPearsCounter`，以及`resetCounter`功能：

```js
  mutations: {
    decrementWithApplesCounter(state, payload) {
      state.count = state.count - 1;
      state.count < 0 ? (state.count = 0) : (state.count, state.apples 
       += 1);
    },
    decrementWithPearsCounter(state, payload) {
      state.count = state.count - 1;
      state.count < 0 ? (state.count = 0) : (state.count, state.pears 
      += 1);
    },
    resetCounter(state) {
      state.count = 5;
      state.apples = 0;
      state.pears = 0;
    }
  },
```

接下来，我们将列出我们的动作，`decrementWithApples`，`decrementWithPears`和“重置”：

```js
  actions: {
     decrementWithApples(state, payload) {
       setTimeout(() => {
         state.commit("decrementWithApplesCounter", payload);
       }, 1000)
     }, 
    decrementWithPears(state, payload) {
      state.commit("decrementWithPearsCounter", payload);
    },
    reset(state) {
      state.commit("resetCounter");
    }
  }
});
```

我们将通过添加 Vue 构造函数来结束它：

```js
const app = new Vue({
  el: "#app",
  store: store,
  computed: {
    count() {
      return store.getters.counter;
    },
    apples() {
      return store.getters.appleCount;
    },
    pears() {
      return store.getters.pearCount;
    }
  },
  methods: {
    eatApples(payload) {
      store.dispatch("decrementWithApples", payload);
    },
    eatPears(payload) {
      store.dispatch("decrementWithPears", payload);
    },
    counterReset() {
      store.dispatch("reset");
    }
  }
});
```

正如我们在这段代码中看到的，我们可以在 JS 三元运算符中更新多个变量的值。我们还使用`setTimeout()`函数调用来“模拟”对服务器的调用；这是不必要的，但用作更复杂的异步操作的示例。

更新后的 HTML 代码将如下所示：

```js
<div id="app" class="p-3">
  <h1>Fruit to eat: {{ count }}</h1>
  <p>Eaten: {{ apples }} apples, {{ pears }} pears</p>
  <button v-on:click="eatApples(1)" class="btn btn-success">
    An apple!
  </button>
  <button v-on:click="eatPears(1)" class="btn btn-warning">
    A pear!
  </button>
  <button v-on:click="counterReset()" class="btn btn-danger">
    Reset the counter
  </button>
</div>
```

更新后的示例应用程序可以在这里找到：[`codepen.io/AjdinImsirovic/pen/EdNaaO`](https://codepen.io/AjdinImsirovic/pen/EdNaaO)。

# 总结

在本章中，我们了解了 Vuex，这是一个强大的 Vue 插件，可以帮助我们从集中式全局存储管理状态。我们了解了什么是状态，以及为什么需要在更复杂的应用程序中集中数据存储。我们讨论了单向数据流及其在 Vuex 中的实现，通过使用 getter、store mutation 和 store action。我们从理论转向实践，首先构建了一个简单的应用程序，然后学习如何借助 Vue Devtools 扩展来简化我们的开发过程。

在下一节中，我们将使用 Vue-router 进行路由处理，并学习使用 Nuxt 进行服务器端渲染。
