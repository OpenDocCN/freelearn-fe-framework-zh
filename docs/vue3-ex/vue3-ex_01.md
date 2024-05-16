# 第一章：在 Vue 3 中创建您的第一个应用程序

**Vue 3**是流行的 Vue.js 框架的最新版本。它专注于改善开发人员的体验和速度。它是一个基于组件的框架，让我们可以轻松创建模块化、可测试的应用程序。它包括其他框架常见的概念，如 props、过渡、事件处理、模板、指令、数据绑定等。本章的主要目标是让您开始开发您的第一个 Vue 应用程序。本章侧重于如何创建组件。

在本章中，我们将学习如何使用 Vue 3 从头开始创建简单的应用程序。我们将从构建最基本的应用程序开始，然后在接下来的几章中构建更复杂的解决方案。

我们将涵盖的主要主题如下：

+   了解 Vue 作为一个框架

+   设置 Vue 项目

+   Vue 3 核心功能-组件和内置指令

+   使用 Vue.js Devtools 进行调试

# 技术要求

本章的代码位于[`github.com/PacktPublishing/-Vue.js-3-By-Example/tree/master/Chapter01`](https://github.com/PacktPublishing/-Vue.js-3-By-Example/tree/master/Chapter01)。

# 了解 Vue 作为一个框架

正如我们在介绍中提到的，Vue 中有一些概念可以从其他框架中获得。指令可以像在 Angular.js 和 Angular 中一样操作**文档对象模型**（**DOM**）。模板可以像我们在 Angular 中一样渲染数据。它还有自己特殊的语法用于数据绑定和添加指令。

Angular 和 React 都有 props，用于在组件之间传递数据。我们还可以循环遍历数组和对象条目，以显示列表中的项目。与 Angular 一样，我们可以向 Vue 项目添加插件以扩展其功能。

Vue.js 独有的概念包括计算属性，这些属性是从其他属性派生出来的组件属性。此外，Vue 组件具有 watchers，让我们可以监视响应式数据的变化。响应式数据是由 Vue.js 监视的数据，当响应式数据发生变化时，操作会自动执行。

随着响应式数据的变化，组件的其他部分和引用这些值的其他组件都会自动更新。这就是 Vue 的魔力。这也是我们可以用如此少的代码做如此多事情的原因之一。它替我们负责监视数据变化的任务，这样我们就不必自己做了。

Vue 3 的另一个独特功能是，我们可以使用脚本标签添加框架及其库。这意味着，如果我们有一个旧的前端，我们仍然可以使用 Vue 3 及其库来增强旧的前端。此外，我们不需要添加构建工具来构建我们的应用程序。这是大多数其他流行框架所没有的一个很棒的功能。

还有流行的 Vue Router 库用于路由，以及 Vuex 库用于状态管理。它们都已更新为与 Vue 3 兼容，因此我们可以安全地使用它们。这样，我们就不必像在使用其他框架（如 React）时担心要使用哪个路由器和状态管理库。Angular 自带其自己的路由，但没有指定标准状态管理库。

# 使用 Vue CLI 和脚本标签设置 Vue 项目

有几种方法可以创建 Vue 项目或向现有前端添加脚本标签。对于原型设计或学习目的，我们可以通过添加以下代码来添加 Vue 3 的最新版本：

```js
<script src="https://unpkg.com/vue@next"></script>
```

这将始终在我们的应用程序中包含最新版本的 Vue。如果我们在生产中使用它，我们应该包含版本号，以避免新版本的意外更改破坏我们的应用程序。如果我们想指定版本，版本号可以替换`next`这个词。

我们还可以通过安装包来安装 Vue。为此，我们可以运行以下命令：

```js
npm install vue@next
```

这将在我们的 JavaScript 项目中安装最新版本的 Vue。

如果我们使用旧版本的 Vue CLI 从头创建了一个 Vue 项目，那么我们可以使用 CLI 为我们生成所有文件并安装所有包。这是开始使用 Vue 项目的最简单的方法。对于 Vue 3，我们应该使用 Vue CLI v4.5，通过运行以下命令：

```js
yarn global add @vue/cli@next
```

我们还可以通过运行以下命令安装 Vue 调色板：

```js
npm install -g @vue/cli@next
```

然后，要将我们的 Vue 项目升级到 Vue 3，我们可以运行以下命令：

```js
vue upgrade –-next
```

Vite 构建工具将让我们从头开始创建一个 Vue 3 项目。它可以比 Vue CLI 更快地为我们提供项目服务，因为它可以原生地处理模块。我们可以通过使用 NPM 运行以下命令来从头开始设置 Vue 项目：

```js
$ npm init vite-app <project-name>
$ cd <project-name>
$ npm install
$ npm run dev
```

使用 Yarn，我们必须运行以下命令：

```js
$ yarn create vite-app <project-name>
$ cd <project-name>
$ yarn
$ yarn dev
```

在任何情况下，我们都可以用我们选择的项目名称替换`<project-name>`。

我们可以使用各种版本的 Vue。其中一组是 CDN 版本，不带捆绑器。我们可以通过文件名中的`vue(.runtime).global(.prod).js`模式来识别它们。这些可以直接通过脚本标签包含。

我们使用它们与直接添加到 HTML 中的模板一起。`vue.global.js`文件是完整的构建，包括编译器和运行时，因此它可以从 HTML 动态编译模板。`vue.runtime.global.js`文件只包含运行时，并且需要在构建步骤中预编译模板。

开发和生产分支是硬编码的，我们可以通过检查文件是否以`.prod.js`结尾来区分它们。这些文件已经准备好用于生产，因为它们已经被压缩。这些不是**通用模块定义**（**UMD**）构建。它们包含用于常规脚本标签的 IIFE。

如果我们使用诸如 Webpack、Rollup 或 Parcel 之类的捆绑器，那么我们可以使用`vue(.runtime).esm-bundler.js`文件。开发和生产分支由`process.env.NODE_ENV`属性确定。它还有完整版本，它可以在运行时动态编译模板和运行时版本。

在本章中，我们将通过 Vue 的脚本标签版本介绍 Vue 的基本功能。在随后的章节中，我们将继续使用 Vue CLI 来创建我们的 Vue 3 项目。这样，我们可以专注于探索 Vue 3 的基本功能，这在我们转向创建更复杂的项目时会很有用。让我们开始创建一个 Vue 实例。

## 创建您的 Vue 实例

现在我们已经设置好了我们的 Vue 项目，我们可以更仔细地看一下 Vue 实例。所有 Vue 3 应用程序都有一个 Vue 实例。Vue 实例充当应用程序的入口点。这意味着这是首先加载的内容。它是应用程序的根组件，它有一个模板和一个组件选项对象，用于控制模板在浏览器中的呈现方式。

要创建我们的第一个 Vue 3 应用程序，我们必须将以下代码添加到`index.html`文件中：

```js
<!DOCTYPE html>
<html lang="en">
  <head>
    <title>Vue App</title>
    <script src="https://unpkg.com/vue@next"></script>
  </head>
  <body>
    <div id="app">
      count: {{ count }}
    </div>
    <script>
      const Counter = {
        data() {
          return {
            count: 0
          };
        }
      };
      Vue.createApp(Counter).mount("#app");
    </script>
  </body>
</html>
```

在我们的第一个 Vue 3 应用程序中，我们首先添加了`script`标签以添加 Vue 框架脚本。它还不是最终版本，所以我们添加了 Vue 脚本的下一个版本。

在 body 中，我们有一个 ID 为 app 的`div`，我们用它来容纳模板。模板中唯一的内容将由 Vue 3 附带的模板编译器编译。在下面，我们有一个`script`标签来创建我们的应用程序。它提供了`Counter`对象，其中包含我们可以用来创建应用程序的属性。

Vue 组件作为对象提供，Vue 将使用它们来创建任何必要的组件。`data`属性是一个特殊属性，返回我们状态的初始值。这些状态是自动响应的。`count`状态是一个我们可以更新的响应式状态。它与模板中的相同。花括号中的任何内容必须是包含响应式属性或其他 JavaScript 表达式的表达式。

如果我们在花括号之间添加响应式状态，它们将被更新。由于`count`响应式属性被初始化为`0`，模板中的`count`属性也是`0`。`Counter`对象被传递到`Vue.createApp`方法中，以编译模板并连接响应式属性，将花括号内的表达式渲染为最终结果。因此，我们应该在渲染的输出中看到`count: 0`。

`mount()`方法接受一个 CSS 选择器字符串作为参数。选择器是要在其中渲染应用程序的模板。其中的任何内容都将被视为 Vue 表达式，并相应地进行渲染。花括号中的表达式将被渲染，属性将被 Vue 解释为 props 或指令，具体取决于它们的编写方式。

在下一节中，我们将看一下 Vue.js 3 的核心特性。

# Vue 3 核心特性 - 组件和内置指令

现在我们已经创建了一个带有 Vue 实例的基本 Vue 应用程序，我们可以更仔细地看一下如何使它做更多的事情。Vue 3 是一个基于组件的框架。因此，组件是用于构建完整的生产质量 Vue 3 应用程序的核心构建块。组件是可以组合成完整应用程序并且可重用的部分。Vue 3 组件有几个部分，包括模板、组件选项对象和样式。样式是我们应用于渲染元素的 CSS 样式。模板是在浏览器屏幕上呈现的内容。它包含 HTML 代码和 JavaScript 表达式，形成在浏览器中呈现的内容。

模板从相应的组件选项对象获取数据。此外，组件模板可以具有指令，控制内容的呈现方式以及将数据从模板绑定到响应式属性。

## 组件

我们用一个 Vue 实例创建了一个基本的 Vue 应用。现在，我们必须找到一种管理我们应用的方法。Vue 3 是一个基于组件的前端框架。这意味着使用 Vue 3 创建的应用是由多个组件组合而成的。这样，我们可以保持应用的每个部分都很小，这有助于使测试变得容易，也容易调试。这对我们来说很重要，因为我们正在创建一个为用户提供功能的非平凡应用。

在 Vue 3 中，组件是具有一些预定义选项的 Vue 实例。要在另一个组件中使用组件，我们必须注册它们。要创建一个 Vue 组件，我们可以调用`app.component()`方法。第一个参数是组件，称为`string`，而第二个参数是一个包含组件选项的对象。

一个最小的组件应该至少包含添加到对象中的模板属性。这样，它将在我们的组件中显示一些内容，使其有用。我们将首先创建一个用于显示待办事项的组件。为了显示我们的待办事项，我们可以创建一个`todo-item`组件。此外，组件很可能需要接受 props 来显示来自其父组件的数据。**prop**是一个特殊的属性，让 Vue 组件将一些数据传递给子组件。子组件具有`props`属性来定义它将接受的值的类型。为此，我们可以编写以下代码：

```js
<!DOCTYPE html>
<html lang="en">
  <head>
    <title>Vue App</title>
    <script src="https://unpkg.com/vue@next"></script>
  </head>
  <body>
    <div id="app">
      <div>
        <ol>
           ...
            ]
          };
        }
      };
      const app = Vue.createApp(App);
      app.component("todo-item", {
        props: ["todo"],
        template: `<li>{{todo.description}}</li>`
      });
      app.mount("#app");
    </script>
  </body>
</html>
```

我们调用了`app.component()`方法来创建`todo-item`组件。它包含了`props`属性，其中包含一个接受`todo`prop 的 prop 名称数组。我们定义 prop 的方式意味着我们可以接受任何值作为`todo`prop 的值。我们还可以指定它们的值类型，设置它是否是必需的，或为其提供默认值。`template`属性让我们在需要时呈现它。我们只需将其设置为一个字符串，它将像任何其他模板一样呈现项目。

`li`元素在模板中呈现。花括号的工作方式与任何其他模板相同。它用于插值值。要访问 prop 的值，我们只需在组件中将其作为 this 的属性访问，或者在模板中直接使用 prop 名称本身。

要将`todo`属性从根 Vue 实例传递给`todo-item`组件，我们需要使用冒号前缀属性名称，以指示它是一个 prop。冒号是`v-bind`的简写。`v-bind`指令是 Vue 的内置指令，它让我们将数据作为 prop 传递给子组件。如果 prop 名称是驼峰式命名，则它将被映射到 HTML 中的 kebab-case 名称，以保持其有效。这是因为有效的属性应该具有 kebab-case 名称。Vue 3 附带的模板编译器将自动执行映射。因此，我们只需遵循惯例，然后我们就可以正确传递我们的 props。

如果我们使用`v-for`指令，我们应该添加 key 属性，以便 Vue 3 可以正确跟踪项目。使用`v-for`指令，我们可以循环遍历数组或对象，并显示它们的条目。值应该是一个唯一的 ID，以便 Vue 可以正确渲染项目，即使我们交换项目的位置并添加或删除项目并在列表中执行其他操作。为此，我们可以编写以下代码：

```js
<!DOCTYPE html>
<html lang="en">
  <head>
    <title>Vue App</title>
    <script src="https://unpkg.com/vue@next"></script>
  </head>
  <body>
  ...
    </div>
    <script>
      const App = {
        data() {
          return {
            todos: [
              { id: 1, description: "eat" },
              { id: 2, description: "drink" },
              { id: 3, description: "sleep" }
            ]
          };
          ...
      app.mount("#app");
    </script>
  </body>
</html>
```

每个`id`属性值对于 Vue 的列表跟踪是唯一的。

Vue 组件看起来像 Web 组件规范中的自定义元素，但 Vue 组件不是自定义元素。它们不能互换使用。这只是一种使用熟悉的语法来创建组件的方式，这是标准的。Vue 组件中有一些特性在自定义元素中是不可用的。在自定义元素中没有跨组件数据流、自定义事件通信和构建工具集成。然而，所有这些特性都在 Vue 组件中可用。我们将在接下来的部分中介绍 Vue 组件的这些特性。

### 组件生命周期

每个 Vue 3 组件都有自己的生命周期，每个生命周期阶段都有自己的方法。如果达到了生命周期的给定阶段，并且在组件中定义了该方法，那么该方法将被运行。

在使用`app.mount()`挂载应用程序后，事件和生命周期被初始化。当组件加载时将运行的第一个方法是`beforeCreate()`方法。然后，组件被初始化为响应式属性。然后运行`created()`方法。由于在这个阶段初始化了响应式属性，我们可以在这个方法和之后加载的方法中访问这些响应式属性。

然后，运行组件的模板或渲染函数来呈现项目。内容加载完成后，将运行`beforeMount`。一旦运行了`beforeMount`，应用程序将被挂载到我们在`app.mount()`方法中传递给选择器指定的元素中。

一旦应用程序被挂载到元素中，就会运行挂载钩子。现在，当任何响应属性发生变化时，将运行`beforeUpdate`钩子。然后，重新渲染虚拟 DOM，并从响应属性的最新值呈现最新的项目。这是运行任何外部库的初始化代码的好地方。完成后，将运行`updated`钩子。

`beforeDestroy`在组件卸载之前运行。这是在销毁组件之前运行任何清理代码的好地方。当组件被销毁时，将运行`destroyed`钩子。在这里，响应属性将不可用。

### 响应属性

响应属性是组件选项对象的属性，它们让我们同步模板中显示的内容，并根据我们对它们进行的操作而改变。对响应属性所做的任何更改都会在引用它们的任何地方传播到整个应用程序中。

在前面的示例中，我们向我们的应用程序添加了`count`响应属性。要更新它，我们只需更新响应属性的值本身：

```js
<!DOCTYPE html>
<html lang="en">
  <head>
    <title>Vue App</title>
    <script src="https://unpkg.com/vue@next"></script>
  </head>
  <body>
    <div id="app">
      <button @click="count++">increment</button>
      count: {{ count }}
    </div>
    <script>
      const Counter = {
        data() {
          return {
            count: 0
          };
        }
      };
      Vue.createApp(Counter).mount("#app");
    </script>
  </body>
</html>
```

在这里，我们有`@click="count++"`表达式，它监听按钮的点击，并在点击增加按钮时将计数增加`1`。最新的值将在任何地方都得到反映，因为它是一个响应属性。Vue 可以自动捕捉到响应属性的变化。`@click`是`v-on:click`的简写。

此外，我们可以将表达式重写为方法。为此，我们可以编写以下代码：

```js
<!DOCTYPE html>
<html lang="en">
  <head>
    <title>Vue App</title>
    <script src="https://unpkg.com/vue@next"></script>
  </head>
  <body>
    <div id="app">
      <button @click="increment">increment</button>
      count: {{ count }}
    </div>
    <script>
      const Counter = {
        data() {
          return {
            count: 0
          };
        },
        methods: {
          increment() {
            this.count++;
          }
        }
      };
      Vue.createApp(Counter).mount("#app");
    </script>
  </body>
</html>
```

引用 Vue 实例对象中的`count`响应属性，我们必须将其作为`this`的属性引用。因此，在 Vue 实例对象中的`this.count`与模板中的`count`是相同的。`this`关键字指的是组件实例。我们应该记住这一点，这样我们就不会在以后遇到问题。

此外，我们将方法的属性添加到组件对象中。这是一个特殊的属性，用于在我们的代码中保存我们可以在 Vue 实例的其他部分或模板中引用的方法。与响应属性一样，方法在 Vue 实例对象中被引用为`this`的属性，并且在模板中省略了`this`。

因此，当我们点击按钮时，我们运行`methods`属性中的增量方法。当我们点击按钮时，计数值将增加`1`，我们应该在浏览器的输出中看到它显示。

### 处理用户输入

大多数应用程序需要用户向表单输入内容。我们可以使用 Vue 3 的`v-model`指令轻松实现这一点。它将输入的值与我们在 Vue 实例中定义的响应属性进行同步。

要使用它，我们只需将`v-model`属性添加到输入框中。为此，我们可以编写以下代码：

```js
<!DOCTYPE html>
<html lang="en">
  <head>
    <title>Vue App</title>
    <script src="https://unpkg.com/vue@next"></script>
  </head>
  <body>
    <div id="app">
      <p>{{ message }}</p>
      <input v-model="message" />
    </div>
    <script>
      const App = {
        data() {
          return {
            message: "hello world."
          };
        }
      };
      Vue.createApp(App).mount("#app");
    </script>
  </body>
</html>
```

在这里，我们有`message`响应属性，它已初始化为'hello world.'字符串。我们可以通过将其设置为`v-model`指令的值，在模板中使用相同的值。它将在输入的值和`message`响应属性之间进行同步，以便我们输入的任何内容都会传播到 Vue 实例的其余部分。

因此，'hello world.'字符串既显示在输入框中，也显示在段落元素中。当我们在输入框中输入内容时，它也会显示在段落元素中。它将更新`message`响应属性的值。这是 Vue 3 带来的一个很棒的功能，我们将在许多地方使用它。

### 条件和循环

Vue 3 的另一个非常有用的功能是我们可以在模板中有条件地渲染内容。为此，我们可以使用`v-if`指令，它让我们有条件地显示某些内容。`v-if`指令只有在我们分配给它的条件为真时，才将元素放入 DOM 中。`v-show`指令使用 CSS 显示和隐藏它绑定的元素，并且该元素始终在 DOM 中。如果它的值为真，我们将在模板中看到它显示。否则，我们不会看到该项显示。

它通过有条件地将项目附加到 DOM 来工作。只有当`v-if`值为真时，具有`v-if`指令的元素或组件内的元素和组件才会被附加到 DOM 中。否则，它们不会附加到 DOM 中。

例如，假设我们有以下代码：

```js
<!DOCTYPE html>
<html lang="en">
  <head>
    <title>Vue App</title>
    <script src="https://unpkg.com/vue@next"></script>
  </head>
  <body>
    <div id="app">
      <span v-if="show">hello world</span>
    </div>
    <script>
      const App = {
        data() {
          return {
            show: true
          };
        }
      };
      Vue.createApp(App).mount("#app");
    </script>
  </body>
</html>
```

在这里，'hello world'将被显示，因为`show`是`true`。如果我们有以下代码，我们将看不到任何显示，因为 span 没有附加到 DOM 上：

```js
<!DOCTYPE html>
<html lang="en">
  <head>
    <title>Vue App</title>
    <script src="https://unpkg.com/vue@next"></script>
  </head>
  <body>
    <div id="app">
      <span v-if="show">hello world</span>
    </div>
    <script>
      const App = {
        data() {
          return {
            show: false
          };
        }
      };
      Vue.createApp(App).mount("#app");
    </script>
  </body>
</html>
```

要在模板中呈现项目数组和最终输出，我们可以使用`v-for`指令。我们放置一个特殊的 JavaScript 表达式，让我们循环遍历数组。我们可以通过编写以下代码来使用`v-for`指令：

```js
<!DOCTYPE html>
<html lang="en">
  <head>
    <title>Vue App</title>
    <script src="https://unpkg.com/vue@next"></script>
  </head>
  <body>
    <div id="app">
      <ol>
        <li v-for="todo in todos" :keu="todo.id">
          {{ todo.description }}
        </li>
      </ol>
    </div>
    <script>
      const App = {
        data() {
          return {
            todos: [
              { description: "eat", id: 1 },
              { description: "drink", id: 2 },
              { description: "sleep", id: 3 }
            ]
          };
        }
      };
      Vue.createApp(App).mount("#app");
    </script>
  </body>
</html>
```

我们在`li`元素中使用了`v-for`指令。`'todo in todos'`循环遍历`todo`数组并呈现标签之间的项目。`todo`变量是正在迭代的单个*todos*条目。我们访问描述属性，以便我们可以在列表中显示描述的值。

完成后，我们将看到一个带有`todo`文本的编号列表。

### 模板语法

我们已经广泛使用了模板。我们主要使用插值来显示数据和一些指令来呈现数据。此外，我们可以使用`@`或`v-on`指令来监听发出的事件，例如点击和输入值更改。

还有其他有用的语法，我们可以用来创建模板。其中之一是使用插值表达式显示原始 HTML。默认情况下，Vue 3 会转义所有 HTML 实体，以便它们按原样显示。`v-html`指令让我们将 HTML 代码显示为真正的 HTML，而不是纯文本。

例如，让我们编写以下代码：

```js
<!DOCTYPE html>
<html lang="en">
  <head>
    <title>Vue App</title>
    <script src="https://unpkg.com/vue@next"></script>
  </head>
  <body>
    <div id="app">
      <span v-html="rawHtml"></span>
    </div>
    <script>
      const App = {
        data() {
          return {
            rawHtml: `<b>hello world</b>`
          };
        }
      };
      const app = Vue.createApp(App);
      app.mount("#app");
    </script>
  </body>
</html>
```

在这里，我们将`rawHtml`响应式属性设置为`v-html`的值，这样我们就可以看到`b`标签被呈现为粗体文本，而不是以原始形式呈现的字符。

### JavaScript 表达式和模板

我们可以在大括号之间放置任何 JavaScript 表达式。它只能是单个表达式。

例如，以下代码片段显示了大括号之间的有效内容：

```js
{{ number + 1 }} 
{{ areYouSure ? 'YES' : 'NO' }}
{{ message.split('').reverse().join('') }}
```

但是，我们不能在大括号之间放置任何 JavaScript 语句。例如，我们不能写`{{ var foo = 1 }}`或`{{ if (yes) { return message } }}`。

### 计算属性

**计算属性**是从其他响应式属性派生出来的特殊响应式属性。计算属性被添加到计算属性对象中作为函数。它们总是返回从其他响应式属性派生出来的东西。因此，它们必须是同步函数。

要创建计算属性，我们可以编写以下代码：

```js
<!DOCTYPE html>
<html lang="en">
  <head>
    <title>Vue App</title>
    <script src="https://unpkg.com/vue@next"></script>
  </head>
  <body>
    <div id="app">
      <p>{{message}}</p>
      <p>{{reversedMessage}}</p>
    </div>
    <script>
      const App = {
        data() {
          return {
            message: "hello world"
          };
        },
        computed: {
          reversedMessage() {
            return
             this.message.split("").reverse().join("");
          }
        }
      };
      const app = Vue.createApp(App);
      app.mount("#app");
    </script>
  </body>
</html>
```

在这里，我们创建了`reversedMessage`计算属性，它是`message`响应式属性的反转。我们返回了字符顺序反转后的消息。每当`message`响应式属性更新时，`reversedMessage()`方法将再次运行并返回最新的值。因此，我们可以在同一个模板中看到`'hello world'`和`'dlrow olleh'`。这些计算属性的返回值必须包含其他响应式属性，以便在其他响应式属性更新时它们也会更新。

## 指令

组件可能没有足够的能力做我们想要的事情。最重要的缺失是操作 DOM 并将输入数据与响应式属性同步的能力。指令是以`v-`前缀开头的特殊属性。它们期望单个 JavaScript 表达式作为值。我们已经看到一些内置指令，比如`v-if`、`v-for`、`v-bind`和`v-on`被用于各种目的。指令除了值之外还可以带参数。

例如，我们可以写`<a v-on:click="doSomething"> ... </a>`来监听锚元素上的点击事件。`v-on`部分是指令的名称。冒号和等号之间的部分是指令的参数，所以`click`是指令的参数。`doSomething`是指令的值。它是我们想要调用的方法的名称。

指令参数可以是动态的。要添加动态参数，我们可以将它们放在方括号之间：

```js
<a v-bind:[attributeName]="url"> ... </a>
```

`attributeName`是我们想要用来设置参数值的响应式属性。它应该是一个字符串。我们也可以用`v-on`指令做同样的事情：

```js
<a v-on:[eventName]="doSomething"> ... </a>
```

我们使用给定的`eventName`来监听事件。`eventName`也应该是一个字符串。

### 指令修饰符

指令可以带有修饰符，让我们改变指令的行为。修饰符是以点表示的特殊后缀。它们可以链接在一起以提供更多的改变。它们表示指令应该以某种特殊的方式绑定。例如，如果我们需要监听`submit`事件，我们可以添加`prevent`修饰符来调用`event.preventDefault()`，这将阻止默认的提交行为。我们可以通过编写以下代码来实现：

```js
<form v-on:submit.prevent="onSubmit">...</form>
```

接下来，我们将看看如何使用 Vue.js Devtools 浏览器扩展轻松调试 Vue 3 项目。

# 使用 Vue.js Devtools 进行调试

现在，我们没有简单的方法来调试我们的应用程序。我们只能在代码中添加`console.log`语句来查看值。使用 Vue.js Devtools，我们可以更清晰地看到我们的应用程序。Vue.js Devtools 是一个 Chrome 或 Firefox 扩展，我们可以用它来调试我们的 Vue.js 应用程序。它可以用于使用 Vite 创建的项目，也可以通过包含 Vue 3 的`script`标签从头开始创建的项目。我们可以通过在各自浏览器的应用商店中搜索 Vue.js Devtools 扩展来安装该扩展。

重要提示：

安装 Chrome 版本的 Vue.js Devtools 的 URL 在[`chrome.google.com/webstore/detail/vuejs-devtools/nhdogjmejiglipccpnnnanhbledajbpd`](https://chrome.google.com/webstore/detail/vuejs-devtools/nhdogjmejiglipccpnnnanhbledajbpd)。

Firefox 版本的插件在[`addons.mozilla.org/en-CA/firefox/addon/vue-js-devtools/?utm_source=addons.mozilla.org&utm_medium=referral&utm_content=search`](https://addons.mozilla.org/en-CA/firefox/addon/vue-js-devtools/?utm_source=addons.mozilla.org&utm_medium=referral&utm_content=search)。

安装完成后，我们应该在浏览器的开发控制台中看到 Vue 选项卡。通过它，我们可以检查 Vue 加载的响应式属性。如果我们的组件有一个`name`属性，那么它将显示在应用程序的组件树中。例如，假设我们有以下代码：

```js
<!DOCTYPE html>
<html lang="en">
  <head>
    <title>Vue App</title>
    <script src="https://unpkg.com/vue@next"></script>
  </head>
  <body>
    <div id="app">
      <foo></foo>
    </div>
    <script>
      const App = {
        data() {
          return {};
        }
      };
      const app = Vue.createApp(App);
      app.component("foo", {
        data() {
          return {
            message: "I am foo."
          };
        },
        name: "foo",
        template: `<p>{{message}}</p>`
      });
      app.mount("#app");
    </script>
  </body>
</html>
```

在这里，由于我们将`foo`组件的`name`属性设置为`'foo'`，我们将在组件树中看到它的列表。此外，`foo`组件具有`message`响应式属性，因此我们还将看到`message`属性及其值的显示。在组件树上方，有一个搜索框，让我们可以找到具有给定名称的响应式属性。我们还可以在**查找组件**`…`输入框中搜索组件。

以下截图显示了我们的 Vue 3 应用程序中 Vue Devtools 扩展中的响应式属性的值：

![图 1.1-使用 Vue Devtools 检查响应式属性](img/Figure_1.1_B14405.jpg)

图 1.1-使用 Vue Devtools 检查响应式属性

还有“时间轴”菜单项，我们可以用它来检查被触发的事件。例如，假设我们有以下代码：

```js
<!DOCTYPE html>
<html lang="en">
<head>
  <title>Vue App</title>
  <script src="https://unpkg.com/vue@next"></script>
</head>
<body>
  <div id="app">
    <button @click="count++">increment</button>
    count: {{ count }}
  </div>
  <script>
    const Counter = {
      data() {
        return {
          count: 0
        };
      }
    };
    Vue.createApp(Counter).mount("#app");
  </script>
</body>
</html>
```

当我们点击“增加”按钮时，我们将在“时间轴”部分看到鼠标事件的记录。事件触发的时间也将被记录。

在**全局设置**部分，我们可以看到**规范化组件名称**设置，它让我们可以改变组件名称的显示方式。我们可以以帕斯卡命名法或短横线命名法显示原始名称。**主题**选项让我们可以改变 Vue 标签的主题颜色。

# 总结

在本章中，我们了解到 Vue 3 是一个基于组件的框架，并且我们看了组件的不同部分。我们涵盖的一个重要部分是响应式属性。它们是组件的属性，我们可以更改以更新引用响应式属性的应用程序的所有部分。这些属性可以手动监视，任何值的更改也会被 Vue 3 自动捕捉到，以便自动更新引用响应式属性的应用程序的任何部分。组件被组合在一起，以便在可能的情况下可以重复使用。

然后，我们继续了解了每个组件中模板的作用。模板也是每个组件的一部分。它们必须在屏幕上渲染出一些东西。模板可以包含 HTML 元素、其他组件和修改模板中元素和组件渲染方式的指令。模板中可以包含 JavaScript 表达式，这样我们就可以对事件做出反应。然后，我们看到了组件中计算属性的重要性。计算属性是依赖于其他响应式属性的特殊响应式属性。它们是同步的函数，并根据其他响应式属性的组合返回一个值。

我们看到的另一个重要点是内置在 Vue 3 中的`v-model`指令。Vue 3 提供了`v-model`指令，这样我们就可以将响应式属性绑定到表单控件的值上。指令是特殊的 Vue 代码，让我们可以改变 DOM 元素的渲染方式。Vue 3 提供了许多内置指令，可以做诸如从数组中渲染元素、将表单控件的值绑定到响应式属性等事情。

在最后一节中，我们学习了如何使用 Vue.js Devtools 来简化调试。这是一个适用于 Chromium 浏览器和 Firefox 的浏览器扩展，让我们可以观察组件的响应式属性值，并查看渲染的组件。它还会记录组件中元素触发的任何事件。

在下一章中，我们将学习如何构建一个简单的 GitHub 应用程序，进行 HTTP 请求。
