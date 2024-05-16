添加 Vue 组件

正如我们在上一章中所述，Vue 组件是 Nuxt 视图的**可选部分**。您已经了解了 Nuxt 视图的各种组成部分：应用程序模板、HTML 头部、布局和页面。但是，我们还没有涵盖 Nuxt 中最小的单位-**Vue 组件**。因此，在本章中，您将学习它的工作原理以及如何利用`/components/`创建自定义组件。然后，您将学习如何创建全局和本地组件，以及基本和全局 mixin，并了解一些用于开发 Vue 或 Nuxt 应用程序的命名约定。最令人兴奋的是，您将发现如何将数据从父组件传递到子组件，以及如何从子组件向父组件发出数据。

在本章中，我们将涵盖以下主题：

+   理解 Vue 组件

+   创建单文件 Vue 组件

+   注册全局和本地组件

+   编写基本和全局 mixin

+   定义组件名称并使用命名约定

让我们开始吧！

# 第五章：理解 Vue 组件

我们在第二章中简要介绍了`/components/`目录，*开始使用 Nuxt*，但我们还没有亲自动手。到目前为止，我们知道如果使用 Nuxt 脚手架工具安装 Nuxt 项目，则该目录中有一个`Logo.vue`组件。该目录中的所有组件都是**Vue 组件**，就像`/pages/`目录中的页面组件一样。主要区别在于`/components/`目录中的这些组件不支持`asyncData`方法。让我们以`/chapter-4/nuxt-universal/sample-website/`中的`copyright.vue`组件为例：

```js
// components/copyright.vue
<template>
  <p v-html="copyright"></p>
</template>

<script>
export default {
  data () {
    return { copyright: '&copy; Lau Tiam Kok' }
  }
}
</script>
```

让我们尝试用`asyncData`函数替换前面代码中的`data`函数，如下所示：

```js
// components/copyright.vue
export default {
  asyncData () {
    return { copyright: '&copy; Lau Tiam Kok' }
  }
}
```

您将收到警告错误，浏览器控制台上会显示“属性或方法“copyright”未定义...”。那么，我们如何动态获取版权目的的数据呢？我们可以使用`fetch`方法直接在组件中使用 HTTP 客户端（例如`axios`）请求数据，如下所示：

1.  在项目目录中通过 npm 安装`axios`包：

```js
$ npm i axios
```

1.  导入`axios`并在`fetch`方法中请求数据，如下所示：

```js
// components/copyright.vue
import axios from 'axios'

export default {
  data () {
    return { copyright: null }
  },
  fetch () {
    const { data } = axios.get('http/path/to/site-info.json')
    this.copyright = data.copyright  
  }
}
```

这种方法可以正常工作，但是最好不要使用 HTTP 请求从有效负载中获取少量数据，最好是请求一次，然后将数据从父作用域传递到其子组件中，如下所示：

```js
// components/copyright.vue
export default {
  props: ['copyright']
}
```

在前面的片段中，子组件是`/components/`目录中的`copyright.vue`文件。这个解决方案的奥妙就在于在组件中使用`props`属性。这样更简单、更整洁，因此是一个优雅的解决方案！但是，如果我们要理解它是如何工作的，以及如何使用它，我们需要了解 Vue 的组件系统。

## 什么是组件？

组件是单一的、自包含的、可重用的 Vue 实例，具有自定义名称。我们使用 Vue 的`component`方法来定义组件。例如，如果我们想定义一个名为`post-item`的组件，我们会这样做：

```js
Vue.component('post-item', {
  data () {
    return { text: 'Hello World!' }
  },
  template: '<p>{{ text }}</p>'
})
```

做完这些之后，当使用`new`语句创建根 Vue 实例时，我们可以在 HTML 文档中将此组件用作`<post-item>`，如下所示：

```js
<div id="post">
  <post-item></post-item>
</div>

<script type="text/javascript">
  Vue.component('post-item', { ... }
  new Vue({ el: '#post' })
</script>
```

所有组件本质上都是 Vue 实例。这意味着它们具有与`new Vue`相同的选项（`data`、`computed`、`watch`、`methods`等），只是少了一些根特定的选项，比如`el`。此外，组件可以嵌套在其他组件中，并最终成为类似树状的组件。然而，当这种情况发生时，传递数据变得棘手。因此，在特定组件中直接使用`fetch`方法获取数据可能更适合这种情况。或者，您可以使用 Vuex 存储，您将在第十章中发现它，“添加 Vuex 存储”。

然而，我们将暂时搁置深度嵌套的组件，专注于本章中简单的父子组件，并学习如何在它们之间传递数据。数据可以从父组件传递到它们的子组件，也可以从子组件传递到父组件。但是我们如何做到这一点呢？首先，让我们找出如何从父组件向子组件传递数据。

## 使用 props 将数据传递给子组件

让我们通过创建一个名为`user-item`的子组件来创建一个小型的 Vue 应用，如下所示：

```js
Vue.component('user-item', {
  template: '<li>John Doe</li>'
})
```

你可以看到它只是一个静态组件，没有太多功能；你根本无法抽象或重用它。只有在我们可以动态地将数据传递到模板内部的`template`属性中时，它才变得可重用。这可以通过`props`属性来实现。让我们对组件进行重构，如下所示：

```js
Vue.component('user-item', {
  props: ['name'],
  template: '<li>{{ name }}</li>'
})
```

在某种意义上，`props`的行为类似于变量，我们可以使用`v-bind`指令为它们设置数据，如下所示：

```js
<ol>
  <user-item
    v-for="user in users"
    v-bind:name="user.name"
    v-bind:key="user.id"
  ></user-item>
</ol>
```

在这个重构的组件中，我们使用`v-bind`指令将`item.name`绑定到`name`，如`v-bind:name`。组件内的 props 必须接受`name`作为该组件的属性。然而，在一个更复杂的应用程序中，我们可能需要传递更多的数据，为每个数据写多个 props 可能会适得其反。因此，让我们重构`<user-item>`组件，使其接受一个名为`user`的单个 prop：

```js
<ol>
  <user-item
    v-for="user in users"
    v-bind:user="user"
    v-bind:key="user.id"
  ></user-item>
</ol>
```

现在，让我们再次重构组件代码，如下所示：

```js
Vue.component('user-item', {
  props: ['user'],
  template: '<li>{{ user.name }}</li>'
})
```

让我们将我们在这里所做的事情放到一个单页 HTML 中，这样您就可以看到更大的图片：

1.  在`<head>`块中包含以下 CDN 链接：

```js
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
```

1.  在`<body>`块中创建以下标记：

```js
<div id="app">
  <ol>
    <user-item
      v-for="user in users"
      v-bind:user="user"
      v-bind:key="user.id"
    ></user-item>
  </ol>
</div>
```

1.  将以下代码添加到`<script>`块中：

```js
Vue.component('user-item', {
  props: ['user'],
  template: '<li>{{ user.name }}</li>'
})

new Vue({
  el: '#app',
  data: {
    users: [
      { id: 0, name: 'John Doe' },
      { id: 1, name: 'Jane Doe' },
      { id: 2, name: 'Mary Moe' }
    ]
  }
})
```

在这个例子中，我们将应用程序分解成了更小的单元：一个子组件和一个父组件。然而，它们通过`props`属性进行绑定。现在，我们可以进一步完善它们，而不用担心它们相互干扰。

您可以在本书的 GitHub 存储库中的`/chapter-5/vue/component/basic.html`中找到这个示例代码。

然而，在一个真实而复杂的应用程序中，我们应该将这个应用程序分成更可管理的单独文件（单文件组件）。我们将在*创建单文件 Vue 组件*部分向您展示如何创建它们。但现在，让我们发现如何从子组件将数据传递给父组件。

## 监听子组件事件

到目前为止，您已经学会了如何使用`props`属性将数据传递给子组件。但是如何从子组件将数据传递给父组件呢？我们可以通过使用`$emit`方法和自定义事件来实现这一点，如下所示：

```js
$emit(<event>)
```

您可以选择任何名称作为要在子组件中广播的自定义事件的名称。然后，父组件可以使用`v-on`指令来监听这个广播事件，并决定接下来要做什么。

```js
v-on:<event>="<event-handler>"
```

因此，如果您正在发出一个名为`done`的自定义事件，那么父组件将使用`v-on:done`指令来监听这个`done`事件，然后是一个事件处理程序。这个事件处理程序可以是一个简单的 JavaScript 函数，比如`v-on:done=handleDone`。让我们创建一个简单的应用程序来演示这一点：

1.  创建应用程序的标记，如下所示：

```js
<div id="todos">
  <todo-item
    v-on:completed="handleCompleted"
  ></todo-item>
</div>
```

1.  创建一个子组件，如下所示：

```js
Vue.component('todo-item', {
  template: '<button v-on:click="clicked">Task completed</button>',
  methods: {
    clicked () {
      this.$emit('completed')
    }
  }
})
```

1.  创建一个 Vue 根实例作为父级：

```js
new Vue({
  el: '#todos',
  methods: {
    handleCompleted () {
      alert('Task Done')
    }
  }
})
```

在这个例子中，当`clicked`方法在子组件中触发时，子组件将发出一个`completed`事件。在这里，父组件通过`v-on`接收事件，然后在其端触发`handleCompleted`方法。

您可以在本书的 GitHub 存储库中的`/chapter-5/vue/component/emit/emit-basic.html`中找到这个例子。

### 通过事件发出值

然而，有时仅仅发出一个事件是不够的。在某些情况下，使用带有值的事件更有用。我们可以通过在`$emit`方法中使用第二个参数来实现这一点，如下所示：

```js
$emit(<event>, <value>)
```

然后，当父组件监听事件时，可以以以下格式使用`$event`访问发出的事件的值：

```js
v-on:<event>="<event-handler> = $event"
```

如果事件处理程序是一个方法，那么该值将是该方法的第一个参数，格式如下：

```js
methods: {
  handleCompleted (<value>) { ... }
}
```

因此，现在，我们可以简单地修改前面的应用程序，如下所示：

```js
// Child
clicked () {
  this.$emit('completed', 'Task done')
}

// Parent
methods: {
  handleCompleted (value) {
    alert(value)
  }
}
```

在这里，您可以看到在父组件和子组件之间传递数据是有趣且容易的。但是，如果您的子组件中有一个`<input>`元素，如何将输入字段中的值传递给父组件进行双向数据绑定呢？如果我们了解 Vue 中双向数据绑定的“底层”发生了什么，这并不难。我们将在下一节中学习这个知识点。

您可以在本书的 GitHub 存储库中的`/chapter-5/vue/component/emit/value.html`中找到这个简单的例子，以及在`/chapter-5/vue/component/emit/emit-value-with-props.html`中找到更复杂的例子。

## 使用 v-model 创建自定义输入组件

我们还可以使用组件创建自定义的双向绑定输入，其工作方式与`v-model`指令相同，用于向父组件发出事件。让我们创建一个基本的自定义输入组件：

```js
<custom-input v-model="newTodoText"></custom-input>

Vue.component('custom-input', {
  props: ['value'],
  template: `<input v-on:input="$emit('input', $event.target.value)">`,
})
```

它是如何工作的？要理解这一点，我们需要了解`v-model`在幕后是如何工作的。让我们使用一个简单的`v-model`输入：

```js
<input v-model="handler">
```

前面的`<input>`元素是以下内容的简写：

```js
<input
  v-bind:value="handler"
  v-on:input="handler = $event.target.value"
>
```

因此，在我们的自定义输入中编写`v-model="newTodoText"`是以下内容的简写：

```js
v-bind:value="newTodoText"
v-on:input="newTodoText = $event.target.value"
```

这意味着这个简写下面的组件必须在`props`属性中具有`value`属性，以便让数据从顶部传递下来。它必须发出一个带有`$event.target.value`的`input`事件，以便将数据传递到顶部。

因此，在这个例子中，当用户在`custom-input`子组件中输入时，我们发出值，而父组件通过`v-model="newTodoText"`监听更改，并更新`data`对象中`newTodoText`的值：

```js
<p>{{ newTodoText }}</p>

new Vue({
  el: '#todos',
  data: {
    newTodoText: null
  }
})
```

当你了解 Vue 中双向数据绑定的机制——`v-model`指令时，这就变得很合理了，不是吗？但是，如果你不想使用复选框输入和单选按钮元素的默认值呢？在这种情况下，你会想要将自定义的值发送到父组件中。我们将在下一节中学习如何做到这一点。

你可以在`/chapter-5/vue/component/custom-inputs/basic.html`找到这个简单的例子，在`/chapter-5/vue/component/custom-inputs/props.html`中找到一个更复杂的例子，这两个例子都可以在这本书的 GitHub 存储库中找到。

### 自定义输入组件中的模型定制

默认情况下，自定义输入组件中的模型使用`value`属性作为 prop，`input`作为事件。使用我们之前例子中的`custom-input`组件，可以写成如下形式：

```js
Vue.component('custom-input', {
  props: {
    value: null
  },
  model: {
    prop: 'value', // <-- default
    event: 'input' // <-- default
  }
})
```

在这个例子中，我们不需要指定`prop`和`event`属性，因为它们是该组件模型的默认行为。但是当我们不想对某些输入类型使用这些默认值时，这将变得很有用，比如复选框和单选按钮。

我们可能希望在这些输入中使用`value`属性来实现不同的目的，比如在提交的数据中与复选框的`name`一起发送特定的值，如下所示：

```js
Vue.component('custom-checkbox', {
  model: {
    prop: 'checked',
    event: 'change'
  },
  props: {
    checked: Boolean
  },
  template: `
    <input
      type="checkbox"
      v-bind:checked="checked"
      v-on:change="changed"
      name="subscribe"
      value="newsletter"
    >
  `
  ,
  methods: {
    changed ($event) {
      this.$emit('change', $event.target.checked)
    }
  }
})
```

在这个例子中，我们想要将这两个数据发送到服务器：

```js
name="subscribe"
value="newsletter"
```

我们还可以在使用`JSON.stringify`进行序列化后以 JSON 格式进行：

```js
[{
  "name":"subscribe",
  "value":"newsletter"
}]
```

所以，假设我们在这个组件中没有设置以下自定义模型：

```js
model: {
  prop: 'checked',
  event: 'change'
}
```

在这种情况下，我们只能将以下默认数据发送到服务器：

```js
[{
  "name":"subscribe",
  "value":"on"
}]
```

你可以在这本书的 GitHub 存储库中的`/chapter-5/vue/component/custom-inputs/checkbox.html`中找到这个例子。

当你知道 Vue 组件底层是什么，并且可以通过一点努力进行定制时，这就变得合理了。`/components/`目录中的 Vue 组件与你刚刚学习的组件的工作方式相同。但在深入编写 Nuxt 应用程序的组件之前，你应该了解在使用`v-for`指令时为什么`key`属性很重要。让我们找出来。

## 理解`v-for`循环中的`key`属性

在这本书的许多先前的例子和练习中，你可能注意到了所有`v-for`循环中的`key`属性，如下所示：

```js
<ol>
  <user-item
    v-for="user in users"
    v-bind:user="user"
    v-bind:key="user.id"
  ></user-item>
</ol>
```

也许你会想知道它是什么，它是用来做什么的。`key`属性是每个 DOM 节点的唯一标识，以便 Vue 可以跟踪它们的变化，从而重用和重新排序现有元素。使用`index`作为 key 属性的跟踪是 Vue 默认的行为，因此像这样使用`index`作为 key 属性是多余的：

```js
<div v-for="(user, index) in users" :key="index">
  //...
</div>
```

因此，如果我们希望 Vue 准确地跟踪每个项目的标识，我们必须通过使用`v-bind`指令将每个 key 属性绑定到一个唯一的值，如下所示：

```js
<div v-for="user in users" :key="user.id">
  //...
</div>
```

我们可以使用缩写`:key`来绑定唯一的值，就像前面的例子中所示的那样。还要记住，`key`是一个保留属性。这意味着它不能作为组件 prop 使用：

```js
Vue.component('user-item', {
  props: ['key', 'user']
})
```

在`props`属性中使用`key`将导致浏览器控制台中出现以下错误：

```js
[Vue warn]: "key" is a reserved attribute and cannot be used as 
component prop.
```

当使用`v-for`与组件时，`key`属性是必需的。因此，最好在可能的情况下明确地使用`key`与`v-for`，无论您是否将其与组件一起使用。

为了演示这个问题，让我们创建一个 Vue 应用程序，我们将在其中使用`index`作为我们的`key`，并借助一点 jQuery 的帮助：

1.  在`<head>`块中包含所需的 CDN 链接，以及一些 CSS 样式：

```js
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<script src="http://code.jquery.com/jquery-3.3.1.js"></script>
<style type="text/css">
  .removed {
    text-decoration: line-through;
  }
  .removed button {
    display: none;
  }
</style>
```

1.  在`<body>`块中创建所需的应用程序 HTML 标记：

```js
<div id="todo-list-example">
  <form v-on:submit.prevent="addNewTodo">
    <label for="new-todo">Add a todo</label>
    <input
      v-model="newTodoText"
      id="new-todo"
      placeholder="E.g. Feed the cat"
    >
    <button>Add</button>
  </form>
  <ul>
    <todo-item
      v-for="(todo, index) in todos"
      v-bind:key="index"
      v-bind:title="todo.title"
    ></todo-item>
  </ul>
</div>
```

1.  在`<script>`块中创建所需的组件：

```js
Vue.component('todo-item', {
  template: `<li>{{ title }} <button v-
   on:click="remove($event)">Remove</button></li>`,
  props: ['title'],
  methods: {
    remove: function ($event) {
      $($event.target).parent().addClass('removed')
    }
  }
})
```

1.  创建所需的待办任务列表，如下所示：

```js
new Vue({
  el: '#todo-list-example',
  data: {
    newTodoText: '',
    todos: [
      { id: 1, title: 'Do the dishes' },
      //...
    ],
    nextTodoId: 4
  },
  methods: {
    addNewTodo: function () {
      this.todos.unshift({
        id: this.nextTodoId++,
        title: this.newTodoText
      })
      this.newTodoText = ''
    }
  }
})
```

在这个例子中，我们通过在我们的`todos`数组上发生`unshift`来将一个新的待办任务添加到列表的顶部。我们通过向`li`元素添加`removed`类名来删除一个待办任务。然后，我们使用 CSS 为已删除的待办任务添加删除线，并隐藏删除按钮。

1.  让我们移除`洗碗`。你会看到以下内容：

```js
Do the dishes  (with a strike-through)
```

1.  现在，添加一个名为`喂猫`的新任务。你会看到以下内容：

```js
Feed the cat (with a strike-through)
```

这是因为`喂猫`现在已经占据了`洗碗`的索引，即 0。Vue 只是重用元素而不是渲染新元素。换句话说，无论对项目进行了何种更改，Vue 都将根据数组中的索引更新 DOM 元素。这意味着我们得到了一个意外的结果。

你可以在这本书的 GitHub 存储库中的`/chapter-5/vue/component/key/using-index.html`中找到这个例子。在浏览器上运行它，看看问题出在哪里。然后，将其与在`/chapter-5/vue/component/key/using-id.html`中使用`id`作为键的情况进行比较。你会发现你得到了正确的行为。

使用索引作为 key 的问题也可以通过以下伪代码来解释，其中正在生成一组数字，并为每个数字设置索引作为 key：

```js
let numbers = [1,2,3]

<div v-for="(number, index) in numbers" :key="index">
  // Which turns into number - index
  1 - 0
  2 - 1
  3 - 2
</div>
```

这看起来很棒，乍一看工作正常。但是如果你添加数字 4，索引信息就变得无用了。这是因为现在每个数字都得到了一个新的索引：

```js
<div v-for="(number, index) in numbers" :key="index">
  4 - 0
  1 - 1
  2 - 2
  3 - 3
</div>
```

如你所见，1、2 和 3 失去了它们的状态，必须重新渲染。这就是为什么对于这种情况，使用唯一的 key 是必需的。对于每个项目来说，保持其索引号并且不在每次更改时重新分配是很重要的：

```js
<user-item
  v-for="(user, index) in users"
  v-bind:key="user.id"
  v-bind:name="user.name"
></user-item>
```

作为一个经验法则，每当你以一种导致索引变化的方式操纵列表时，使用 key，这样 Vue 可以在之后正确地更新 DOM。这些操纵包括以下内容：

+   在数组中的任何位置添加一个项目

+   从数组中删除一个项目，从除数组末尾以外的任何位置

+   以任何方式重新排序数组

如果你的列表在组件的生命周期内从未改变过，或者你只是使用 push 函数而不是`unshift`函数添加项目，就像前面的例子一样，那么使用索引作为 key 是可以的。但是如果你试图追踪你何时需要使用索引和何时不需要使用索引，最终你会遇到“bug”，因为你可能会误解 Vue 的行为。

如果你不确定是否要使用索引作为 key，那么最好在`v-for`循环中使用带有不可变 ID 的`key`属性。使用具有唯一值的`key`属性不仅对`v-for`指令很重要，而且对 HTML 表单中的`<input>`元素也很重要。我们将在下一节中讨论这个问题。

## 使用 key 属性控制可重用的元素

为了提供更好的性能，我们发现 Vue 总是重用 DOM 节点而不是重新渲染，这可能会产生一些不良结果，正如前一节所示。这里有另一个没有使用`v-for`的示例，以演示为什么拥有 key 属性是相当重要的：

```js
<div id="app">
  <template v-if="type === 'fruits'">
    <label>Fruits</label>
    <input />
  </template>
  <template v-else>
    <label>Vegetables</label>
    <input />
  </template>
  <button v-on:click="toggleType">Toggle Type</button>
</div>

<script type="text/javascript">
  new Vue({
    el: '#app',
    data: { type: 'fruits' },
    methods: {
      toggleType: function () {
        return this.type = this.type === 'fruits' ? 'vegetables' : 'fruits'
      }
    }
  })
</script>
```

在这个例子中，如果你在输入水果的名字并切换类型，你仍然会在`vegetables`输入框中看到你刚刚输入的名字。这是因为 Vue 试图尽可能地重用相同的`<input>`元素以获得最快的结果。但这并不总是理想的。你可以通过为每个`<input>`元素添加`key`属性以及一个唯一值来告诉 Vue 不要重用相同的`<input>`元素，如下所示：

```js
<template v-if="type === 'fruits'">
  <label>Fruits</label>
  <input key="fruits-input"/>
</template>
<template v-else>
  <label>Vegetables</label>
  <input key="vegetables-input"/>
</template>
```

因此，如果您刷新页面并再次测试，输入字段现在应该按预期工作，而不会在切换它们时“重用”彼此。这不适用于`<label>`元素，因为它们没有`key`属性。但是，从视觉上来看，这不是问题。

您可以在本书的 GitHub 存储库的`/chapter-5/vue/component/key/`目录中的`toggle-with-key.html`和`toggle-without-key.html`文件中找到此示例代码。

这就是您需要了解的关于 Vue 组件基本性质的全部内容。因此，到目前为止，您应该已经掌握了足够的基本知识，可以开始使用单文件组件创建 Vue 组件的下一个级别。让我们开始吧！

如果您想了解更多关于 Vue 组件以及 Vue 组件更深入的部分，例如插槽，请访问[`vuejs.org/v2/guide/components.html`](https://vuejs.org/v2/guide/components.html)。

# 创建单文件 Vue 组件

我们一直在使用单个 HTML 页面编写 Vue 应用程序，以便快速获得我们想要看到的结果。但是在 Vue 或 Nuxt 的实际开发项目中，我们不希望编写这样的东西：

```js
const Foo = { template: '<div>foo</div>' }
const Bar = { template: '<div>bar</div>' }
```

在前面的代码中，我们在一个地方（例如在单个 HTML 页面中）使用 JavaScript 对象创建了两个 Vue 组件，但最好将它们分开，并在单独的`.js`文件中创建每个组件，如下所示：

```js
// components/foo.js
Vue.component('page-foo', {
  data: function () {
    return { message: 'foo' }
  },
  template: '<div>{{ count }}</div>'
})
```

这对于简单的组件可以很好地工作，其中 HTML 布局很简单。但是，在涉及更复杂的 HTML 标记的更复杂布局中，我们希望避免在 JavaScript 文件中编写 HTML。这个问题可以通过具有`.vue`扩展名的单文件组件来解决，如下所示：

```js
// index.vue
<template>
  <p>{{ message }}</p>
</template>

<script>
export default {
  data () {
    return { message: 'Hello World!' }
  }
}
</script>

<style scoped>
p {
  font-size: 2em;
  text-align: center;
}
</style>
```

然而，我们不能只是在浏览器上运行该文件，而不使用构建工具（如 webpack 或 rollup）进行编译。在本书中，我们使用 webpack。这意味着，从现在开始，我们将不再使用 CDN 或单个 HTML 页面来创建复杂的 Vue 应用程序。相反，我们将使用`.vue`和`.js`文件，只有一个`.html`文件来创建我们的 Vue 应用程序。我们将在接下来的部分指导您如何使用 webpack 来帮助我们做到这一点。让我们开始吧。

## 使用 webpack 编译单文件组件

要编译`.vue`组件，我们需要将`vue-loader`和`vue-template-compiler`安装到 webpack 构建过程中。但在此之前，我们必须在项目目录中创建一个`package.json`文件，列出我们项目依赖的 Node.js 包。您可以在[`docs.npmjs.com/creating-a-package-json-file`](https://docs.npmjs.com/creating-a-package-json-file)上查看`package.json`字段的详细信息。最基本和必需的是`name`和`version`字段。让我们开始吧：

1.  在项目目录中创建一个`package.json`文件，其中包含以下必填字段和值：

```js
// package.json
{
  "name": "vue-single-file-component",
  "version": "1.0.0"
}
```

1.  打开一个终端，将目录更改为您的项目，并安装`vue-loader`和`vue-template-compiler`：

```js
$ npm i vue-loader --save-dev 
$ npm i vue-template-compiler --save-dev
```

您应该在终端上看到一个警告，因为您在此处安装的 Node.js 包需要其他 Node.js 包，其中最显着的是 webpack 包。在本书中，我们在本书的 GitHub 存储库中的`/chapter-5/vue/component-webpack/basic/`中设置了一个基本的 webpack 构建过程。我们将在大多数即将推出的 Vue 应用程序中使用此设置。我们已将 webpack 配置文件分成了三个较小的配置文件：

+   `webpack.common.js`包含在开发和生产过程中共享的常见 webpack 插件和配置。

+   `webpack.dev.js`仅包含开发过程的插件和配置。

+   `webpack.prod.js`仅包含生产过程的插件和配置。

以下代码显示了我们如何在`script`命令中使用这些文件：

```js
// package.json
"scripts": {
  "start": "webpack-dev-server --open --config webpack.dev.js",
  "watch": "webpack --watch",
  "build": "webpack --config webpack.prod.js"
}
```

请注意，在本书中，我们假设您已经知道如何使用 webpack 来编译 JavaScript 模块。如果您对 webpack 还不熟悉，请访问[`webpack.js.org/`](https://webpack.js.org/)获取更多信息。

1.  因此，在安装了`vue-loader`和`vue-template-compiler`之后，我们需要在`webpack.common.js`（或`webpack.config.js`，如果您使用单个配置文件）中配置`module.rules`，如下所示：

```js
// webpack.common.js
const VueLoaderPlugin = require('vue-loader/lib/plugin')

module.exports = {
  mode: 'development',
  module: {
    rules: [
      {
        test: /\.vue$/,
        loader: 'vue-loader'
      },
      {
        test: /\.js$/,
        loader: 'babel-loader'
      },
      {
        test: /\.css$/,
        use: [
          'vue-style-loader',
          'css-loader'
        ]
      }
    ]
  },
  plugins: [
    new VueLoaderPlugin()
  ]
}
```

1.  然后，我们可以使用在`package.json`中设置的以下命令来查看我们的应用程序运行情况：

+   `$ npm run start`用于在`localhost:8080`进行实时重新加载和开发

+   `$ npm run watch`用于在`/path/to/your/project/dist/`进行开发

+   `$ npm run build`用于在`/path/to/your/project/dist/`编译我们的代码

就是这样。现在你有了一个基本的构建流程来开发 Vue 应用程序与 webpack。所以，从现在开始，在更复杂的应用程序中，我们将编写单文件组件，并使用这种方法来编译它们。我们将在下一节中创建一个简单的 Vue 应用程序。

## 在单文件组件中传递数据和监听事件

到目前为止，我们一直在使用单个 HTML 页面进行我们的“todo”演示。这一次，我们将使用单文件组件来创建一个简单的“todo”购物清单。让我们开始：

1.  在`<div>`元素中创建一个带有`"todos"`ID 的`index.html`文件，以便 Vue 运行 Vue 实例：

```js
// index.html
<!doctype html>
<html>
  <head>
    <title>Todo Grocery Application (Single File 
     Components)</title>
  </head>
  <body>
    <div id="todos"></div>
  </body>
</html>
```

1.  在项目根目录中创建一个`/src/`目录，并在其中创建一个`entry.js`文件作为文件入口点，以指示 webpack 应该使用哪些模块来开始构建我们的应用程序内部依赖图。webpack 还将使用此文件来找出入口点依赖的其他模块和库（直接和间接）。

```js
// src/entry.js
'use strict'

import Vue from 'vue/dist/vue.js'
import App from './app.vue'

new Vue({
  el: 'todos',
  template: '<App/>',
  components: {
    App
  }
})
```

1.  在`<script>`块中创建一个提供虚拟数据的父组件，其中包含项目列表：

```js
// src/app.vue
<template>
  <div>
    <ol>
      <TodoItem
        v-for="thing in groceryList"
        v-bind:item="thing"
        v-bind:key="item.id"
        v-on:add-item="addItem"
        v-on:delete-item="deleteItem"
      ></TodoItem>
    </ol>
    <p><span v-html="&pound;"></span>{{ total }}</p>
  </div>
</template>

<script>
import TodoItem from './todo-item.vue'
export default {
  data () {
    return {
      cart: [],
      total: 0,
      groceryList: [
        { id: 0, text: 'Lentils', price: 2 },
        //...
      ]
    }
  },
  components: {
    TodoItem
  }
}
</script>
```

在上面的代码中，我们简单地将子组件作为`TodoItem`导入，并使用`v-for`从`groceryList`中生成它们的列表。

1.  在`methods`对象中添加以下方法以添加和删除项目。然后，在`computed`对象中添加一个方法，计算购物车中项目的总成本：

```js
// src/app.vue
methods: {
  addItem (item) {
    this.cart.push(item)
    this.total = this.shoppingCartTotal
  },
  deleteItem (item) {
    this.cart.splice(this.cart.findIndex(e => e === item), 1)
    this.total = this.shoppingCartTotal
  }
},
computed: {
  shoppingCartTotal () {
    let prices = this.cart.map(item => item.price)
    let sum = prices.reduce((accumulator, currentValue) =>
     accumulator + currentValue, 0)
    return sum
  }
}
```

1.  创建一个子组件，通过`props`显示从父级传递下来的项目：

```js
// src/todo-item.vue
<template>
  <li>
    <input type="checkbox" :name="item.id" v-model="checked"> {{
     item.text }}
    <span v-html="&pound;"></span>{{ item.price }}
  </li>
</template>

<script>
export default {
  props: ['item'],
  data () {
    return { checked: false }
  },
  methods: {
    addToCart (item) {
      this.$emit('add-item', item)
    }
  },
  watch: {
    checked (boolean) {
      if (boolean === false) {
        return this.$emit('delete-item', this.item)
      }
      this.$emit('add-item', this.item)
    }
  }
}
</script>
```

在这个组件中，我们还有一个`checkbox`按钮。这用于发出`delete-item`或`add-item`事件，并将项目数据传递给父级。现在，如果你用`$ npm run start`运行应用程序，你应该看到它在`localhost:8080`加载。

干得好！你已经成功构建了一个使用 webpack 的 Vue 应用程序，这就是 Nuxt 在幕后使用的编译和构建你的 Nuxt 应用程序。了解已建立系统下方运行的内容总是有用的。当你知道如何使用 webpack 时，你可以使用刚学到的 webpack 构建设置来进行各种 JavaScript 和 CSS 相关的项目。

你可以在本书的 GitHub 存储库中的`/chapter-5/vue/component-webpack/todo/`中找到这个示例。

在下一节中，我们将把前面几节学到的内容应用到`/chapter-5/nuxt-universal/local-components/sample-website/`中的`sample website`示例网站中，这个示例可以在本书的 GitHub 存储库中找到。

## 在 Nuxt 中添加 Vue 组件

在示例网站中，我们只有两个`.vue`文件可以使用 Vue 组件进行改进：`/layouts/default.vue`和`/pages/work/index.vue`。首先，我们应该改进`/layouts/default.vue`。在这个文件中，我们只需要改进三件事：导航、社交媒体链接和版权。

### 重构**导航**和**社交链接**

我们将从重构导航和社交媒体链接开始：

1.  在`/components/`目录中创建一个导航组件，如下所示：

```js
// components/nav.vue
<template>
  <li>
    <nuxt-link :to="item.link" v-html="item.name">
    </nuxt-link>
  </li>
</template>

<script>
export default {
  props: ['item']
}
</script>
```

1.  在`/components/`目录中也创建一个社交链接组件，如下所示：

```js
// components/social.vue
<template>
  <li>
    <a :href="item.link" target="_blank">
      <i :class="item.classes"></i>
    </a>
  </li>
</template>

<script>
export default {
  props: ['item']
}
</script>
```

1.  将它们导入到布局的`<script>`块中，如下所示：

```js
// layouts/default.vue
import Nav from '~/components/nav.vue'
import Social from '~/components/social.vue'

components: {
  Nav,
  Social
}
```

请注意，如果您在 Nuxt 配置文件中将`components`选项设置为`true`，则可以跳过此步骤。

1.  从`<template>`块中删除现有的导航和社交链接块：

```js
// layouts/default.vue
<template v-for="item in nav">
  <li><nuxt-link :to="item.link" v-html="item.name">
  </nuxt-link></li>
</template>

<template v-for="item in social">
  <li>
    <a :href="item.link" target="_blank">
      <i :class="item.classes"></i>
    </a>
  </li>
</template>
```

1.  用导入的`Nav`和`Social`组件替换它们，如下所示：

```js
// layouts/default.vue
<Nav
  v-for="item in nav"
  v-bind:item="item"
  v-bind:key="item.slug"
 ></Nav>

<Social
  v-for="item in social"
  v-bind:item="item"
  v-bind:key="item.name"
 ></Social>
```

有了这些，你就完成了！

### 重构**版权组件**

现在，我们将重构已经存在于`/components/`目录中的版权组件。让我们开始吧：

1.  从`/components/base-copyright.vue`文件的`<script>`块中删除`data`函数：

```js
// components/copyright.vue
export default {
  data () {
    return { copyright: '&copy; Lau Tiam Kok' }
  }
}
```

1.  用`props`属性替换前面的`data`函数，如下所示：

```js
// components/copyright.vue
export default {
  props: ['copyright']
}
```

1.  将版权数据添加到`<script>`块中，而不是`/layouts/default.vue`中：

```js
// layouts/default.vue
data () {
  return {
    copyright: '&copy; Lau Tiam Kok',
  }
}
```

1.  从`<template>`块中删除现有的`<Copyright />`组件：

```js
// layouts/default.vue
<Copyright />
```

1.  添加一个新的`<Copyright />`组件，并将版权数据绑定到它：

```js
// layouts/default.vue
<Copyright v-bind:copyright="copyright" />
```

到此为止，您应该已经成功将数据从默认页面（父级）传递给组件（子级），在默认页面中保留了您的数据。干得好！这就是`/layouts/default.vue`的全部内容。我们还可以改进工作页面，我们已经为您在`/chapter-5/nuxt-universal/local-components/sample-website/`中完成了这项工作，这可以在本书的 GitHub 存储库中找到。如果您在本地安装此示例网站并在本地机器上运行它，您将看到我们已经很好地应用了我们的组件。通过这个例子，您可以看到在理解了 Vue 组件系统的工作原理后，将布局中的元素抽象化为组件是多么容易。但是，如何将数据传递给父组件呢？为此，我们创建了一个示例应用程序，其中子组件向父组件发出事件，位于`/chapter-5/nuxt-universal/local-components/emit-events/`，这可以在本书的 GitHub 存储库中找到。我们还向应用程序添加了自定义输入和复选框组件，请查看一下。以下是一个示例片段：

```js
// components/input-checkbox.vue
<template>
  <input
    type="checkbox"
    v-bind:checked="checked"
    v-on:change="changed"
    name="subscribe"
    value="newsletter"
  >
</template>

<script>
export default {
  model: {
    prop: 'checked',
    event: 'change'
  },
  props: { checked: Boolean },
  methods: {
    changed ($event) {
      this.$emit('change', $event.target.checked)
    }
  }
}
</script>
```

在这里，您可以看到我们在 Nuxt 应用程序中使用的组件代码与我们在 Vue 应用程序中编写的代码相同。这些类型的组件是嵌套组件。`props`属性和`$emit`方法用于在父组件和子组件之间传递数据。这些嵌套组件也是本地的，因为它们只在导入它们的组件（父级）的范围内可用。因此，从另一个角度来看，Vue 组件可以被归类为本地组件和全局组件。自从*什么是组件？*部分以来，您一直在学习全局组件。但是，您只学会了如何在 Vue 应用程序中使用它们。在接下来的部分中，我们将看看如何为 Nuxt 应用程序注册全局组件。但在跳入其中之前，让我们从整体的角度重新审视 Vue 组件：全局组件和本地组件。

# 注册全局和本地组件

我们已经创建了许多组件，无论是使用`Vue.component()`、纯 JavaScript 对象还是单文件组件引擎。我们创建的一些组件是全局组件，而另一些是本地组件。例如，在上一节中刚刚创建的`/components/`目录中的所有重构组件都是本地组件，而在*什么是组件？*部分中创建的组件都是全局组件。无论它们是本地组件还是全局组件，如果您想使用它们，都必须进行注册。其中一些在创建时注册，而另一些则需要手动注册。在接下来的部分中，您将学习如何全局和本地注册它们。您还将了解两种类型的注册将如何影响您的应用程序。我们将学习如何注册 Vue 组件，而不是传递它们。

## 在 Vue 中注册全局组件

全局组件，正如它们的名称所示，可以在整个应用程序中全局使用。当您使用`Vue.component()`创建它们时，它们会被全局注册：

```js
Vue.component('my-component-name', { ... })
```

全局组件必须在根 Vue 实例实例化之前注册。注册后，它们可以在根 Vue 实例的模板中使用，如下所示：

```js
Vue.component('component-x', { ... })
Vue.component('component-y', { ... })
Vue.component('component-z', { ... })

new Vue({ el: '#app' })

<div id="app">
  <component-x></component-x>
  <component-y></component-y>
  <component-z></component-z>
</div>
```

在这里，您可以看到注册全局组件非常容易 - 在创建它们时，您甚至可能意识不到注册过程。我们将很快在*Nuxt 中注册全局组件*部分中研究这种类型的注册。但现在，我们将学习如何注册本地组件。

## 在 Vue/Nuxt 中注册本地组件

在本章中，我们已经看到并使用了 Vue 和 Nuxt 应用中的本地组件。这些组件是通过使用纯 JavaScript 对象创建的，如下所示：

```js
var ComponentX = { ... }
var ComponentY = { ... }
var ComponentZ = { ... }
```

然后，它们可以通过`components`选项进行注册，如下所示：

```js
new Vue({
  el: '#app',
  components: {
    'component-x': ComponentX,
    'component-y': ComponentY,
    'component-z': ComponentZ
  }
})
```

还记得我们在本书的 GitHub 存储库中的`/chapter-5/vue/component/basic.html`文件中创建的 Vue 应用吗？该应用中的`user-item`组件是一个全局组件。现在，让我们对其进行重构并将其变成一个本地组件：

1.  移除以下全局组件：

```js
Vue.component('user-item', {
  props: ['user'],
  template: '<li>{{ user.name }}</li>'
})
```

1.  使用以下方式替换为本地组件：

```js
const UserItem = {
  props: ['user'],
  template: '<li>{{ user.name }}</li>'
}
```

1.  使用`components`选项注册本地组件：

```js
new Vue({
  el: '#app',
  data: {
    users: [
      { id: 0, name: 'John Doe' },
      //...
    ]
  },
  components: {
    'user-item': UserItem
  }
})
```

该应用程序将与以前的方式相同工作。唯一的区别是`user-item`不再全局可用。这意味着它在任何其他子组件中都不可用。例如，如果您想要在`ComponentZ`中使`ComponentX`可用，那么您必须手动"附加"它：

```js
var ComponentX = { ... }

var ComponentZ = {
  components: {
    'component-x': ComponentX
  }
}
```

如果您正在使用 babel 和 webpack 编写 ES2015 模块，您可以将`ComponentX`作为单文件组件，然后导入它，如下所示：

```js
// components/ComponentZ.vue
import Componentx from './Componentx.vue'

export default {
  components: {
    'component-x': ComponentX
  }
}

<component-x
  v-for="item in items"
  ...
></component-x>
```

您还可以从`components`选项中省略`component-x`，并直接在其中使用`ComponentX`变量，如下所示：

```js
// components/ComponentZ.vue
export default {
  components: {
    ComponentX
  }
}
```

在 ES2015+中使用诸如`ComponentX`之类的变量作为 JavaScript 对象的简写形式为`ComponentX: ComponentX`。由于`component-x`从未注册过，所以您需要在模板中使用`<ComponentX>`而不是`<component-x>`。

```js
<ComponentX
  v-for="item in items"
  ...
></ComponentX>
```

在前面的单文件组件中编写 ES2015 与我们在 Nuxt 中编写`.vue`文件的方式相同。因此，到目前为止，您应该已经意识到我们一直在 Nuxt 应用程序中编写本地组件，例如`/components/copyright.vue`和`/components/nav.vue`。但是在 Nuxt 应用程序中如何编写全局组件呢？这就是`/plugins/`目录发挥作用的地方。在下一节中，您将学习如何在 Nuxt 中进行此操作。

您可以在本书的 GitHub 存储库中的`/chapter-5/vue/component/registering-local-components.html`中找到前面的应用程序。

## 在 Nuxt 中注册全局组件

我们在第二章中学习了目录结构，*开始使用 Nuxt*，`/plugins/`目录是我们可以创建 JavaScript 文件并在实例化根 Vue 应用程序之前运行的最佳位置。因此，这是注册我们的全局组件的最佳位置。

让我们创建我们的第一个全局组件：

1.  在`/plugins/`目录中创建一个简单的 Vue 组件，如下所示：

```js
// components/global/sample-1.vue
<template>
  <p>{{ message }}</p>
</template>

<script>
export default {
  data () {
    return {
      message: 'A message from sample global component 1.'
    }
  }
}
</script>
```

1.  在`/plugins/`目录中创建一个`.js`文件，并导入前面的组件，如下所示：

```js
// plugins/global-components.js
import Vue from 'vue'
import Sample from '~/components/global/sample-1.vue'

Vue.component('sample-1', Sample)
```

1.  我们还可以直接在`/plugins/global-components.js`中创建第二个全局组件，如下所示：

```js
Vue.component('sample-2', {
  render (createElement) {
    return createElement('p', 'A message from sample global
     component 2.')
  }
})
```

1.  告诉 Nuxt 在 Nuxt 配置文件中在实例化根应用程序之前先运行它们，如下所示：

```js
// nuxt.config.js
plugins: [
  '~/plugins/global-components.js',
]
```

请注意，此组件将在 Nuxt 应用程序的客户端和服务器端都可用。如果您只想在特定端上运行此组件，例如仅在客户端上运行，则可以注册它，如下所示：

```js
// nuxt.config.js
plugins: [
  { src: '~/plugins/global-components.js',  mode: 'client' }
]
```

现在，这个组件只能在客户端使用。但是，如果你只想在服务器端运行它，只需在前面的`mode`选项中使用`server`。

1.  我们可以在任何地方使用这些全局组件，而无需手动再次导入它们，如下面的代码所示：

```js
// pages/about.vue
<sample-1 />
<sample-2 />
```

1.  在浏览器上运行应用程序。你应该得到以下输出：

```js
<p>A message from sample global component 1.</p>
<p>A message from sample global component 2.</p>
```

就是这样！这就是你可以通过涉及各种文件在 Nuxt 中注册全局组件的方法。全局注册的底线是使用`Vue.component`，就像我们在 Vue 应用程序中所做的那样。然而，全局注册通常不是理想的，就像它的“表兄弟”全局混入一样，我们将在下一节中介绍。例如，全局注册组件但在大多数情况下不需要它们对于服务器和客户端来说都是不必要的。现在，让我们继续看看混入是什么，以及如何编写它们。

你可以在本书的 GitHub 存储库中的`/chapter-5/nuxt-universal/global-components/`中找到这个例子。

# 编写基本和全局混入

混入只是一个 JavaScript 对象，可以用来包含任何组件选项，比如`created`，`methods`，`mounted`等等。它们可以用来使这些选项可重用。我们可以通过将它们导入到组件中，并将它们与该组件中的其他选项“混合”来实现这一点。

在某些情况下，使用混入可能是有用的，比如在第二章中，*开始使用 Nuxt*。我们知道，当 Vue Loader 编译单文件组件中的`<template>`块时，它会将遇到的任何资源 URL 转换为 webpack 模块请求，如下所示：

```js
<img src="~/assets/sample-1.jpg">
```

前面的图像将被转换为以下 JavaScript 代码：

```js
createElement('img', {
  attrs: {
    src: require('~/assets/sample-1.jpg') // this is now a module request
  }
})
```

如果您手动插入图像，这并不难。但在大多数情况下，我们希望动态插入图像，如下所示：

```js
// pages/about.vue
<template>
  <img :src="'~/assets/images' + post.image.src" :alt="post.image.alt">
</template>

const post = {
  title: 'About',
  image: {
    src: '/about.jpg',
    alt: 'Sample alt 1'
  }
}

export default {
  data () {
    return { post }
  }
}
```

在这个例子中，当您在控制台上使用`:src`指令时，图像将会得到 404 错误，因为 Vue Loader 在构建过程中从未编译它。为了解决这个问题，我们需要手动将模块请求插入到`:src`指令中。

```js
<img :src="require('~/assets/images/about.jpg')" :alt="post.image.alt">
```

然而，这也不好，因为更倾向于动态图像解决方案。因此，这里的解决方案如下：

```js
<img :src="loadAssetImage(post.image.src)" :alt="post.image.alt">
```

在这个解决方案中，我们编写了一个可重用的`loadAssetImage`函数，以便在任何需要的 Vue 组件中调用它。因此，在这种情况下，我们需要混合。有几种使用混合的方法。我们将在接下来的几节中看一些常见的用法。

## 创建基本混合/非全局混合

在非单文件组件 Vue 应用程序中，我们可以这样定义一个混合对象：

```js
var myMixin = {
  created () {
    this.hello()
  },
  methods: {
    hello () { console.log('hello from mixin!') }
  }
}
```

然后，我们可以使用`Vue.extend()`将其“附加”到一个组件中：

```js
const Foo = Vue.extend({
  mixins: [myMixin],
  template: '<div>foo</div>'
})
```

在这个例子中，我们只将这个混合附加到`Foo`，所以当调用这个组件时，你只会看到`console.log`消息。

你可以在这本书的 GitHub 存储库的`/chapter-5/vue/mixins/basic.html`中找到这个例子。

对于 Nuxt 应用程序，我们在`/plugins/`目录中创建并保存混合对象，保存在`.js`文件中。让我们来演示一下：

1.  在`/plugins/`目录中创建一个`mixin-basic.js`文件，其中包含一个在浏览器控制台上打印消息的函数，当 Vue 实例被创建时：

```js
// plugins/mixin-basic.js
export default {
  created () {
    this.hello()
  },
  methods: {
    hello () {
      console.log('hello from mixin!')
    }
  }
}
```

1.  在需要的地方随时导入它，如下所示：

```js
// pages/about.vue
import Mixin from '~/plugins/mixin-basic.js'

export default {
  mixins: [Mixin]
}
```

在这个例子中，只有当你在`/about`路由上时，你才会得到`console.log`消息。这就是我们创建和使用非全局混合的方法。但在某些情况下，我们需要全局混合适用于应用程序中的所有组件。让我们看看我们如何做到这一点。

你可以在这本书的 GitHub 存储库的`/chapter-5/nuxt-universal/mixins/basic/`中找到这个例子。

## 创建全局混合

我们可以通过使用`Vue.mixin()`来创建和应用全局混合：

```js
Vue.mixin({
  mounted () {
    console.log('hello from mixin!')
  }
})
```

全局混合必须在实例化 Vue 实例之前定义：

```js
const app = new Vue({
  //...
}).$mount('#app')
```

现在，你创建的每个组件都将受到影响并显示该消息。你可以在这本书的 GitHub 存储库的`/chapter-5/vue/mixins/global.html`中找到这个例子。如果你在浏览器上运行它，你会看到`console.log`消息出现在每个路由上，因为它在所有路由组件中传播。通过这种方式，我们可以看到如果被滥用可能造成的潜在危害。在 Nuxt 中，我们以相同的方式创建全局混合；也就是使用`Vue.mixin()`。让我们来看一下：

1.  在`/plugins/`目录中创建一个`mixin-utils.js`文件，以及用于从`/assets/`目录加载图像的函数：

```js
// plugins/mixin-utils.js
import Vue from 'vue'

Vue.mixin({
  methods: {
    loadAssetImage (src) {
      return require('~/assets/images' + src)
    }
  }
})
```

1.  在 Nuxt 配置文件中包含前面的全局混合路径：

```js
// nuxt.config.js
module.exports = {
  plugins: [
    '~/plugins/mixin-utils.js'
  ]
}
```

1.  现在，你可以在你的组件中随意使用`loadAssetImage`函数，如下所示：

```js
// pages/about.vue
<img :src="loadAssetImage(post.image.src)" :alt="post.image.alt">
```

请注意，我们不需要像导入基本混入那样导入全局混入，因为我们已经通过`nuxt.config.js`全局注入了它们。但同样，要谨慎而谨慎地使用它们。

您可以在本书的 GitHub 存储库中的`/chapter-5/nuxt-universal/mixins/global/`中找到这个混入的示例。

混入非常有用。全局混入如全局 Vue 组件在数量过多时很难管理，因此会使您的应用难以预测和调试。因此，明智而谨慎地使用它们。我们希望您现在知道 Vue 组件是如何工作的以及如何编写它们。然而，仅仅知道它们是如何工作和如何编写它们是不够的 - 我们应该了解编写可读性和未来可管理性时需要遵守的标准规则。因此，在结束本章之前，我们将看一些这些规则。

# 定义组件名称和使用命名约定

在本章和前几章中，我们已经看到并创建了许多组件。我们创建的组件越多，我们就越需要遵循组件的命名约定。否则，我们将不可避免地会遇到混淆和错误，以及反模式。我们的组件将不可避免地会相互冲突 - 甚至与 HTML 元素相冲突。幸运的是，有一个官方的 Vue 风格指南，我们可以遵循以提高我们应用的可读性。在本节中，我们将介绍一些特定于本书的规则。

## 多词组件名称

我们现有和未来的 HTML 元素都是单词（例如`article`，`main`，`body`等），因此为了防止冲突发生，我们在命名组件时应该使用多个单词（除了根应用组件）。例如，以下做法被认为是不好的：

```js
// .js
Vue.component('post', { ... })

// .vue
export default {
  name: 'post'
}
```

组件的名称应该按照以下方式书写：

```js
// .js
Vue.component('post-item', { ... })

// .vue
export default {
  name: 'PostItem'
}
```

## 组件数据

我们应该始终使用`data`函数而不是`data`属性来处理组件的数据，除了在根 Vue 实例中。例如，以下做法被认为是不好的：

```js
// .js
Vue.component('foo-component', {
  data: { ... }
})

// .vue
export default {
  data: { ... }
}
```

上述组件中的数据应该按照以下方式书写：

```js
// .js
Vue.component('foo-component', {
  data () {
    return { ... }
  }
})

// .vue
export default {
  data () {
    return { ... }
  }
}

// .js or .vue
new Vue({
  data: { ... }
})
```

但是为什么呢？这是因为当 Vue 初始化数据时，它会从`vm.$options.data`创建一个对`data`的引用。因此，如果数据是一个对象，并且一个组件有多个实例，它们都将使用相同的`data`。更改一个实例中的数据将影响其他实例。这不是我们想要的。因此，如果`data`是一个函数，Vue 将使用`getData`方法返回一个只属于当前初始化实例的新对象。因此，根实例中的数据在所有其他组件实例中共享，这些实例包含它们自己的数据。你可以通过`this.$root.$data`从任何组件实例中访问根数据。你可以在本书的 GitHub 存储库中的`/chapter-5/vue/component-webpack/data/`和`/chapter-5/vue/data/basic.html`中查看一些示例。

你可以在[`github.com/vuejs/vue/blob/dev/src/core/instance/state.js#L112`](https://github.com/vuejs/vue/blob/dev/src/core/instance/state.js#L112)上查看 Vue 源代码，了解数据是如何初始化的。

## 属性定义

我们应该在`props`属性中定义属性，以便尽可能详细地指定它们的类型（至少）。只有在原型设计时才可以不进行详细定义。例如，以下做法被认为是不好的：

```js
props: ['message']
```

这应该这样写：

```js
props: {
  message: String
}
```

或者，更好的做法是这样写：

```js
props: {
  message: {
    type: String,
    required: false,
    validator (value) { ... }
  }
}
```

## 组件文件

我们应该始终遵守“一个文件一个组件”的政策；也就是说，一个文件中只写一个组件。这意味着你不应该在一个文件中有多个组件。例如，以下做法被认为是不好的：

```js
// .js
Vue.component('PostList', { ... })

Vue.component('PostItem', { ... })
```

它们应该拆分成多个文件，如下所示：

```js
components/
|- PostList.js
|- PostItem.js
```

如果你在`.vue`中编写组件，应该这样做：

```js
components/
|- PostList.vue
|- PostItem.vue
```

## 单文件组件文件名大小写

我们应该只为单文件组件的文件名使用 PascalCase 或 kebab-case。例如，以下做法被认为是不好的：

```js
components/
|- postitem.vue

components/
|- postItem.vue
```

它们应该这样写：

```js
// PascalCase
components/
|- PostItem.vue

// kebab-case
components/
|- post-item.vue
```

## 自闭合组件

当我们的单文件组件中没有内容时，应该使用自闭合格式，除非它们在 DOM 模板中使用。例如，以下做法被认为是不好的：

```js
// .vue
<PostItem></PostItem>

// .html
<post-item/>
```

它们应该这样写：

```js
// .vue
<PostItem/>

// .html
<post-item></post-item>
```

这些只是一些基本的规则。还有更多规则，比如编写多属性元素的规则，指令简写，带引号的属性值等等。但是我们在这里突出显示的选定规则应该足够让你完成本书。你可以在[`vuejs.org/v2/style-guide/`](https://vuejs.org/v2/style-guide/)找到其他规则和完整的样式指南。

# 摘要

干得好！在本章中，你学会了全局和局部 Vue 组件之间的区别，如何在 Nuxt 应用程序中注册全局组件，以及如何创建局部和全局 mixin。你还学会了如何通过`props`属性将数据传递给子组件，如何使用`$emit`方法从子组件向父组件发出数据，以及如何创建自定义输入组件。然后，你学会了为组件使用`key`属性的重要性。之后，你学会了如何使用 webpack 编写单文件组件。最后但同样重要的是，你了解了在 Nuxt 和 Vue 应用程序开发中应该遵循的一些规则。

在下一章中，我们将进一步探讨`/plugins/`目录的使用，通过编写 Vue 中的自定义插件并导入它们来扩展 Nuxt 应用程序。我们还将研究如何从 Vue 社区导入外部 Vue 插件，通过将它们注入到 Nuxt 的`$root`和`context`组件中创建全局函数，编写基本/异步模块和模块片段，并使用 Nuxt 社区的外部 Nuxt 模块。我们将会对这些进行详细的指导，敬请关注！
