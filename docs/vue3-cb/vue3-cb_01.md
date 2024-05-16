# 第一章：了解 Vue 3 和创建组件

Vue 3 带来了许多新功能和变化，所有这些都旨在帮助开发并改善框架的整体稳定性、速度和可维护性。借鉴其他框架和库的灵感，Vue 核心团队设法在 API 上实现了很高的抽象水平，现在任何人都可以使用 Vue，无论他们是前端开发人员还是后端开发人员。

在本章中，我们将学习如何将我们的 Vue 项目升级到新版本，以及一些新的 Vue 功能，比如多个根元素，新的属性继承引擎，我们如何在另一个应用程序中使用暴露的响应性 API，以及如何使用新的组合 API 创建组件。

在本章中，您将学习以下内容：

+   Vue 3 有什么新功能

+   将您的 Vue 2 应用程序升级到 Vue 3

+   使用多个根元素创建组件

+   使用属性继承创建组件

+   在 Vue 范围之外使用响应性和可观察 API

+   使用组合 API 创建组件

# Vue 3 有什么新功能

你可能想知道一个框架的新版本怎么会在互联网上引起如此大的轰动？想象一下把一辆汽车开上高速公路，做一个完整的 360 度翻滚，然后继续朝着同一个方向全速前进。这将引起一场戏剧性的场面，这正是描述 Vue 将从 2 版本升级到 3 版本的完美方式。

在本章的第一部分，我将向您介绍 Vue 的改进，框架中添加了什么，发生了什么变化，以及它将如何影响您编写 Vue 应用程序的方式。

## 框架的改进

在这个新版本中，Vue 框架有许多改进；所有这些改进都集中在尽可能使框架更好。以下是一些可能影响用户和开发人员日常开发和使用框架的改进。

### 底层

外壳看起来和旧的一样，但引擎是一件艺术品。在新版本中，没有留下来自 Vue 2 的代码。核心团队使用 TypeScript 从头开始构建了框架，并重写了一切，以最大程度地提高框架的性能。

选择了 TypeScript 来创建 Vue 核心团队和开源社区更易于维护的代码库，并改进自动完成功能，如 IDE 和代码编辑器提供的**IntelliSense**或**typeahead**，无需特殊的插件和扩展。

### 渲染引擎

对于 Vue 3，使用了一种新的算法开发了一个新的渲染引擎，用于影子 DOM。这个新的渲染引擎默认情况下完全暴露在框架的核心中，无需由框架执行。这使得可以实现一个全新的渲染函数的新实现，可以注入到框架中并替换原始的渲染引擎。

在这个新版本的 Vue 中，从头开始编写了一个新的模板编译器。这个新的编译器使用了一种新的缓存操作和管理渲染元素的新技术，并应用了一种新的提升方法来创建 VNodes。

对于缓存操作，应用了一种新的方法来控制元素的位置，其中元素可以是具有计算数据的动态元素，也可以是对可以被改变的函数的响应。

Vue 核心团队制作了一个浏览器，可以看到新模板编译器如何渲染最终的`render`函数。可以在[`vue-next-template-explorer.netlify.app/`](https://vue-next-template-explorer.netlify.app/)上查看。

### 暴露的 API

通过所有这些修改，可以在 Vue 应用程序范围之外的文件中渲染所有暴露给使用的 Vue API。可以在 React 应用程序中使用 Vue 响应性或影子 DOM，而无需在 React 应用程序内部渲染 Vue 应用程序。这种可扩展性是将 Vue 转变为更多功能的框架的一种方式，它可以在任何地方使用，不仅仅是在前端开发中。

## 新的自定义组件

Vue 3 引入了三个新的自定义组件，开发人员可以使用这些组件来解决旧问题。这些组件在 Vue 2 中也存在，但作为第三方插件和扩展。现在它们由 Vue 核心团队制作，并添加到 Vue 核心框架中。

### 片段

在 Vue 2 中，我们总是需要在单文件组件内部的组件周围有一个父节点。这是由于 Vue 2 的渲染引擎的构造方式所致，需要在每个节点上都有一个根元素。

在 Vue 2 中，我们需要有一个包装元素，封装将要呈现的元素。在这个例子中，我们有一个`div` HTML 元素，包装了两个`p` HTML 子元素，这样我们就可以在页面上实现多个元素：

```js
<template>
 <div>
 <p>This is two</p>
  <p>children elements</p>
  </div> </template>
```

现在，在 Vue 3 中，可以在单文件组件中声明任意数量的根元素，而无需使用新的 Fragments API 特殊插件，它将处理多个根元素。这有助于为用户保持更清洁的最终代码，而无需为包装元素而创建空壳：

```js
<template>
 <p>This is two</p>
  <p>root elements</p> </template>
```

正如我们在 Vue 3 代码中看到的，我们能够有两个根`p` HTML 元素，而无需包装元素。

### Teleport

`Teleport`组件，也称为 Portal 组件，顾名思义，是一个可以使元素从一个组件移动到另一个组件的组件。这一开始可能看起来很奇怪，但它有许多应用，包括对话框、自定义菜单、警报、徽章和许多其他需要出现在特殊位置的自定义 UI。

想象一个标题组件，您希望在组件上放置一个自定义插槽，以便放置组件：

```js
<template>
  <header>
    <div id="blue-portal" />
  </header>
</header>  
```

然后，您想在此标题上显示一个自定义按钮，但您希望从页面上调用此按钮。您只需要执行以下代码：

```js
<template>
  <page>
   <Teleport to="blue-portal">
     <button class="orange-portal">Cake</button>
   </Teleport>
  </page>
</template>
```

现在，您的按钮将显示在标题上，但代码将在页面上执行，从而访问页面范围。

### 悬念

当等待数据的时间比您想要的时间长时，如何为用户显示自定义加载程序？现在这是可能的，而无需自定义代码；Vue 将为您处理。`Suspense`组件将管理此过程，在数据加载完成后显示默认视图，并在加载数据时显示备用视图。

您可以编写一个特殊的包装器，如下所示：

```js
<template>
  <Suspense>
    <template #default>
      <data-table />
    </template>
    <template #fallback>
      <loading-gears />
    </template>
  </Suspense>
</template>
```

新的 Vue 组合 API 将了解组件的当前状态，因此它将能够区分组件是正在加载还是准备好显示。

## API 更改

Vue 3 进行了一些 API 更改，这些更改是为了清理 Vue API 并简化开发而必要的。其中一些是破坏性的更改，另一些是新增的。但不用担心；Vue 2 对象开发并没有被移除，它仍然存在，并将继续使用。这种声明方法是许多开发人员选择 Vue 而不是其他框架的原因之一。

Vue 3 中将出现一些重要的变化，这些变化很重要，需要更多了解。我们将讨论 Vue 3 中将引入的最重要的变化，以及如何处理它们。

在 Vue 3 中，正在引入一种创建组件的新方法——组合 API。这种方法将使您的代码更易于维护，并为您提供更可靠的代码，您将拥有 TypeScript 的全部功能。

### 一些较小的变化

在 Vue 3 中存在一些较小的变化，需要提及。这些变化涉及我们以前用来编写代码的一种方法，现在在使用 Vue 3 时已经被替换。这并不是一项艰巨的工作，但您需要了解这些变化。

#### 再见过滤器，你好过滤器！Vue 过滤器 API

在 Vue 2 中，我们使用`filters`的方式已经不再可用。Vue 过滤器已从 API 中删除。这一变化是为了简化渲染过程并加快速度。最终，所有过滤器都是接收一个字符串并返回一个字符串的函数。

在 Vue 2 中，我们使用`filters`如下：

```js
{{ textString | filter }}
```

现在，在 Vue 3 中，我们只需要传递一个`function`来操作`string`：

```js
{{ filter(textString) }}
```

#### 公交车刚刚离开车站！事件总线 API

在 Vue 2 中，我们能够利用全局 Vue 对象的力量创建一个新的 Vue 实例，并使用这个实例作为一个事件总线，可以在组件和函数之间传输消息而不需要任何麻烦。我们只需要发布和订阅事件总线，一切都很完美。

这是在组件之间传输数据的一个好方法，但对于 Vue 框架和组件来说是一种反模式的方法。在 Vue 中，在组件之间传输数据的正确方式是通过父子通信或状态管理，也被称为状态驱动架构。

在 Vue 3 中，`$on`、`$off`和`$once`实例方法已被移除。现在，要使用事件总线策略，建议使用第三方插件或框架，如 mitt（[`github.com/developit/mitt`](https://github.com/developit/mitt)）。

#### 不再有全局 Vue——挂载 API

在 Vue 2 中，我们习惯于导入 Vue，并在挂载应用程序之前，使用全局 Vue 实例来添加`plugins`、`filters`、`components`、`router`和`store`。这是一种很好的技术，我们可以向 Vue 实例添加任何内容，而无需直接附加到挂载的应用程序上。它的工作原理如下：

```js
import Vue from 'vue';
import Vuex from 'vuex';
import App from './App.vue';

Vue.use(Vuex);
const store = new Vuex.store({});

new Vue({
  store,
  render: (h) => h(App),
}).$mount('#app');
```

现在，在 Vue 3 中，这是不再可能的。我们需要直接将每个`component`、`plugin`、`store`和`router`附加到挂载的实例上：

```js
import { createApp } from 'vue';
import { createStore } from 'vuex';
import App from './App.vue';

const store = createStore({});

createApp(App)
  .use(store)
  .mount('#app');
```

使用这种方法，我们可以在同一个全局应用程序中创建不同的 Vue 应用程序，而不会相互干扰。

#### v-model，v-model，v-model - 多个 v-model

在开发单文件组件时，我们被限制为只能使用一个`v-model`指令和一个`.sync`选项来进行第二次更新更改。这意味着我们需要使用大量自定义事件发射器和巨大的对象负载来处理组件内的数据。

在这次重大变化中，引入了一个相关的破坏性变化，导致 Vue API 中的`model`属性被移除。这个属性用于自定义组件，以前可以做与新的 v-model 指令现在所做的相同的事情。

使用`v-model`指令的新方法将改变糖语法的工作方式。在 Vue 2 中，要使用`v-model`指令，我们需要创建一个组件，期望接收`props`为`"value"`，当有变化时，我们需要发出一个`'input'`事件，就像下面的代码：

```js
<template>
  <input 
    :value="value" 
    @input="$emit('input', $event)" 
  />
</template>
<script>
export default {
  props: {
    value: String,
  },
}
</script>
```

在 Vue 3 中，为了使语法糖工作，组件将接收的`props`属性和事件发射器将发生变化。现在，组件期望一个名为`modelValue`的`props`，并发出一个名为`'update:modelValue'`的事件，就像下面的代码：

```js
<template>
  <input 
    :modelValue="modelValue" 
    v-on:['update:modelValue']="$emit('update:modelValue', $event)" 
  />
</template>
<script>
export default {
  props: {
    modelValue: String,
  },
}
</script>
```

但是多个`v-model`指令呢？理解`v-model`的破坏性变化是了解多个`v-model`新方法如何工作的第一步。

要创建多个`v-model`组件，我们需要创建各种`props`，并使用`'update:value'`事件发出值作为模型指令的名称：

```js
<script>
export default {
  props: {
    name: String,
    email: String,
  },
  methods: {
   updateUser(name, email) {
    this.$emit('update:name', name);
    this.$emit('update:email', email);
   }
  }
}
</script>
```

在我们想要使用多个`v-model`指令的组件中，使用以下代码：

```js
<template>
  <custom-component
    v-model:name="name"
    v-model:email="email"
  />
</template>
```

组件将有每个`v-model`指令，绑定到子组件正在发出的事件。在这种情况下，子组件发出`'update:email'`（父组件）以便能够使用`v-model`指令与 email 修饰符。例如，您可以使用`v-model:email`来创建组件和数据之间的双向数据绑定。

### 组合 API

这是 Vue 3 最受期待的功能之一。组合 API 是创建 Vue 组件的一种新方式，以优化的方式编写代码，并在组件中提供完整的 TypeScript 类型检查支持。这种方法以更简单和更高效的方式组织代码。

在这种声明 Vue 组件的新方式中，你只需要一个`setup`属性，它将被执行并返回组件执行所需的一切，就像这个例子：

```js
<template>
 <p @click="increaseCounter">{{ state.count }}</p> </template> <script> import { reactive, ref } from 'vue';   export default {
  setup(){
  const state = reactive({
  count: ref(0)
 });    const increaseCounter = () => {
  state.count += 1;
 }    return { state, increaseCounter }
 } } </script>
```

您将从 Vue 核心导入`reactivity` API，以在对象类型数据属性中启用它，例如`state`。`ref` API 可以使基本类型值（如`count`）具有反应性，它是一个数字。

最后，函数可以在`setup`函数内部声明，并在返回的对象中传递。然后，所有内容都可以在`<template>`部分中访问。

现在，让我们继续进行一些示例。

# 技术要求

在本章中，我们将使用**Node.js**和**Vue-CLI**。

注意 Windows 用户！您需要安装一个名为`windows-build-tools`的 NPM 包，以便能够安装以下必需的包。为此，请以管理员身份打开 Power Shell 并执行以下命令：

`> npm install -g windows-build-tools`

要安装 Vue-CLI，您需要打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows）并执行以下命令：

```js
> npm install -g @vue/cli @vue/cli-service-global
```

## 创建基本文件

在本章的所有示例中，我们将使用这个基本模板，现在我们将创建它。确保在开始示例之前按照以下步骤创建文件：

1.  在任何文件夹中创建一个新的`.html`文件并打开它。

1.  创建一个`html`标签，并添加一个`head`HTML 元素作为子元素。在`head`HTML 元素内部，添加一个带有`src`属性定义为`http://unpkg.com/vue@next`的`script`HTML 元素：

```js
<html>  <head>
 <script src="https://unpkg.com/vue@next"></script>
 </head>
</html>
```

1.  作为`head`HTML 元素的同级，创建一个`body`HTML 元素。在`body`HTML 元素内部，添加一个带有属性`id`定义为`"app"`的`div`HTML 元素：

```js
<body>
 <div id="app">
 </div>
</body>
```

1.  最后，作为`div`HTML 元素的同级，创建一个带有空内容的`script`HTML 元素。这将是我们放置示例代码的地方：

```js
<script></script>
```

# 将您的 Vue 2 应用程序升级到 Vue 3

将您的项目从 Vue 2 升级到 Vue 3 有时可以自动完成，但在其他情况下，需要手动完成。这取决于您在应用程序中使用 Vue API 的深度。

对于由 Vue-CLI 制作和管理的项目，这个过程将变得更加顺畅，并且与使用自定义框架包装 CLI 的项目相比，将有更加简单的方法。

在这个食谱中，您将学习如何使用 Vue-CLI 升级您的应用程序以及如何手动升级项目和依赖项。

## 准备工作

这个食谱的先决条件如下：

+   Node.js 12+

所需的 Node.js 全局对象如下：

+   `@vue/cli`

+   `@vue/cli-service-global`

## 如何做...

为了将您的 Vue 2 项目升级到 Vue 3，您将需要将升级分为不同的部分。我们有框架本身的升级，然后是生态系统组件，比如`vue-router`和`vuex`，最后是将所有内容汇总的捆绑器。

框架升级带来了一些破坏性的变化。本章的*Vue 3 中的新内容*部分介绍了一些破坏性的变化，还有一些可能出现在更高级的 API 模式中。您必须手动更新并检查您的组件是否适用于框架的升级。

### 使用 Vue-CLI 升级项目

使用最新版本的 Vue-CLI，您将能够在项目中使用 Vue 3，并且您将能够将当前项目更新到 Vue 3。

要将 Vue-CLI 更新到最新版本，您需要打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows）并执行以下命令：

```js
> npm install @vue/cli-service@latest
```

### 手动升级项目

要手动升级项目，您首先需要将项目依赖项升级到它们的最新版本。您不能在 Vue 3 中使用旧版本的 Vue 生态系统插件。要做到这一点，请执行以下步骤：

1.  我们需要升级 Vue 框架、ESLint 插件（Vue 依赖的插件）和捆绑器的`vue-loader`。要升级它，您需要打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows）并执行以下命令：

```js
> npm install vue@next eslint-plugin-vue@next vue-loader@next
```

1.  我们需要将新的 Vue 单文件组件编译器作为项目的依赖项添加进去。要安装它，您需要打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows）并执行以下命令：

```js
> npm install @vue/compiler-sfc@latest
```

1.  如果您在项目中使用单元测试和`@vue/test-utils`包，您还需要升级此依赖项。要升级它，您需要打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows）并执行以下命令：

```js
> npm install @vue/test-utils@next @vue/server-test-utils@latest
```

1.  对于 Vue 生态系统插件，如果你使用`vue-router`，你也需要升级它。要升级它，你需要打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows），并执行以下命令：

```js
> npm install vue-router@next
```

1.  如果你的应用程序使用`vuex`作为默认状态管理，你也需要升级它。要升级它，你需要打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows），并执行以下命令：

```js
> npm install vuex@next
```

#### 更改起始文件

使用新版本的包，我们需要改变我们的起始文件。在使用 Vue-CLI 起始工具创建的 Vue 项目中，你会找到一个名为`main.js`或`main.ts`的文件。如果你使用 TypeScript，该文件位于`src`文件夹中。现在按照以下说明进行操作：

1.  打开项目中`src`文件夹中的`main.js`文件。在文件顶部，导入包的位置，你会看到以下代码：

```js
import Vue from 'vue';
```

我们需要将其更改为新的 Vue 暴露的 API 方法。为此，我们需要从 Vue 包中导入`createApp`，如下所示：

```js
import { createApp } from 'vue';
```

1.  从你的代码中移除全局 Vue 静态属性定义的`Vue.config.productionTip`。

1.  应该改变你的应用程序的挂载函数。旧的 API 看起来像这样：

```js
new Vue({
  router,
  store,
  render: (h) => h(App),
}).$mount('#app');
```

旧的 API 应该改为新的`createApp` API，如下所示：

```js
createApp(App)
  .use(router)
  .use(store)
  .mount('#app')
```

1.  打开你的`vuex`存储实例化文件（通常，该文件位于`src/store`，命名为`store.js`或`index.js`）。

1.  将存储的创建从实例化一个新的`vuex`类改为新的`createStore` API。`vuex` v3 类的实例化可能看起来像这样：

```js
import Vue from 'vue';
import Vuex from 'vuex';

Vue.use(Vuex);

export default new Vuex.Store({
  state: { /* ... */ },
  mutations: { /* ... */ },
  actions: { /* ... */ },
  getters: { /* ... */ },
  modules: { /* ... */ },
});
```

你需要用`createStore` API 替换它的内容，例如：

```js
import { createStore } from 'vuex';

export default createStore({
  state: { /* ... */ },
  mutations: { /* ... */ },
  actions: { /* ... */ },
  getters: { /* ... */ },
  modules: { /* ... */ },
});
```

1.  在`vue-router`生态系统中，我们需要用新的 API 替换路由器创建的旧 API。为此，打开路由器创建文件（在`src/router`文件夹中，通常命名为`router.js`或`index.js`）。

1.  最后，在创建文件中，用新的`createRouter` API 替换旧的`vue-router`类实例化。`vue-router` v3 类的实例化可能看起来像这样：

```js
import Vue from 'vue';
import VueRouter from 'vue-router';

Vue.use(VueRouter);

export default new VueRouter({
  routes: [{
    path: '/',
    name: 'HomePage',
    component: () => import('pages/home'),
  }]
});
```

你还需要用新的`createRouter`和`createWebHistory` API 替换`new VueRouter`的实例化，就像这个例子一样：

```js
import {
  createRouter,
  createWebHistory,
} from 'vue-router';

Vue.use(VueRouter);

export default createRouter({
  history: createWebHistory(),
  routes: [{
    path: '/',
    name: 'HomePage',
    component: () => import('pages/home'),
  }]
});
```

## 它是如何工作的...

在升级过程中，Vue 为我们提供了两种更新项目的方式。第一种方式是使用 Vue-CLI 插件，它试图自动化几乎所有升级所需的过程和更改。

第二种方法是手动升级项目。这种方法需要开发人员将所有依赖项升级到最新版本，安装新的单文件组件编译器`@vue/compiler-sfc`，并将 Vue 应用程序、路由器和存储的入口文件更改为新的 API。

在项目的起始结构更改后，开发人员需要检查组件，看看是否存在任何 Vue 3 破坏性更改，将组件重构为新的 Vue 3 API，并从 Vue 2 中删除已弃用的 API。

# 创建具有多个根元素的组件

在 Vue 3 中，可以创建具有多个根元素的组件，无需包装元素。这个选项也被称为片段。

在 React 中，这已经很久了，但在 Vue 中，您需要使用自定义的第三方插件，如`vue-fragment`（[`github.com/Thunberg087/vue-fragment`](https://github.com/Thunberg087/vue-fragment)）来使用此功能。

在这个教程中，您将学习如何创建一个具有多个根元素的组件，以及如何将其与`<template>`部分和`render`函数一起使用。

## 如何做...

在这个教程中，我们将创建两个多个根元素组件的示例，一个是使用`<template>`结构，另一个是使用`render`函数。为了做到这一点，这个教程将分为两部分。

### 使用<template>结构创建组件

为了在我们的示例中使用`<template>`结构，我们将使用 Vue 对象的`template`属性，我们可以将字符串或模板字符串作为值传递，这将由 Vue 脚本插值并呈现在屏幕上：

1.  使用“创建基本文件”部分的基本示例，创建一个名为`template.html`的新文件并打开它。

1.  在空的`<script>` HTML 元素中，通过对象解构`Vue`全局常量，创建常量`defineComponent`和`createApp`：

```js
const {
  defineComponent,   createApp, } = Vue;
```

1.  创建一个名为`component`的常量，定义为`defineComponent`方法，传递一个 JavaScript 对象作为参数，其中有三个属性：`data`、`methods`和`template`：

```js
const component = defineComponent({
  data: () => ({}),
  methods: {},
  template: `` });
```

1.  在`data`属性中，将其定义为一个单例函数，返回一个 JavaScript 对象，其中有一个名为`count`的属性，并且默认值为`0`：

```js
data: () => ({
  count: 0 }),
```

1.  在`methods`属性中，创建一个名为`addOne`的属性，这是一个函数，将通过`1`增加`count`的值：

```js
methods: {
  addOne() {
  this.count += 1;
  }, },
```

1.  在`template`属性中，在模板字符串中，创建一个带有标题的`h1` HTML 元素。然后，作为兄弟元素，创建一个带有绑定到`click`事件的事件监听器的`button` HTML 元素，当执行时触发`addOne`函数：

```js
template: `
  <h1>  This is a Vue 3 Root Element!  </h1>
 <button @click="addOne">  Pressed {{ count }} times.  </button> `
```

1.  最后，调用`createApp`函数，将`component`常量作为参数传递。然后，原型链连接`mount`函数，并将`div` HTML 元素的`id`属性`("#app")`作为函数的参数：

```js
createApp(component)
  .mount('#app');
```

### 使用渲染函数创建组件

为了在我们的示例中使用`<template>`结构，我们将使用 Vue 对象的`template`属性，我们可以将字符串或模板字符串作为值传递，Vue 脚本将对其进行插值处理并在屏幕上呈现：

1.  使用“创建基本文件”部分的基本示例，创建一个名为`render.html`的新文件并打开它。

1.  在空的`<script>` HTML 元素中，使用对象解构方法创建将要使用的函数的常量，从`Vue`全局常量中调用`defineComponent`、`h`和`createApp`方法：

```js
const {
  defineComponent,
 h,  createApp, } = Vue;
```

1.  创建一个名为`component`的常量，定义为`defineComponent`方法，传递一个 JavaScript 对象作为参数，该对象有三个属性：`data`、`methods`和`render`：

```js
const component = defineComponent({
  data: () => ({}),
  methods: {},
  render() {},  });
```

1.  在`data`属性中，将其定义为一个单例函数，返回一个具有名为`count`且默认值为`0`的 JavaScript 对象：

```js
data: () => ({
  count: 0 }),
```

1.  在`methods`属性中，创建一个名为`addOne`的属性，它是一个函数，将`count`的值增加`1`：

```js
methods: {
  addOne() {
  this.count += 1;
  }, },
```

1.  在`render`属性中，执行以下步骤：

+   创建一个名为`h1`的常量，并将其定义为`h`函数，将`'h1'`作为第一个参数传递，将要使用的标题作为第二个参数。

+   创建一个名为`button`的常量，它将是`h`函数，将`"button"`作为第一个参数传递，将一个具有`onClick`属性且值为`this.addOne`的 JavaScript 对象作为第二个参数传递，将`button`的内容作为第三个参数。

+   返回一个数组，第一个值为`h1`常量，第二个值为`button`常量：

```js
render() {
  const h1 = h('h1', 'This is a Vue 3 Root Element!');
  const button = h('button', {
  onClick: this.addOne,
  }, `Pressed ${this.count} times.`);    return [
  h1,
  button,
  ]; },
```

1.  最后，调用`createApp`函数，将`component`常量作为参数传递，原型链连接`mount`函数，并将`div` HTML 元素的`id`属性`("#app")`作为函数的参数：

```js
createApp(component)
  .mount('#app');
```

## 工作原理...

新的 Vue 组件创建 API 需要由一个函数`defineComponent`执行，并且作为参数传递的 JavaScript 对象几乎保持与 Vue 2 中的旧结构相同。在示例中，我们使用了相同的属性`data`、`render`、`methods`和`template`，这些属性都存在于 Vue 2 中。

在具有`<template>`结构的示例中，我们不必创建包装元素来封装应用程序组件的内容，并且可以直接在组件上有两个根元素。

在`render`函数示例中，发生了相同的行为，但最终示例使用了新的暴露的`h` API，它不再是`render`函数的参数。在按钮创建中出现了一个重大变化；我们必须在数据 JavaScript 对象内部使用`onClick`属性，而不是`on`属性和`click`方法。这是因为 Vue 3 的 VNode 的新数据结构。

# 使用属性继承创建组件。

自 Vue 2 以来，组件上已经可以使用属性继承，但在 Vue 3 中，属性继承变得更好，并且具有更可靠的 API 可用于组件中。

组件中的属性继承是一种模式，它可以更快地开发基于 HTML 元素的自定义组件（例如自定义输入、按钮、文本包装器或链接）。

在这个示例中，我们将创建一个具有属性继承的自定义输入组件，直接应用于`input` HTML 元素。

## 如何做...

在这里，我们将创建一个组件，该组件将在 DOM 树上的选定元素上具有完整的属性继承：

1.  使用*创建基本文件*部分的基本示例，创建一个名为`component.html`的新文件并打开它。

1.  在空的`<script>` HTML 元素中，使用对象解构方法创建将要使用的函数的常量，调用`Vue`全局常量的`defineComponent`和`createApp`方法：

```js
const {
  defineComponent,   createApp, } = Vue;
```

1.  创建一个名为`nameInput`的常量，定义为`defineComponent`方法，传递一个 JavaScript 对象作为参数，具有四个属性：`name`、`props`、`template`和`inheritAttrs`。然后，我们将`inheritAttrs`的值定义为`false`：

```js
const nameInput = defineComponent({
  name: 'NameInput',
  props: {},
  inheritAttrs: false,
  template: `` });
```

1.  在`props`属性中，添加一个名为`modelValue`的属性，并将其定义为`String`：

```js
props: {
  modelValue: String, },
```

1.  在模板属性中，在模板字符串内部，我们需要执行以下操作：

+   创建一个`label` HTML 元素，并将一个`input` HTML 元素作为子元素添加。

+   在`input` HTML 元素中，将`v-bind`指令定义为一个 JavaScript 对象，其中包含`this.$attrs`的解构值。

+   将变量属性`value`定义为接收到的 prop 的`modelValue`。

+   将`input`属性`type`设置为`"text"`。

+   将匿名函数添加到`change`事件监听器中，该函数接收一个`event`作为参数，然后发出一个名为`"update:modeValue"`的事件，载荷为`event.target.value`：

```js
template: ` <label>
 <input
 v-bind="{  ...$attrs,  }"
 :value="modelValue" type="text" @change="(event) => $emit('update:modelValue', 
                             event.target.value)"
  /> </label>`
```

1.  创建一个名为`appComponent`的常量，定义为`defineComponent`方法，传递一个 JavaScript 对象作为参数，其中包含两个属性，`data`和`template`：

```js
const component = defineComponent({
  data: () => ({}),
  template: ``,  });
```

1.  在`data`属性中，将其定义为一个单例函数，返回一个具有名为`name`的属性的 JavaScript 对象，其默认值为`''`：

```js
data: () => ({
  name: ''  }),
```

1.  在模板属性中，在模板字符串中，我们需要执行以下操作：

+   创建一个`NameInput`组件，其中`v-model`指令绑定到`name`数据属性。

+   创建一个带有值`"border:0; border-bottom: 2px solid red;"`的`style`属性。

+   创建一个带有值`"name-input"`的`data-test`属性：

```js
template: ` <name-input
 v-model="name" style="border:0; border-bottom: 2px solid red;"
 data-test="name-input" />`
```

1.  创建一个名为`app`的常量，并将其定义为`createApp`函数，将`component`常量作为参数传递。然后，调用`app.component`函数，将要注册的组件的名称作为第一个参数传递，组件作为第二个参数传递。最后，调用`app.mount`函数，将`"#app"`作为参数传递：

```js
const  app  =  createApp(component);  app.component('NameInput', nameInput); app.mount('#app');
```

## 工作原理...

在 Vue 3 中，为了创建一个组件，我们需要执行`defineComponent`函数，传递一个 JavaScript 对象作为参数。这个对象保持了几乎与 Vue 2 相同的组件声明结构。在示例中，我们使用了相同的属性，`data`，`methods`，`props`和`template`，这些属性都存在于 V2 中。

我们使用`inheritAttrs`属性来阻止将属性自动应用于组件上的所有元素，仅将其应用于具有`v-bind`指令和解构`this.$attrs`对象的元素。

要在 Vue 应用程序中注册组件，我们首先使用`createApp` API 创建应用程序，然后执行`app.component`函数在应用程序上全局注册组件，然后渲染我们的应用程序。

# 在 Vue 范围之外使用响应性和可观察 API

在 Vue 3 中，使用暴露的 API，我们可以在不需要创建 Vue 应用程序的情况下使用 Vue reactivity 和 reactive 变量。这使得后端和前端开发人员可以充分利用 Vue `reactivity` API。

在这个示例中，我们将使用`reactivity`和`watch` API 创建一个简单的 JavaScript 动画。

## 如何做...

在这里，我们将使用 Vue 暴露的`reactivity` API 创建一个应用程序，以在屏幕上呈现动画：

1.  使用“创建基本文件”部分中的基本示例，创建一个名为`reactivity.html`的新文件并打开它。

1.  在`<head>`标签中，添加一个新的`<meta>`标签，属性为`chartset`定义为`"utf-8"`：

```js
<meta charset="utf-8"/>
```

1.  在`<body>`标签中，删除`div#app` HTML 元素，并创建一个`div` HTML 元素，`id`定义为`marathon`，`style`属性定义为`"font-size: 50px;"`：

```js
<div
  id="marathon"
  style="font-size: 50px;" > </div>
```

1.  在空的`<script>` HTML 元素中，使用对象解构方法创建将要使用的函数的常量，调用`Vue`全局常量中的`reactivity`和`watch`方法：

```js
const {
  reactive,
  watch, } = Vue;
```

1.  创建一个名为`mod`的常量，定义为一个函数，接收两个参数`a`和`b`。然后返回一个算术运算，`a`模`b`：

```js
const mod = (a, b) => (a % b);
```

1.  创建一个名为`maxRoadLength`的常量，其值为`50`。然后，创建一个名为`competitor`的常量，其值为`reactivity`函数，传递一个 JavaScript 对象作为参数，其中`position`属性定义为`0`，`speed`定义为`1`：

```js
const maxRoadLength = 50; const competitor  = reactive({
  position: 0,
  speed: 1, });
```

1.  创建一个`watch`函数，传递一个匿名函数作为参数。在函数内部，执行以下操作：

+   创建一个名为`street`的常量，并将其定义为一个大小为`maxRoadLength`的`Array`，并用`*'_'*`*.*填充它。

+   创建一个名为`marathonEl`的常量，并将其定义为 HTML DOM 节点`#marathon`。

+   选择数组索引中`competitor.position`的`street`元素，并将其定义为`*"![](img/c8b07311-36a4-4df3-98fd-3b68200deed3.png)"*`，如果`competitor.position`是偶数，或者如果数字是奇数，则定义为`*"![](img/562ed724-a630-4193-a9c6-4e143a9690e2.png)"*`。

+   将`marathonEl.innertHTML`定义为`*""*`和`street.reverse().join('')`：

本示例中使用的表情符号是**跑步的人**和**行走的人**。表情符号图像可能因您的操作系统而异。本示例中呈现的图像是苹果操作系统的表情符号。

```js
watch(() => {
  const street = Array(maxRoadLength).fill('_');
  const marathonEl = document.getElementById('marathon');
  street[competitor.position] = (competitor.position % 2 === 1)
  ? '![](img/c8b07311-36a4-4df3-98fd-3b68200deed3.png)'
  : '![](img/562ed724-a630-4193-a9c6-4e143a9690e2.png)';    marathonEl.innerHTML = '';
  marathonEl.innerHTML = street.reverse().join(''); });
```

1.  创建一个`setInterval`函数，将一个匿名函数作为参数传递。在函数内部，将`competitor.position`定义为`mod`函数，将`competitor.position`加上`competitor.speed`作为第一个参数，将`maxRoadLength`作为第二个参数：

```js
setInterval(() => {
 competitor.position = mod(competitor.position +competitor.speed, 
    maxRoadLength) }, 100);
```

## 它是如何工作的...

使用 Vue 暴露的`reactive`和`watch`API，我们能够创建一个具有 Vue 框架中的响应性的应用程序，但不使用 Vue 应用程序。

首先，我们创建了一个响应式对象`competitor`，它的工作方式与 Vue 的`data`属性相同。然后，我们创建了一个`watch`函数，它的工作方式与`watch`属性相同，但是作为匿名函数使用。在`watch`函数中，我们为竞争者开辟了一条跑道，并创建了一个简单的动画，使用两个不同的表情符号，根据在道路上的位置进行更改，以模拟屏幕上的动画。

最后，我们在屏幕上打印了当前的跑步者，并创建了一个`setInterval`函数，每 100 毫秒改变竞争者在道路上的位置：

![](img/aa026d03-a44e-4d9b-ac15-3246bccda09f.png)

# 使用组合 API 创建组件

组合 API 是一种编写 Vue 组件的新方法，基于使用函数来组合组件，它使代码的组织和可重用性更好。

这种方法受到 React Hooks 的启发，并引入了创建特殊函数来组合应用程序的技术，这些函数可以在不需要在 Vue 应用程序内部的情况下共享，因为使用了暴露的 Vue API。

在这个示例中，我们将学习如何创建一个外部函数，用于获取用户的地理位置并在屏幕上显示这些数据，使用组合 API。

## 如何做...

在这里，我们将使用组合 API 创建一个组件，该组件将获取用户的 GPS 位置并在屏幕上显示该信息：

1.  使用“创建基本文件”部分的基本示例，创建一个名为`component.html`的新文件并打开它。

1.  在空的`<script>` HTML 元素中，使用对象解构方法创建将要使用的函数的常量，从`Vue`全局常量中调用`createApp`、`defineComponent`、`setup`、`ref`、`onMounted`和`onUnmounted`方法：

```js
const {
  createApp,
  defineComponent,
  setup,
  ref,
  onMounted,
  onUnmounted, } = Vue;
```

1.  创建一个`fetchLocation`函数，在其中创建一个名为`watcher`的`let`变量。然后，创建一个名为`geoLocation`的常量，并将其定义为`navigator.geolocation`。接下来，创建一个名为`gpsTime`的常量，并将其定义为`ref`函数，将`Date.now()`函数作为参数传递。最后，创建一个名为`coordinates`的常量，并将其定义为`ref`函数，将一个 JavaScript 对象作为参数传递，其中的属性`accuracy`、`latitude`、`longitude`、`altitude`、`altitudeAccuracy`、`heading`和`speed`都定义为`0`：

```js
function fetchLocation() {
  let watcher;
  const geoLocation = navigator.geolocation;
  const gpsTime = ref(Date.now());
  const coordinates = ref({
  accuracy: 0,
  latitude: 0,
  longitude: 0,
  altitude: 0,
  altitudeAccuracy: 0,
  heading: 0,
  speed: 0,
  }); }
```

1.  然后，在`fetchLocation`函数内部，在常量创建之后，创建一个名为`setPosition`的函数，带有一个名为`payload`的参数。在函数内部，将`gpsTime.value`定义为`payload.timestamp`参数，将`coordinates.value`定义为`payload.coords`参数：

```js
function setPosition(payload) {
  gpsTime.value = payload.timestamp
  coordinates.value = payload.coords
}  
```

1.  在创建`setPosition`函数之后，调用`onMounted`函数，将一个匿名函数作为参数传递。在函数内部，检查浏览器是否可用`geoLocation` API，并将`watcher`定义为`geoLocation.watchPostion`函数，将`setPosition`函数作为参数传递：

```js
onMounted(() => {
  if (geoLocation) watcher = geoLocation.watchPosition(setPosition); });
```

1.  调用`onMounted`函数后，创建一个`onUnmounted`函数，将一个匿名函数作为参数传递。在函数内部，检查`watcher`是否已定义，然后执行`geoLocation.clearWatch`函数，将`watcher`作为参数传递：

```js
onUnmounted(() => {
  if (watcher) geoLocation.clearWatch(watcher); });
```

1.  最后，在`fetchLocation`函数中，返回一个 JavaScript 对象，并将`coordinates`和`gpsTime`常量作为属性/值传递：

```js
return {
  coordinates,
  gpsTime, };
```

1.  创建一个名为`appComponent`的常量，并将其定义为`defineComponent`函数，将一个具有`setup`和`template`属性的 JavaScript 对象作为参数传递：

```js
const appComponent = defineComponent({
  setup() {},
  template: `` });
```

1.  在`setup`函数中，创建一个常量，这是一个对象解构，包括`fetchLocation`函数的`coordinates`和`gpsTime`属性：

```js
setup() {
  const {
  coordinates,
  gpsTime,
  } = fetchLocation(); }
```

1.  在`setup`函数内部，创建另一个名为`formatOptions`的常量，并将其定义为一个具有`year`、`month`、`day`、`hour`和`minute`属性的 JavaScript 对象，其值均为`'numeric'`。然后，将属性`hour12`定义为`true`：

```js
const formatOptions = {
  year: 'numeric',
  month: 'numeric',
  day: 'numeric',
  hour: 'numeric',
  minute: 'numeric',
  hour12: true,
  };
```

1.  在创建`formatOptions`常量之后，创建一个名为`formatDate`的常量，并将其定义为一个函数，该函数接收一个名为`date`的参数。然后，返回一个新的`Intl.DateTimeFormat`函数，将`navigator.language`作为第一个参数，将`formatOption`常量作为第二个参数。然后，原型链连接`format`函数，传递`date`参数：

```js
const formatDate = (date) => (new 
  Intl.DateTimeFormat(navigator.language, 
     formatOptions).format(date)); 
```

1.  最后，在`setup`函数的末尾，返回一个 JavaScript 对象，其属性定义为`coordinates`、`gpsTime`和`formatDate`常量：

```js
return {
  coordinates,
  gpsTime,
  formatDate };
```

1.  在`template`属性中，进行以下操作：

+   创建一个带有文本“我的地理位置在{{ formatDate(new Date(gpsTime) }}”的`h1` HTML 元素。

+   创建一个`ul` HTML 元素，并添加三个`li` HTML 元素作为子元素。

+   在第一个子元素中，添加文本“纬度：{{ coordinates.latitude }}”。

+   在第二个子元素中，添加文本“经度：{{ coordinates.longitude }}”。

+   在第三个子元素中，添加文本“海拔：{{ coordinates.altitude }}”：

```js
template: `
  <h1>My Geo Position at {{formatDate(new 
                          Date(gpsTime))}}</h1>
 <ul>
 <li>Latitude: {{ coordinates.latitude }}</li>
 <li>Longitude: {{ coordinates.longitude }}</li>
 <li>Altitude: {{ coordinates.altitude  }}</li>
 </ul> `
```

1.  最后，调用`createApp`函数，传递`appComponent`常量作为参数。然后，原型链连接`mount`函数，并将`div` HTML 元素的`id`属性`("#app")`作为函数的参数：

```js
createApp(appComponent)
  .mount('#app');
```

## 它是如何工作的...

在这个示例中，首先我们导入了暴露的 API - `createApp`、`defineComponent`、`setup`、`ref`、`onMounted`和`onUnmounted` - 作为常量，我们将使用它们来创建组件。然后，我们创建了`fetchLocation`函数，它负责获取用户的地理位置数据，并将其作为响应式数据返回，当用户更改位置时可以自动更新。

能够获取用户 GPS 位置是因为现代浏览器上存在的`navigator.geolocation` API，它能够获取用户当前的 GPS 位置。利用浏览器提供的数据，我们能够用它来定义由 Vue `ref` API 创建的变量。

我们使用 Vue 对象声明的`setup`函数创建了组件，因此渲染知道我们正在使用新的组合 API 作为组件创建方法。在`setup`函数内部，我们导入了`fetchLocation`函数的动态变量，并创建了一个方法，用于在模板上使用日期格式化。

然后我们返回导入的变量和过滤器，以便它们可以在模板部分中使用。在模板部分中，我们创建了一个标题，添加了最后一次 GPS 位置的时间，使用过滤器进行格式化，并创建了用户的纬度、经度和海拔的列表。

最后，我们使用`createApp`公开的 API 创建了应用程序，并挂载了 Vue 应用程序。

## 另请参阅

您可以在[`developer.mozilla.org/en-US/docs/Web/API/Navigator/geolocation`](https://developer.mozilla.org/en-US/docs/Web/API/Navigator/geolocation)找到有关`Navigator.geolocation`的更多信息。

您可以在[`developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl/DateTimeFormat`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl/DateTimeFormat)找到有关`Intl.DateTimeFormat`的更多信息。
