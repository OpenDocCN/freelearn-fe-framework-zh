添加 Vuex 存储

拥有像 MongoDB 这样的数据库系统来管理我们的数据是很棒的，因为我们可以使用它在需要时远程请求我们路由的数据。然而，偶尔我们需要在页面或组件之间共享一些数据，并且我们不希望为这种数据进行额外和不必要的 HTTP 请求。理想情况下，我们希望在本地应用程序中有一个中心位置来存储这种“无处不在”的和集中的数据。幸运的是，我们有一个名为 Vuex 的系统来为我们存储这种数据，这就是你将在本章中探索的内容。因此，在本章中，您将学习如何在应用程序中使用 Vuex 进行状态管理（集中式数据管理）。您将了解 Vuex 的架构、其核心概念以及管理模块化 Vuex 存储的建议目录结构。最后，您将学习如何在 Nuxt 应用程序中激活和使用 Vuex 存储。

本章我们将涵盖的主题如下：

+   理解 Vuex 的架构

+   开始使用 Vuex

+   理解 Vuex 的核心概念

+   构建 Vuex 存储模块

+   在 Vuex 存储中处理表单

+   在 Nuxt 中使用 Vuex 存储

# 第十章：理解 Vuex 架构

在学习如何在 Nuxt 应用程序中使用 Vuex 存储之前，我们应该了解它在标准 Vue 应用程序中的工作原理。但是什么是 Vuex？让我们在接下来的部分中找出来。

## 什么是 Vuex？

简而言之，Vuex 是一个集中式数据（也称为状态）管理系统，具有一些规则（我们稍后会详细了解），以确保状态只能可预测地从需要访问共同数据的多个（远程）组件中进行变异。这种信息集中的想法在 Redux 等工具中很常见。它们都与 Vuex 共享类似的状态管理模式。让我们在下一节看看这种模式是什么。

## 状态管理模式

为了理解 Vuex 中的状态管理模式，让我们看一个我们已经熟悉的简单 Vue 应用程序：

```js
<div id="app"></div>

new Vue({
  // state
  data () {
    return { message: '' }
  },

  // view
  template: `
    <div>
      <p>{{ message }}</p>
      <button v-on:click="greet">Greet</button>
    </div>
  `,

  // actions
  methods: {
    greet () {
      this.message = 'Hello World'
    }
  }
}).$mount('#app')
```

这个简单的应用程序有以下部分：

+   `state`，保存应用程序的源

+   `view`，映射状态

+   `actions`，可以用于从视图中变异状态

它们在这样一个小应用程序中运行得很好，并且很容易管理，但是当我们有两个或更多组件共享相同状态时，或者当我们想要使用来自不同视图的操作来变异状态时，这种简单性就变得不可持续和有问题了。

传递 props 可能是你脑海中浮现的解决方案，但对于嵌套组件来说这很繁琐。这就是 Vuex 的作用，将通用状态提取出来并在一个特定位置全局管理，称为*store*，以便任何组件都可以从任何地方访问它，无论嵌套多深。

因此，使用状态管理进行分离并强制执行一些规则可以保持视图和状态的独立性。使用这种方法，我们可以使我们的代码更加结构化和可维护。让我们来看一下 Vuex 的架构，如下图所示：

![](img/4d4c9ca1-b841-4800-ace4-f04e8959c25a.png)

参考来源：[`vuex.vuejs.org/`](https://vuex.vuejs.org/)

简而言之，Vuex 由 actions、mutations 和 state 组成。状态始终通过 mutations 进行变化，而 mutations 则始终通过 Vuex 生命周期中的 actions 进行提交。变化后的状态然后被渲染到组件中，同时，actions 通常会从组件中派发。与后端 API 的通信通常发生在 actions 中。让我们在下一节开始使用 Vuex，并深入了解其构成。

# 开始使用 Vuex

正如我们在前一节中提到的，所有 Vuex 活动都发生在一个 store 中，这个 store 可以简单地在项目根目录中创建。然而，虽然看起来很简单，但 Vuex store 与普通的 JavaScript 对象不同，因为 Vuex store 是响应式的，就像使用 `v-model` 指令在 `<input>` 元素上进行的双向绑定一样。因此，当你在 Vue 组件中访问任何状态数据时，当它在 store 中发生变化时，它会被*响应式地*更新。在 store 的状态中的数据必须通过 mutations *显式地*提交，就像我们在前一节的图表中解释的那样。

在这个练习中，我们将使用单文件组件骨架来构建一些简单的带有 Vuex 的 Vue 应用程序。我们将把所有示例代码放在我们的 GitHub 仓库的 `/chapter-10/vue/vuex-sfc/` 中。让我们开始吧。

## 安装 Vuex

在我们创建 Vuex store 之前，我们必须通过以下步骤安装 Vuex 并导入它：

1.  使用 npm 安装 Vuex：

```js
$ npm i vuex
```

1.  使用 `Vue.use()` 方法导入并注册它：

```js
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex)
```

请记住，前面的安装步骤是为了在模块系统中使用 Vuex，这也是我们在本章中要做的。但在跳入模块系统应用程序之前，我们应该看一下如何通过 CDN 或直接下载来创建 Vuex 应用程序。

请注意，Vuex 需要 Promise 支持。如果您的浏览器不支持 Promise，请查看如何为您的应用安装 polyfill 库的方法，网址为[`vuex.vuejs.org/installation.html#promise`](https://vuex.vuejs.org/installation.html#promise)。

## 创建一个简单的 store

我们可以通过以下步骤使用 CDN 或直接下载开始一个简单的 store：

1.  使用 HTML 的`<script>`块安装 Vue 和 Vuex：

```js
<script src="/path/to/vue.js"></script>
<script src="/path/to/vuex.js"></script>
```

1.  在 HTML 的`<body>`块中激活 Vuex store：

```js
<script type="text/javascript">
  const store = new Vuex.Store({
    state: { count: 0 },
    mutations: {
      increment (state) { state.count++ }
    }
  })
  store.commit('increment')
  console.log(store.state.count) // -> 1
</script>
```

从这段代码中可以看出，你只需要在一个 JavaScript 对象中创建 Vuex 状态，一个 mutation 方法，然后你可以通过 store 的`state`键访问状态对象，并使用 store 的`commit`方法触发状态的改变，如下所示：

```js
store.commit('increment')
console.log(store.state.count)
```

在这个简单的例子中，我们遵守了 Vuex 中的一个强制规则，即通过提交 mutation 来改变状态数据，而不是直接改变它。让我们在下一节通过创建模块系统应用来深入了解 Vuex 的核心概念和其他规则。

# 理解 Vuex 的核心概念

在本节中，我们将指导您了解 Vuex 中的五个核心概念。它们是 state、getters、mutations、actions 和 modules。我们将从下一节开始首先研究 state 的概念。

## 状态

状态是 Vuex 存储的核心。它是我们可以以结构化和可预测的方式管理和维护的“全局”数据的来源。在 Vuex 中，状态是一个单一的状态树——一个包含所有应用状态数据的单一 JavaScript 对象。因此，通常每个应用程序只有一个 store。让我们看看如何在下面的部分中将状态传递给组件。

### 访问状态

正如我们在上一节中提到的，Vuex 存储是响应式的，但如果我们想在视图中访问响应式值，我们应该使用`computed`属性而不是`data`方法，如下所示：

```js
// vuex-sfc/state/basic/src/app.vue
<p>{{ number }}</p>

import Vue from 'vue/dist/vue.js'
import Vuex from 'vuex'
Vue.use(Vuex)

const store = new Vuex.Store({
  state: { number: 1 }
})

export default {
  computed: {
    number () {
      return store.state.number
    }
  }
}
```

现在，在`<template>`块中的`number`字段是响应式的，`computed`属性将重新评估并更新 DOM，每当`store.state.number`发生变化时。但是这种模式会导致耦合问题，并违背了 Vuex 的抽取理念。因此，让我们通过以下步骤重构前面的代码：

1.  将 store 提取到根组件中：

```js
// vuex-sfc/state/inject/src/entry.js
import Vue from 'vue/dist/vue.js'
import App from './app.vue'

import Vuex from 'vuex'
Vue.use(Vuex)

const store = new Vuex.Store({
  state: { number: 0 }
})

new Vue({
  el: 'app',
  template: '<App/>',
  store,
  components: {
    App
  }
})
```

1.  从子组件中移除 store，但保持`computed`属性不变：

```js
// vuex-sfc/state/inject/src/app.vue
<p>{{ number }}</p>

export default {
  computed: {
    number () {
      return this.$store.state.number
    }
  }
}
```

在更新的代码中，存储现在被注入到子组件中，您可以通过从组件中使用`this.$store`来访问它。但是，当您需要使用`computed`属性计算许多存储状态属性时，这种模式可能会变得重复和冗长。在这种情况下，我们可以使用`mapState`助手来减轻负担。让我们在下一节中看看如何使用它。

### mapState 助手

我们可以使用`mapState`助手来帮助我们生成计算状态函数，以节省一些代码行和按键次数，具体步骤如下：

1.  创建一个具有多个状态属性的存储：

```js
// vuex-sfc/state/mapstate/src/entry.js
const store = new Vuex.Store({
  state: {
    experience: 1,
    name: 'John',
    age: 20,
    job: 'designer'
  }
})
```

1.  从 Vuex 中导入`mapState`助手，并将状态属性作为数组传递给`mapState`方法：

```js
// vuex-sfc/state/mapstate/src/app.vue
import { mapState } from 'vuex'

export default {
  computed: mapState([
    'experience', 'name', 'age', 'job'
  ])
}
```

只要映射的计算属性的名称与状态属性名称相同，这就可以完美地工作。但是，最好使用对象扩展运算符，以便我们可以在`computed`属性中混合多个`mapState`助手：

```js
computed: {
  ...mapState({
    // ...
  })
}
```

例如，您可能希望根据子组件中的数据计算状态数据，如下所示：

```js
// vuex-sfc/state/mapstate/src/app.vue
import { mapState } from 'vuex'

export default {
  data () {
    return { localExperience: 2 }
  },
  computed: {
    ...mapState([
      'experience', 'name', 'age', 'job'
    ]),
    ...mapState({
      experienceTotal (state) {
        return state.experience + this.localExperience
      }
    })
  }
}
```

您还可以传递一个字符串值来为`experience`状态属性创建一个别名，如下所示：

```js
...mapState({
  experienceAlias: 'experience'
})
```

1.  将计算状态属性添加到`<template>`中，如下所示：

```js
// vuex-sfc/state/mapstate/src/app.vue
<p>{{ name }}, {{ age }}, {{ job }}</p>
<p>{{ experience }}, {{ experienceAlias }}, {{ experienceTotal }}</p>
```

您应该在浏览器上获得以下结果：

```js
John, 20, designer
1, 1, 3
```

您可能会想知道，既然我们可以在子组件中计算状态数据，那么我们是否可以在存储本身中计算状态数据？答案是肯定的，我们可以通过 getter 来实现，我们将在下一节中介绍。让我们开始吧。

## getter

您可以在存储的`getters`属性中定义 getter 方法，以在子组件中使用视图之前计算状态。就像`computed`属性一样，getter 中的计算结果是响应式的，但它是被缓存的，并且会在其依赖项发生更改时更新。getter 以状态作为第一个参数，以`getters`作为第二个参数。让我们创建一些 getter 并在子组件中使用它们，以下是具体步骤：

1.  创建一个具有项目列表的`state`属性的存储，并为访问这些项目创建一些 getter：

```js
// vuex-sfc/getters/basic/src/entry.js
const store = new Vuex.Store({
  state: {
    fruits: [
      { name: 'strawberries', type: 'berries' },
      { name: 'orange', type: 'citrus' },
      { name: 'lime', type: 'citrus' }
    ]
  },
  getters: {
    getCitrus: state => {
      return state.fruits.filter(fruit => fruit.type === 'citrus')
    },
    countCitrus: (state, getters) => {
      return getters.getCitrus.length
    },
    getFruitByName: (state, getters) => (name) => {
      return state.fruits.find(fruit => fruit.name === name)
    }
  }
})
```

在此存储中，我们创建了`getCitrus`方法来获取所有类型为`citrus`的项目，并且`countCitrus`方法依赖于`getCitrus`方法的结果。第三个方法`getFruitByName`用于通过柑橘名称获取列表中的特定项目。

1.  在`computed`属性中创建一些方法来执行存储中的 getter，如下所示：

```js
// vuex-sfc/getters/basic/src/app.vue
export default {
  computed: {
    totalCitrus () {
      return this.$store.getters.countCitrus
    },
    getOrange () {
      return this.$store.getters.getFruitByName('orange')
    }
  }
}
```

1.  将计算状态属性添加到`<template>`中，如下所示：

```js
// vuex-sfc/getters/basic/src/app.vue
<p>{{ totalCitrus }}</p>
<p>{{ getOrange }}</p>
```

您应该在浏览器中获得以下结果：

```js
2
{ "name": "orange", "type": "citrus" }
```

与`mapState`助手一样，我们可以在`computed`属性中使用`mapGetters`助手，这样可以节省一些行和按键。让我们在下一节中进行。

### mapGetters 助手

就像`mapState`助手一样，我们可以使用`mapGetters`助手将存储获取器映射到`computed`属性中。让我们看看如何在以下步骤中使用它：

1.  从 Vuex 中导入`mapGetters`助手，并将获取器作为数组传递给`mapGetters`方法，使用对象扩展运算符，以便我们可以在`computed`属性中混合多个`mapGetters`助手：

```js
// vuex-sfc/getters/mapgetters/src/app.vue
import { mapGetters } from 'vuex'

export default {
  computed: {
    ...mapGetters([
      'countCitrus'
    ]),
    ...mapGetters({
      totalCitrus: 'countCitrus'
    })
  }
}
```

在上述代码中，我们通过将字符串值传递给`totalCitrus`键为`countCitrus`获取器创建了别名。请注意，使用对象扩展运算符，我们还可以在`computed`属性中混合其他原始方法。因此，让我们在这些`mapGetters`助手之上的`computed`选项中添加一个原始的`getOrange`获取器方法，如下所示：

```js
// vuex-sfc/getters/mapgetters/src/app.vue
export default {
  computed: {
    // ... mapGetters
    getOrange () {
      return this.$store.getters.getFruitByName('orange')
    }
  }
}
```

1.  将计算状态属性添加到`<template>`中，如下所示：

```js
// vuex-sfc/getters/mapgetters/src/app.vue
<p>{{ countCitrus }}</p>
<p>{{ totalCitrus }}</p>
<p>{{ getOrange }}</p>
```

您应该在浏览器中获得以下结果：

```js
2
2
{ "name": "orange", "type": "citrus" }
```

到目前为止，您已经学会了如何通过使用计算方法和获取器来访问存储中的状态。那么改变状态呢？让我们在下一节中进行。

## 变异

就像我们在前面的部分中提到的一样，存储状态必须通过变异显式提交。变异就像存储属性中学到的任何其他函数一样，它必须在存储的`mutations`属性中定义，它总是以状态作为第一个参数。让我们创建一些变异并在子组件中使用它们，如下所示：

1.  创建一个带有`state`属性和一些变异方法的存储，我们可以使用这些方法来改变状态，如下所示：

```js
// vuex-sfc/mutations/basic/src/entry.js
const store = new Vuex.Store({
  state: { number: 1 },
  mutations: {
    multiply (state) {
      state.number = state.number * 2
    },
    divide (state) {
      state.number = state.number / 2
    },
    multiplyBy (state, n) {
      state.number = state.number  n
    }
  }
})
```

1.  在组件中创建以下方法，通过使用`this.$store.commit`来调用提交变异：

```js
// vuex-sfc/mutations/basic/src/app.js
export default {
  methods: {
    multiply () {
      this.$store.commit('multiply')
    },
    multiplyBy (number) {
      this.$store.commit('multiply', number)
    },
    divide () {
      this.$store.commit('divide')
    }
  }
}
```

就像获取方法一样，您还可以在变异方法上使用`mapMutations`助手，所以让我们在下一节中进行。

### mapMutations 助手

我们可以使用`mapMutations`助手将组件方法映射到变异方法，以便我们可以在`method`属性中混合多个`mapMutations`助手。让我们看看如何在以下步骤中做到这一点：

1.  从 Vuex 中导入`mapMutations`辅助程序，并使用对象扩展运算符将变异作为数组传递给`mapMutations`方法，如下所示：

```js
// vuex-sfc/mutations/mapmutations/src/app.vue
import { mapMutations } from 'vuex'

export default {
  computed: {
    number () {
      return this.$store.state.number
    }
  },
  methods: {
    ...mapMutations([
      'multiply',
      'multiplyBy',
      'divide'
    ]),
    ...mapMutations({
      square: 'multiply'
    })
  }
}
```

1.  将计算状态属性和方法添加到`<template>`中，如下所示：

```js
// vuex-sfc/mutations/mapmutations/src/app.vue
<p>{{ number }}</p>
<p>
  <button v-on:click="multiply">x 2</button>
  <button v-on:click="divide">/ 2</button>
  <button v-on:click="square">x 2 (square)</button>
  <button v-on:click="multiplyBy(10)">x 10</button>
</p>
```

当您单击上述按钮时，您应该看到`number`状态在浏览器中被动态地乘以或除以。在这个例子中，我们已经通过变异来改变状态值，这是 Vuex 中的规则之一。另一个规则是*不要*在变异中进行异步调用。换句话说，*变异必须是同步的*，以便可以通过 DevTool 进行调试。如果要进行异步调用，请使用操作，我们将在下一节中为您介绍。让我们开始吧。

## 操作

操作和变异一样，都是函数，不同的是它们不用于改变状态，而是用于提交变异。与变异不同，操作*可以是异步的*。我们在存储的`actions`属性中创建操作方法。操作方法以上下文对象作为第一个参数，您的自定义参数作为第二个参数等等。您可以使用`context.commit`来提交一个变异，`context.state`来访问状态，以及`context.getters`来访问获取器。让我们通过以下步骤添加一些操作方法：

1.  创建一个带有`state`属性和操作方法的存储，如下所示：

```js
// vuex-sfc/actions/basic/src/entry.js
const store = new Vuex.Store({
  state: { number: 1 },
  mutations: { ... },
  actions: {
    multiplyAsync (context) {
      setTimeout(() => {
        context.commit('multiply')
      }, 1000)
    },
    multiply (context) {
      context.commit('multiply')
    },
    multiplyBy (context, n) {
      context.commit('multiplyBy', n)
    },
    divide (context) {
      context.commit('divide')
    }
  }
})
```

在这个例子中，我们使用了上一节中的相同变异，并创建了操作方法，其中一个创建了一个异步操作方法，以演示为什么我们需要操作来进行异步调用，尽管它们一开始看起来有点麻烦。

请注意，如果您愿意，您可以使用 ES6 JavaScript 解构赋值来解构`context`并直接导入`commit`属性，如下所示：

```js
divide ({ commit }) {
  commit('divide')
}
```

1.  创建一个组件，并使用`this.$store.commit`分派前面的操作，如下所示：

```js
// vuex-sfc/actions/basic/src/app.js
export default {
  methods: {
    multiply () {
      this.$store.dispatch('multiply')
    },
    multiplyAsync () {
      this.$store.dispatch('multiplyAsync')
    },
    multiplyBy (number) {
      this.$store.dispatch('multiply', number)
    },
    divide () {
      this.$store.dispatch('divide')
    }
  }
}
```

与变异和获取器方法一样，您还可以在操作方法上使用`mapActions`辅助程序，因此让我们在下一节中进行操作。

### mapActions 辅助程序

我们可以使用`mapActions`辅助程序将组件方法映射到操作方法，使用对象扩展运算符，以便我们可以在`method`属性中混合多个`mapActions`辅助程序。让我们看看如何通过以下步骤来实现这一点：

1.  从 Vuex 中导入`mapActions`辅助程序，并使用对象扩展运算符将变异作为数组传递给`mapActions`方法，如下所示：

```js
// vuex-sfc/actions/mapactions/src/app.vue
import { mapActions } from 'vuex'

export default {
  methods: {
    ...mapActions([
      'multiply',
      'multiplyAsync',
      'multiplyBy',
      'divide'
    ]),
    ...mapActions({
      square: 'multiply'
    })
  }
}
```

1.  添加计算状态属性并将方法绑定到`<template>`，如下所示：

```js
// vuex-sfc/mapactions/src/app.vue
<p>{{ number }}</p>
<p>
  <button v-on:click="multiply">x 2</button>
  <button v-on:click="square">x 2 (square)</button>
  <button v-on:click="multiplyAsync">x 2 (multiplyAsync)</button>
  <button v-on:click="divide">/ 2</button>
  <button v-on:click="multiplyBy(10)">x 10</button>
</p>

export default {
  computed: {
    number () {
      return this.$store.state.number
    }
  },
}
```

当您点击前面的按钮时，您应该看到`number`状态在浏览器上被动地进行乘法或除法运算。在这个例子中，我们再次通过提交 mutations 来改变状态值，而这些 mutations 只能通过使用 store 的 dispatch 方法来分发。这些是我们在应用 store 时必须遵守的强制规则。

然而，当 store 和应用程序增长时，我们可能希望将状态、mutations 和 actions 分成组。在这种情况下，我们将需要 Vuex 中的最后一个概念-模块-这将在下一节中介绍。让我们开始吧。

## 模块

我们可以将我们的 store 分成模块以扩展应用程序。每个模块可以有状态、mutations、actions 和 getters，如下所示：

```js
const module1 = {
  state: { ... },
  mutations: { ... },
  actions: { ... },
  getters: { ... }
}

const module2 = {
  state: { ... },
  mutations: { ... },
  actions: { ... },
  getters: { ... }
}

const store = new Vuex.Store({
  modules: {
    a: module1,
    b: module2
  }
})
```

然后，您可以访问每个模块的状态或其他属性，如下所示：

```js
store.state.a
store.state.b
```

在为您的 store 编写模块时，您应该理解本地状态、根状态和 store 模块中的命名空间。让我们在接下来的章节中看一下它们。

### 理解本地状态和根状态

每个模块中的 mutations 和 getters 将接收模块的本地状态作为它们的第一个参数，如下所示：

```js
const module1 = {
  state: { number: 1 },
  mutations: {
    multiply (state) {
      console.log(state.number)
    }
  },

  getters: {
    getNumber (state) {
      console.log(state.number)
    }
  }
}
```

在这段代码中，mutation 和 getter 方法中的状态是本地模块状态，因此您将得到`1`作为`console.log(state.number)`的输出，而在每个模块的 actions 中，您将得到上下文作为第一个参数，您可以使用它来访问本地状态和根状态，如`context.state`和`context.rootState`，如下所示：

```js
const module1 = {
  actions: {
    doSum ({ state, commit, rootState }) {
      //...
    }
  }
}
```

根状态也可以作为每个模块的 getters 的第三个参数使用，如下所示：

```js
const module1 = {
  getters: {
    getSum (state, getters, rootState) {
      //...
    }
  }
}
```

当我们有多个模块时，来自模块的本地状态和来自 store 根的根状态可能会混淆和变得令人困惑。这就引出了命名空间，它可以使我们的模块更加自包含，减少与其他模块冲突的可能性。让我们在下一节中讨论它。

### 理解命名空间

默认情况下，每个模块中的`actions`、`mutations`和`getters`属性都在全局命名空间下注册，因此这些属性中的键或方法名必须是唯一的。换句话说，一个方法名不能在两个不同的模块中重复，如下所示：

```js
// entry.js
const module1 = {
  getters: {
    getNumber (state) {
      return state.number
    }
  }
}

const module2 = {
  getters: {
    getNumber (state) {
      return state.number
    }
  }
}
```

对于上面的例子，由于在 getters 中使用了相同的方法名，您将看到以下错误：

```js
[vuex] duplicate getter key: getNumber
```

因此，为了避免重复，必须为每个模块显式命名方法名称，如下所示：

```js
getNumberModule1
getNumberModule2
```

然后，您可以在子组件中访问这些方法并进行映射，如下所示：

```js
// app.js
import { mapGetters } from 'vuex'

export default {
  computed: {
    ...mapGetters({
      getNumberModule1: 'getNumberModule1',
      getNumberModule2: 'getNumberModule2'
    })
  }
}
```

如果您不想像前面的代码中那样使用`mapGetters`，这些方法也可以写成如下形式：

```js
// app.js
export default {
  computed: {
    getNumberModule1 (state) {
      return this.$store.getters.getNumberModule1
    },
    getNumberModule2 (state) {
      return this.$store.getters.getNumberModule2
    }
  }
}
```

然而，这种模式可能看起来很冗长，因为我们必须为存储中创建的每个方法重复编写`this.$store.getters`或`this.$store.actions`。访问每个模块的状态也是如此，如下所示：

```js
// app.js
export default {
  computed: {
    ...mapState({
      numberModule1 (state) {
        return this.$store.state.a.number
      }
    }),
    ...mapState({
      numberModule2 (state) {
        return this.$store.state.b.number
      }
    })
  }
}
```

因此，解决这种情况的方法是通过在每个模块中设置`namespaced`键为`true`来为每个模块使用命名空间，如下所示：

```js
const module1 = {
  namespaced: true
}
```

当模块注册时，其所有 getter、action 和 mutation 将根据模块注册的路径自动命名空间化。接下来是一个示例：

```js
// entry.js
const module1 = {
  namespaced: true
  state: { number:1 }
}

const module2 = {
  namespaced: true
  state: { number:2 }
}

const store = new Vuex.Store({
  modules: {
    a: module1,
    b: module2
  }
})
```

现在，您可以更轻松地访问每个模块的状态，而代码量更少，如下所示：

```js
// app.js
import { mapState } from 'vuex'

export default {
  computed: {
    ...mapState('a', {
      numberModule1 (state) {
        return state.number
      }
    }),
    ...mapState('b', {
      numberModule2 (state) {
        return state.number
      }
    })
  }
}
```

对于上述示例代码，您将获得`numberModule1`的`1`和`numberModule2`的`2`。此外，您还可以通过使用命名空间来消除“重复的 getter 键”错误。因此，现在，您可以为方法使用更“抽象”的名称，如下所示：

```js
// entry.js
const module1 = {
  getters: {
    getNumber (state) {
      return state.number
    }
  }
}

const module2 = {
  getters: {
    getNumber (state) {
      return state.number
    }
  }
}
```

现在，您可以精确地调用和映射这些方法，并使用它们注册的命名空间，如下所示：

```js
// app.js
import { mapGetters } from 'vuex'

export default {
  computed: {
    ...mapGetters('a', {
      getNumberModule1: 'getNumber',
    }),
    ...mapGetters('b', {
      getNumberModule2: 'getNumber',
    })
  }
}
```

我们一直在根文件`entry.js`中编写存储。无论您是写入模块化存储还是不写入，当随着时间的推移状态属性和 mutations、getters 和 actions 中的方法增长时，这个根文件将变得臃肿。因此，这将引导我们进入下一节，在那里您将学习如何将这些方法和状态属性分离和结构化到它们自己的单独文件中。让我们开始吧。

# 结构化 Vuex 存储模块

在 Vue 应用程序中，只要您遵守我们在前几节中介绍的强制规则，对于如何构建您的存储结构，没有严格的限制。根据您的存储结构的复杂程度，本书中有两种推荐的结构，您可以在接下来的章节中使用。让我们开始吧。

## 创建一个简单的存储模块结构

在这种简单的模块结构中，您可以有一个包含此文件夹中所有模块的`/store/`目录，其中包含一个`/modules/`目录。以下是创建此简单项目结构的步骤：

1.  创建一个包含存储模块的`/store/`目录，并在其中包含一个`/modules/`目录，如下所示：

```js
// vuex-sfc/structuring-modules/basic/
├── index.html
├── entry.js
├── components
│ ├── app.vue
│ └── ...
└── store
    ├── index.js
    ├── actions.js
    ├── getters.js
    ├── mutations.js
    └── modules
        ├── module1.js
        └── module2.js
```

在这个简单的结构中，`/store/index.js` 是我们从 `/modules/` 目录中组装模块并导出 store，以及根状态、actions、getters 和 mutations 的地方，如下所示：

```js
// store/index.js
import Vue from 'vue'
import actions from './actions'
import getters from './getters'
import mutations from './mutations'
import module1 from './modules/module1'
import module2 from './modules/module2'

import Vuex from 'vuex'
Vue.use(Vuex)

export default new Vuex.Store({
  state: {
    number: 3
  },
  actions,
  getters,
  mutations,
  modules: {
    a: module1,
    b: module2
  }
})
```

1.  将根的 actions、mutations 和 getters 拆分为单独的文件，并在根索引文件中组装它们，如下所示：

```js
// store/mutations.js
export default {
  mutation1 (state) {
    //...
  },
  mutation2 (state, n) {
    //...
  }
}
```

1.  创建 `.js` 文件的模块，其中包含它们的状态、actions、mutations 和 getters，就像你在前面的部分中学到的那样，如下所示：

```js
// store/modules/module1.js
export default {
  namespaced: true,
  state: {
    number: 1
  },
  mutations: { ... },
  getters: { ... },
  actions: { ... }
}
```

如果一个模块文件变得太大，我们可以将模块的状态、actions、mutations 和 getters 拆分为单独的文件。这将带我们进入一个高级的 store 模块结构，我们将在下一节中进行讨论。让我们来看看。

## 创建一个高级的 store 模块结构

在这个高级模块结构中，你可以有一个包含 `/modules/` 目录的 `/store/` 目录，该目录的子文件夹中包含所有模块。我们可以将模块的状态、actions、mutations 和 getters 拆分为单独的文件，然后将它们保存在模块文件夹中，具体步骤如下：

1.  创建一个包含 `/modules/` 目录的 `/store/` 目录，用于存放 store 模块，如下所示：

```js
// vuex-sfc/structuring-modules/advanced/
├── index.html
├── entry.js
├── components
│ └── app.vue
└── store
    ├── index.js
    ├── action.js
    └── ...
      ├── module1
      │ ├── index.js
      │ ├── state.js
      │ ├── mutations.js
      │ └── ...
      └── module2
          ├── index.js
          ├── state.js
          ├── mutations.js
          └── ...
```

在这个更复杂的项目结构中，`/store/module1/index.js` 是我们组装 `module1` 的地方，而 `/store/module2/index.js` 是我们组装 `module2` 的地方，如下所示：

```js
// store/module1/index.js
import state from './state'
import getters from './getters'
import actions from './actions'
import mutations from './mutations'

export default {
  namespaced: true,
  state,
  getters,
  actions,
  mutations
}
```

我们还可以将一个模块的状态拆分为单独的文件，如下所示：

```js
// store/module1/state.js
export default () => ({
  number: 1
})
```

1.  将模块的 actions、mutations 和 getters 拆分为单独的文件，然后在前面的模块索引文件中组装它们，如下所示：

```js
// store/module1/mutations.js
export default {
  mutation1 (state) {
    //...
  },
  mutation2 (state, n) {
    //...
  }
}
```

1.  将模块索引文件导入到 store 根目录，我们在那里组装模块并导出 store，如下所示：

```js
// store/index.js
import module1 from './module1'
import module2 from './module2'
```

1.  打开严格模式以确保 store 状态只在 `mutations` 属性中被改变，如下所示：

```js
const store = new Vuex.Store({
  strict: true,
  ...
})
```

使用严格模式是一个好习惯，提醒我们只在 `mutations` 属性内改变任何状态。因此，在开发过程中，如果在 `mutations` 属性之外改变 store 状态，将会抛出错误。然而，我们应该在生产环境中禁用它，因为当 store 中有大量状态变化时，它可能会影响性能。因此，我们可以使用构建工具动态关闭它，具体步骤如下：

```js
// store/index.js
const debug = process.env.NODE_ENV !== 'production'

const store = new Vuex.Store({
  strict: debug,
  ...
})
```

然而，在处理 store 中的表单时，使用严格模式有一个注意事项，我们将在下一节中介绍。

# 在 Vuex store 中处理表单

当我们在 Vue 应用中使用`v-model`进行双向数据绑定时，Vue 实例中的数据将与 v-model 输入字段同步。因此，当你在输入字段中输入任何内容时，数据将立即更新。然而，在 Vuex 存储中，这将会创建问题，因为我们*绝对不能*在`mutations`属性之外改变存储状态（数据）。让我们看一个在 Vuex 存储中的简单双向数据绑定：

```js
// vuex-non-sfc/handling-forms/v-model.html
<input v-model="user.message" />

const store = new Vuex.Store({
  strict: true,
  state: {
    message: ''
  }
})

new Vue({
  el: 'demo',
  store: store,
  computed: {
    user () {
      return this.$store.state.user
    }
  }
})
```

在这个例子中，当你在输入字段中输入消息时，你会在浏览器的调试工具中看到以下错误消息：

```js
Error: [vuex] do not mutate vuex store state outside mutation handlers.
```

这是因为当你输入时，`v-model`试图直接改变存储状态中的`message`，所以在严格模式下会导致错误。让我们在接下来的部分看看我们有哪些选项来解决这个问题。

## 使用 v-bind 和 v-on 指令

在大多数情况下，双向绑定并不总是合适的。在 Vuex 中，更合理的做法是使用单向绑定和显式数据更新，通过将`<input>`与`input`或`change`事件上的`value`属性进行绑定。你可以通过以下步骤轻松实现这一点：

1.  创建一个用于改变`mutations`属性中状态的方法，就像你在之前的部分学到的那样：

```js
// vuex-sfc/form-handling/value-event/store/index.js
export default new Vuex.Store({
  strict: true,
  state: {
    message: ''
  },
  mutations: {
    updateMessage (state, message) {
      state.message = message
    }
  }
})
```

1.  将`<input>`元素与`value`属性和`input`事件与方法进行绑定，如下所示：

```js
// vuex-sfc/form-handling/value-event/components/app.vue
<input v-bind:value="message" v-on:input="updateMessage" />

import { mapState } from 'vuex'

export default {
  computed: {
    ...mapState({
      message: state => state.message
    })
  },
  methods: {
    updateMessage (e) {
      this.$store.commit('updateMessage', e.target.value)
    }
  }
}
```

在这个解决方案中，我们在子组件中使用`updateMessage`方法来提交存储中的`updateMessage`变异方法，并传递输入事件的值。通过像这样*显式*地提交变异，我们不违反 Vuex 中必须遵守的强制规则。因此，采用这个解决方案意味着你不能使用 v-model 来处理 Vuex 存储的表单。然而，如果你使用 Vue 本身的计算 getter 和 setter，你仍然可以使用它。让我们在下一节中看看这个。

## 使用双向计算属性

我们可以使用 Vue 内置的双向计算属性和 setter 来处理带有 v-model 的表单，以下是帮助的步骤：

1.  创建一个用于改变`mutations`属性中状态的方法，就像在前一节中一样。

1.  将`get`和`set`方法应用于`message`键，如下所示：

```js
// vuex-sfc/form-handling/getter-setter/components/app.vue
<input v-model="message" />

export default {
  computed: {
    message: {
      get () {
        return this.$store.state.message
      },
      set (value) {
        this.$store.commit('updateMessage', value)
      }
    }
  }
}
```

然而，这对于简单的计算属性可能效果很好。如果你有一个深层级对象，需要更新超过 10 个键，你将需要 10 组双向计算属性（getter 和 setter）。代码最终会比基于事件的解决方案更加重复和冗长。

干得好！您已经成功掌握了 Vuex 存储的基础和概念。您已经学会了如何在 Vue 应用程序中使用存储。现在，是时候继续在 Nuxt 中应用存储了。因此，让我们在下一节中开始吧。

如果您想了解更多关于 Vuex 的信息，请访问[`vuex.vuejs.org/`](https://vuex.vuejs.org/)。

# 在 Nuxt 中使用 Vuex 存储

在 Nuxt 中，Vuex 已经为您安装好了。您只需要确保项目根目录中存在`/store/`目录。如果您使用`create-nuxt-app`安装 Nuxt 项目，此目录将在项目安装期间为您自动生成。在 Nuxt 中，您可以以两种不同的模式创建您的存储：

+   模块

+   经典模式（已弃用）

由于经典模式已经弃用，本书将只关注模块模式。因此，让我们在下一节中开始吧。

您可以在我们的 GitHub 存储库的`/chapter-10/nuxt-universal/`中找到所有以下 Nuxt 示例的源代码。

## 使用模块模式

不同于 Vue 应用，在 Nuxt 中，默认情况下，每个模块的`namespaced`键都设置为`true`，以及根模块。此外，在 Nuxt 中，您不需要在存储根中组装模块；您只需要将状态*作为函数*导出，并将变化、获取器和操作*作为对象*在根和模块文件中。让我们按照以下步骤开始：

1.  创建一个存储根，如下所示：

```js
// store/index.js
export const state = () => ({
  number: 3
})

export const mutations = {
  mutation1 (state) { ... }
}

export const getters = {
  getter1 (state, getter) { ... }
}

export const actions = {
  action1 ({ state, commit }) { ... }
}
```

在 Nuxt 中，默认情况下，Vuex 的`strict`模式在开发过程中设置为`true`，并在生产模式下自动关闭，但您可以在开发过程中禁用它，如下所示：

```js
// store/index.js
export const strict = false
```

1.  创建一个模块，如下所示：

```js
// store/module1.js
export const state = () => ({
  number: 1
})

export const mutations = {
  mutation1 (state) { ... }
}

export const getters = {
  getter1 (state, getter, rootState) { ... }
}

export const actions = {
  action1 ({ state, commit, rootState }) { ... }
}
```

然后，就像我们在上一节中在 Vue 应用程序中*手动*做的那样，存储将被自动生成，如下所示：

```js
new Vuex.Store({
  state: () => ({
    number: 3
  }),
  mutations: {
    mutation1 (state) { ... }
  },
  getters: {
    getter1 (state, getter) { ... }
  },
  actions: {
    action1 ({ state, commit }) { ... }
  },
  modules: {
    module1: {
      namespaced: true,
      state: () => ({
        number: 1
      }),
      mutations: {
        mutation1 (state) { ... }
      }
      ...
    }
  }
})
```

1.  在任何页面的`<script>`块中映射所有存储状态、获取器、变化和操作，如下所示：

```js
// pages/index.vue
import { mapState, mapGetters, mapActions } from 'vuex'

export default {
  computed: {
    ...mapState({
      numberRoot: state => state.number,
    }),
    ...mapState('module1', {
      numberModule1: state => state.number,
    }),
    ...mapGetters({
      getNumberRoot: 'getter1'
    }),
    ...mapGetters('module1', {
      getNumberModule1: 'getter1'
    })
  },
  methods: {
    ...mapActions({
      doNumberRoot:'action1'
    }),
    ...mapActions('module1', {
      doNumberModule1:'action1'
    })
  }
}
```

1.  在`<template>`块中显示计算属性和方法以提交变化，如下所示：

```js
// pages/index.vue
<p>{{ numberRoot }}, {{ getNumberRoot }}</p>
<button v-on:click="doNumberRoot">x 2 (root)</button>

<p>{{ numberModule1 }}, {{ getNumberModule1 }}</p>
<button v-on:click="doNumberModule1">x 2 (module1)</button>
```

您应该在屏幕上看到以下初始结果，并且当您点击屏幕上显示的前面按钮时，它们将被改变：

```js
3, 3
1, 1
```

正如我们之前提到的，您不需要在 Nuxt 的存储根中组装模块，因为它们会被 Nuxt“自动组装”给您，只要您使用以下结构：

```js
// chapter-10/nuxt-universal/module-mode/
└── store
    ├── index.js
    ├── module1.js
    ├── module2.js
    └── ...
```

但是，如果您要像我们为 Vue 应用程序手动组装模块一样，在存储根中使用以下结构：

```js
// chapter-10/vuex-sfc/structuring-modules/basic/
└── store
    ├── index.js
    ├── ...
    └── modules
        ├── module1.js
        └── module2.js
```

您将在 Nuxt 应用程序中看到以下错误：

```js
ERROR [vuex] module namespace not found in mapState(): module1/
ERROR [vuex] module namespace not found in mapGetters(): module1/
```

要修复这些错误，您需要明确告诉 Nuxt 这些模块存放在哪里。

```js
export default {
  computed: {
    ..mapState('modules/module1', {
      numberModule1: state => state.number,
    }),
    ...mapGetters('modules/module1', {
      getNumberModule1: 'getter1'
    })
  },
  methods: {
    ...mapActions('modules/module1', {
      doNumberModule1:'action1'
    })
  }
}
```

就像在 Vue 应用程序中的 Vuex 一样，我们也可以在 Nuxt 应用程序中将状态、操作、突变和获取器拆分为单独的文件。让我们看看我们如何做到这一点，以及 Nuxt 的区别在下一节中。

## 使用模块文件

我们可以将模块中的大文件拆分为单独的文件 - `state.js`、`actions.js`、`mutations.js`和`getters.js` - 用于商店根目录和每个模块。因此，让我们按照以下步骤进行：

1.  为商店根目录创建状态、操作、突变和获取器的单独文件，如下所示：

```js
// store/state.js
export default () => ({
  number: 3
})

// store/mutations.js
export default {
  mutation1 (state) { ... }
}
```

1.  为模块创建状态、操作、突变和获取器的单独文件，如下所示：

```js
// store/module1/state.js
export default () => ({
  number: 1
})

// store/module1/mutations.js
export default {
  mutation1 (state) { ... }
}
```

同样，在 Nuxt 中，我们不需要像在 Vue 应用程序中那样使用`index.js`来组装这些单独的文件。只要我们使用以下结构，Nuxt 就会为我们完成这些工作：

```js
// chapter-10/nuxt-universal/module-files/
└── store
    ├── state.js
    ├── action.js
    └── ...
      ├── module1
      │ ├── state.js
      │ ├── mutations.js
      │ └── ...
      └── module2
          ├── state.js
          ├── mutations.js
          └── ...
```

我们可以将这与我们为 Vue 应用程序使用的以下结构进行比较，其中我们需要一个`index.js`文件用于商店根目录和每个模块，以从单独的文件中组装状态、操作、突变和获取器：

```js
// chapter-10/vuex-sfc/structuring-modules/advanced/
└── store
    ├── index.js
    ├── action.js
    └── ...
      ├── module1
      │ ├── index.js
      │ ├── state.js
      │ ├── mutations.js
      │ └── ...
      └── module2
          ├── index.js
          ├── state.js
          ├── mutations.js
          └── ...
```

所以，商店在 Nuxt 中是开箱即用的，它为您节省了一些代码行来组装文件和注册模块。很棒，不是吗？现在，让我们再进一步，看看我们如何在 Nuxt 中使用`fetch`方法*动态填充商店状态*。

## 使用`fetch`方法

我们可以使用`fetch`方法在页面呈现之前填充商店状态。它的工作方式与我们已经介绍过的`asyncData`方法相同 - 在加载组件之前每次都会被调用。它在服务器端调用一次，然后在客户端导航到其他路由时再次调用。就像`asyncData`一样，我们可以在`fetch`方法中使用`async/await`来处理异步数据。它在组件创建后被调用，因此我们可以通过`this`在`fetch`方法中访问组件实例。因此，我们可以通过`this.$nuxt.context.store`访问商店。让我们使用以下步骤使用这种方法创建一个简单的 Nuxt 应用程序：

1.  使用`fetch`方法在任何页面异步请求远程 API 的用户列表，如下所示：

```js
// pages/index.vue
import axios from 'axios'

export default {
  async fetch () {
    const { store } = this.$nuxt.context
    await store.dispatch('users/getUsers')
  }
}
```

1.  创建一个带有状态、突变和操作的`user`模块，如下所示：

```js
// store/users/state.js
export default () => ({
  list: {}
})

// store/users/mutations.js
export default {
  setUsers (state, data) {
    state.list = data
  },
  removeUser (state, id) {
    let found = state.list.find(todo => todo.id === id)
    state.list.splice(state.list.indexOf(found), 1)
  }
}

// store/users/actions.js
export default {
  setUsers ({ commit }, data) {
    commit('setUsers', data)
  },
  removeUser ({ commit }, id) {
    commit('removeUser', id)
  }
}
```

在突变和操作中使用`setUsers`方法将用户列表设置到状态中，而`removeUser`方法用于逐个从状态中移除用户。

1.  将状态和动作从页面映射到方法，如下所示：

```js
// pages/index.vue
import { mapState, mapActions } from 'vuex'

export default {
  computed: {
    ...mapState ('users', {
      users (state) {
        return state.list
      }
    })
  },
  methods: {
    ...mapActions('users', {
      removeUser: 'removeUser'
    })
  }
}
```

1.  在`<template>`块中循环并显示用户列表，如下所示：

```js
// pages/index.vue
<li v-for="(user, index) in users" v-bind:key="user.id">
  {{ user.name }}
  <button class="button" v-on:click="removeUser(user.id)">Remove</button>
</li>
```

当您在浏览器中加载应用程序时，您应该在屏幕上看到用户列表，并且可以单击“删除”按钮来删除用户。我们还可以在动作中使用`async/await`来获取远程数据，如下所示：

```js
// store/users/actions.js
import axios from 'axios'

export const actions = {
  async getUsers ({ commit }) {
    const { data } = await axios.get('https://jsonplaceholder.typicode.com/users')
    commit('setUsers', data)
  }
}
```

然后，我们可以像下面这样调度`getUsers`动作：

```js
// pages/index.vue
export default {
  async fetch () {
    const { store } = this.$nuxt.context
    await store.dispatch('users/getUsers')
  }
}
```

除了在 Nuxt 中使用`fetch`方法获取和填充状态之外，我们还可以使用`nuxtServerInit`动作，这只在 Nuxt 中可用。让我们继续在下一节中看一下它。

## 使用`nuxtServerInit`动作

与`asyncData`方法不同，它仅在页面级组件中可用，以及`fetch`方法可用于所有 Vue 组件（包括页面级组件），`nuxtServerInit`动作是一个保留的存储动作方法，仅在 Nuxt 存储中定义时可用。它只能在存储根目录的`index.js`文件中定义，并且仅在 Nuxt 应用程序初始化之前在服务器端调用。与在服务器端调用然后在后续路由上的客户端端调用的`asyncData`和`fetch`方法不同，`nuxtServerInit`动作方法仅在服务器端调用*一次*，除非您在浏览器中刷新*任何页面*。此外，与`asyncData`方法不同，它将 Nuxt 上下文对象作为其第一个参数，`nuxtServerInit`动作方法将其作为其第二个参数。它接收的第一个参数是存储上下文对象。让我们将这些上下文对象放入以下表格中：

| **第一个参数** | **第二个参数** |
| --- | --- |

|

+   `dispatch`

+   `commit`

+   `getters`

+   `state`

+   `rootGetters`

+   `rootState`

|

+   `isStatic`

+   `isDev`

+   `isHMR`

+   应用

+   `req`

+   `res`

+   `...`

|

因此，当我们想要从应用程序的任何页面从服务器端获取数据，然后使用服务器数据填充存储状态时，`nuxtServerInit`动作方法非常有用，例如，我们在用户登录到我们的应用程序时在服务器端存储在会话中的经过身份验证的用户数据。这个会话数据可以存储为 Express 中的`req.session.authUser`或 Koa 中的`ctx.session.authUser`。然后，我们可以通过`req`对象将`ctx.session`传递给`nuxtServerInit`。

让我们使用这种方法动作创建一个简单的用户登录应用，并使用 Koa 作为服务器端 API，你在第八章中学到了关于 Koa 的内容，*添加服务器端框架*。在我们可以将任何数据注入会话并使用`nuxtServerIni`动作方法创建存储之前，我们只需要对服务器端进行一点修改，以下是具体步骤：

1.  安装会话包`koa-session`，使用 npm：

```js
$ npm install koa-session
```

1.  导入并注册会话包作为中间件，如下所示：

```js
// server/middlewares.js
import session from 'koa-session'

app.keys = ['some secret hurr']
app.use(session(app))
```

1.  在服务器端创建两个路由，如下所示：

```js
// server/routes.js
router.post('/login', async (ctx, next) => {
  let request = ctx.request.body || {}
  if (request.username === 'demo' && request.password === 'demo') {
    ctx.session.authUser = { username: 'demo' }
    ctx.body = { username: 'demo' }
  } else {
    ctx.throw(401)
  }
})

router.post('/logout', async (ctx, next) => {
  delete ctx.session.authUser
  ctx.body = { ok: true }
})
```

在前面的代码中，我们使用`/login`路由将经过认证的用户数据`authUser`注入到 Koa 上下文`ctx`中，同时`/logout`用于取消认证数据。

1.  创建存储状态，包含一个`authUser`键来保存认证数据：

```js
// store/state.js
export default () => ({
  authUser: null
})
```

1.  创建一个变异方法，在前述状态中设置数据到`authUser`键：

```js
// store/mutations.js
export default {
  setUser (state, data) {
    state.authUser = data
  }
}
```

1.  在存储根目录创建一个`index.js`文件，包含以下动作：

```js
// store/index.js
export const actions = {
  nuxtServerInit({ commit }, { req }) {
    if (req.ctx.session && req.ctx.session.authUser) {
      commit('setUser', req.ctx.session.authUser)
    }
  },
  async login({ commit }, { username, password }) {
    const { data } = await axios.post('/api/login', { username, 
     password })
    commit('setUser', data.data)
  },
  async logout({ commit }) {
    await axios.post('/api/logout')
    commit('setUser', null)
  }
}
```

在前面的代码中，`nuxtServerInit`动作方法用于从服务器访问会话数据，并通过提交`setUser`变异方法来填充存储状态。`login`和`logout`动作方法用于验证用户登录凭据并取消认证。请注意，会话数据存储在`req.ctx`中，因为本书使用 Koa 作为服务器 API。如果你使用 Express，请使用以下代码：

```js
actions: {
  nuxtServerInit ({ commit }, { req }) {
    if (req.session.user) {
      commit('user', req.session.user)
    }
  }
}
```

就像`asyncData`和`fetch`方法一样，`nuxtServerInit`动作方法也可以是异步的。你只需要返回一个 Promise，或者使用`async/await`语句，让 Nuxt 服务器等待动作异步完成，如下所示：

```js
actions: {
  async nuxtServerInit({ commit }) {
    await commit('setUser', req.ctx.session.authUser)
  }
}

```

1.  创建一个表单来使用存储的动作方法，如下所示：

```js
// pages/index.vue
<form v-on:submit.prevent="login">
  <input v-model="username" type="text" name="username" />
  <input v-model="password" type="password" name="password" />
  <button class="button" type="submit">Login</button>
</form>

export default {
  data() {
    return {
      username: '',
      password: ''
    }
  },
  methods: {
    async login() {
      await this.$store.dispatch('login', {
        username: this.username,
        password: this.password
      })
    },
    async logout() {
      await this.$store.dispatch('logout')
    }
  }
}
```

我们已经简化了前面的代码和*步骤 6*的代码以适应这个页面，但你可以在我们的 GitHub 存储库的`/chapter-10/nuxt-universal/nuxtServerInit/`中找到它们的完整版本。

干得好！你终于通过了 Nuxt 和 Vue 的一个激动人心的特性-Vuex 存储。这是一个很长的章节，但它是非常重要的，因为我们将需要在接下来的章节中经常回到 Vuex 并经常使用它。现在，让我们总结一下你在这一章学到了什么。

# 总结

我们已经走了很长的路。在本章中，您了解了 Vuex 存储中的架构、核心概念、模块结构和表单处理。在这一点上，您应该知道 Vuex 存储只是与状态（或数据）集中化和管理有关，并且有一些必须遵守的强制规则。因此，对于您可能在存储中拥有的任何状态属性，正确的访问方式是通过在组件的`computed`属性中计算它。如果您想要更改状态属性的值，必须通过 mutations 对其进行变化，这必须是同步的。如果您想要进行异步调用以改变状态，则必须使用 actions 在组件中分发操作以提交 mutations。

您还学会了在 Nuxt 应用程序中创建存储比在 Vue 应用程序中更容易和简单，因为 Vuex 默认预安装在 Nuxt 上。此外，在 Nuxt 中，您无需手动组装模块及其所有方法，因为它们默认为您完成。此外，在 Nuxt 中，您可以使用`fetch`和`nuxtServerInit`方法在呈现页面组件和启动 Nuxt 应用程序之前使用服务器端 API 填充存储状态。最后，您已经成功使用`nuxtServerInit`操作方法创建了一个简单的用户登录应用程序，并为在即将到来的章节中创建用户登录和 API 身份验证铺平了道路。

在下一章中，我们将研究 Nuxt 中的中间件 - 具体来说，路由中间件和服务器中间件。您将学会区分这两种类型的 Nuxt 中间件。您将在 Vue 应用程序中使用导航守卫创建一些路由中间件，然后在 Nuxt 应用程序中创建中间件。然后，您将在`serverMiddleware`配置选项中编写一些 Nuxt 服务器中间件，作为您在第八章中学习创建的服务器端 API 的替代服务器 API。最后但并非最不重要的是，您将学习如何使用 Vue CLI 创建 Vue 应用程序，而不是使用自定义 webpack 配置创建的 Vue 应用程序。所以，让我们开始吧。
