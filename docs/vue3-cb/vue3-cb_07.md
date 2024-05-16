# 第七章：使用 Vuex 管理应用程序状态

在兄弟组件之间传输数据可能非常容易，但想象一下让一系列组件对任何数据变化做出反应。你需要在事件总线中触发一个事件，或者通过所有父组件发送事件，直到它到达事件链的顶部，然后再一路发送到所需的组件；这个过程可能非常繁琐和痛苦。如果你正在开发一个大型应用程序，这个过程是不可持续的。

Flux 库是为了帮助这个过程而开发的，其想法是将反应性带出组件边界，因为 Vuex 能够维护数据的唯一真相来源，并且同时也是你制定业务规则的地方。

在这一章中，我们将学习如何使用 Vuex，开发我们的存储，将其应用到我们的组件，并对其进行命名空间，以便我们可以在同一个存储中拥有不同的 Vuex 模块。

在这一章中，我们将涵盖以下的配方：

+   创建一个简单的 Vuex 存储

+   创建和理解 Vuex 状态

+   创建和理解 Vuex 变化

+   创建和理解 Vuex 操作

+   创建和理解 Vuex 获取器

+   使用 Vuex 创建一个动态组件

+   为开发添加热模块重新加载

+   创建一个 Vuex 模块

# 技术要求

在这一章中，我们将使用**Node.js**和**Vue-CLI**。

注意，Windows 用户，你需要安装一个名为`windows-build-tools`的 NPM 包，以便安装以下所需的包。要做到这一点，以管理员身份打开 PowerShell 并执行以下命令：

`> npm install -g windows-build-tools`

要安装 Vue-CLI，你需要打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows）并执行以下命令：

```js
> npm install -g @vue/cli @vue/cli-service-global
```

# 创建一个简单的 Vuex 存储

在应用程序中创建一个唯一的真相来源可以让你简化数据流，使数据的反应性流向另一个视角，你不再受限于父子关系。数据现在可以存储在一个地方，每个人都可以获取或请求数据。

在这个配方中，我们将学习如何安装 Vuex 库并创建我们的第一个单一存储，以及如何使用反应式操作和数据获取器来操作它。

## 准备工作

这个配方的先决条件如下：

+   Node.js 12+

所需的 Node.js 全局对象如下：

+   `@vue/cli`

+   `@vue/cli-service-global`

## 如何做...

要创建一个 Vue-CLI 项目，请按照以下步骤进行：

1.  我们需要在终端（macOS 或 Linux）或命令提示符/PowerShell（Windows）中执行以下命令：

```js
> vue create initial-vuex
```

1.  CLI 会询问一些问题，这些问题将有助于创建项目。您可以使用箭头键进行导航，使用*Enter*键继续，使用*Spacebar*选择选项。

1.  有两种方法可以启动新项目。默认方法是基本的`babel`和`eslint`项目，没有任何插件或配置，还有`手动`模式，您可以选择更多模式、插件、代码检查工具和选项。我们将选择`手动`：

```js
? Please pick a preset: (Use arrow keys)
 default (babel, eslint)
❯ Manually select features
```

1.  现在我们被问及项目中需要的功能。这些功能包括一些 Vue 插件，如 Vuex 或 Router（`Vue-Router`），测试工具，代码检查工具等。选择`Babel`，`Router`，`Vuex`和`Linter / Formatter`：

```js
?  Check the features needed for your project: (Use arrow keys) ❯ Babel
 TypeScript Progressive Web App (PWA) Support ❯ Router ❯ Vuex
  CSS Pre-processors ❯ Linter / Formatter
 Unit Testing E2E Testing
```

1.  继续此过程，选择一个代码检查工具和格式化工具。在我们的情况下，我们将选择`ESLint + Airbnb`配置：

```js
?  Pick a linter / formatter config: (Use arrow keys) ESLint with error prevention only ❯ **ESLint + Airbnb config** ESLint + Standard config 
  ESLint + Prettier
```

1.  设置了代码检查规则后，我们需要定义它们何时应用于您的代码。它们可以在“保存时”应用，也可以在“提交时”修复：

```js
?  Pick additional lint features: (Use arrow keys)  Lint on save ❯ Lint and fix on commit
```

1.  在定义了所有这些插件、代码检查工具和处理器之后，我们需要选择设置和配置的存储位置。最好的存储位置是专用文件，但也可以将它们存储在`package.json`文件中：

```js
?  Where do you prefer placing config for Babel, ESLint, etc.?  (Use 
  arrow keys) ❯ **In dedicated config files****In package.json** 
```

1.  现在您可以选择是否将此选择作为将来项目的预设，这样您就不需要再次重新选择所有内容：

```js
?  Save this as a preset for future projects?  (y/N) n
```

我们的步骤将分为两部分：

+   创建 store

+   使用 Vuex 创建响应式组件

让我们开始吧。

### 创建 store

现在您已经有了包含 Vuex 库的项目，我们需要创建我们的第一个 store。在接下来的步骤中，我们将创建 Vuex store：

1.  打开`src/store`文件夹中的`index.js`。

1.  在`state`属性中，添加一个名为`counter`的新键，并将值设置为`0`：

```js
state: {
  counter: 0,
},
```

1.  在`mutations`属性中，添加两个新函数，`increment`和`decrement`。这两个函数都将有一个`state`参数，这是当前的 Vuex`state`对象。`increment`函数将`counter`增加`1`，而`decrement`函数将`counter`减少`1`：

```js
mutations: {
  increment: (state) => {
    state.counter += 1;
  },
  decrement: (state) => {
    state.counter -= 1;
  },
},
```

1.  最后，在`actions`属性中，添加两个新函数，`increment`和`decrement`。这两个函数都将有一个解构参数`commit`，它是调用 Vuex mutation 的函数。在每个函数中，我们将执行`commit`函数，将当前函数的名称作为字符串参数传递：

```js
actions: {
 increment({ commit }) {
 commit('increment');
 },
 decrement({ commit }) {
 commit('decrement');
 },
},  
```

### 使用 Vuex 创建响应式组件

现在您已经定义了您的 Vuex 存储，您需要与之交互。我们将创建一个响应式组件，它将在屏幕上显示当前状态的`counter`，并显示两个按钮，一个用于增加`counter`，另一个用于减少`counter`。

#### 单文件组件<script>部分

在这里，我们将编写单文件组件的`<script>`部分：

1.  从`src`文件夹中打开`App.vue`文件。

1.  在文件中创建`<script>`部分，使用`export default`对象：

```js
<script>
  export default {}; </script>
```

1.  在新创建的对象中，添加 Vue `computed`属性，属性名为`counter`。在这个属性中，我们需要返回当前的`$store.state.counter`：

```js
computed: {
  counter() {
  return this.$store.state.counter;
  }, },
```

1.  最后，在 Vue `methods`属性中创建两个函数，`increment`和`decrement`。这两个函数都将执行一个带有函数名称作为字符串参数的`$store.dispatch`：

```js
methods: {
  increment() {
  this.$store.dispatch('increment');
  },
  decrement() {
  this.$store.dispatch('decrement');
  }, },
```

#### 单文件组件<template>部分

让我们编写单文件组件的`<template>`部分：

1.  在`src`文件夹中打开`App.vue`文件。

1.  在`<template>`部分中，删除`div#app`内的所有内容。

1.  创建一个包含计数器变量的`h1`HTML 元素。

1.  创建一个带有`@click`指令的事件监听器的按钮，调用`increment`函数，并将`+`作为标签：

```js
<button @click="increment">+</button>
```

1.  创建一个带有`@click`指令的事件监听器的按钮，调用`decrement`函数，并将`-`作为标签：

```js
<button @click="decrement">-</button>
```

要运行服务器并查看您的组件，您需要打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows）并执行以下命令：

```js
> npm run serve
```

这是您的组件呈现并运行的方式：

![](img/0846f103-aada-46b7-a0a2-92e7481f19b5.png)

## 它是如何工作的...

当您声明 Vuex 存储时，您需要创建三个主要属性，`state`，`mutations`和`actions`。这些属性作为一个单一的结构，通过注入的`$store`原型或导出的`store`变量绑定到 Vue 应用程序。

`state`是一个集中的对象，保存您的信息并使其可供`mutation`、`actions`或组件使用。改变`state`始终需要通过`mutation`执行的同步函数。

`mutation`是一个同步函数，可以改变`state`并且是可追踪的，因此在开发时，可以通过 Vuex 存储中执行的所有`mutations`进行时间旅行。

`action`是一个异步函数，可用于保存业务逻辑、API 调用、分派其他`actions`和执行`mutations`。这些函数是 Vuex 存储中任何更改的常见入口点。

Vuex 存储的简单表示可以在此图表中看到：

![](img/20737403-4144-48e7-9ee0-6b70a4df9851.png)

## 另请参阅

您可以在[`vuex.vuejs.org/`](https://vuex.vuejs.org/)找到有关 Vuex 的更多信息。

# 创建和理解 Vuex 状态

Vuex 状态似乎很容易理解。但是，随着数据变得更加深入和嵌套，其复杂性和可维护性可能变得更加复杂。

在本配方中，我们将学习如何创建一个 Vuex 状态，该状态可以在**渐进式 Web 应用程序（PWA）**/**单页面应用程序（SPA）**和**服务器端渲染（SSR）**的情景中使用，而无需任何问题。

## 准备就绪

本配方的先决条件如下：

+   Node.js 12+

所需的 Node.js 全局对象如下：

+   `@vue/cli`

+   `@vue/cli-service-global`

## 如何做...

要开始我们的组件，我们将使用在第六章中使用的 Vue-CLI 的 Vue 项目，或者我们可以开始一个新的项目。

要启动一个新项目，打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows），并执行以下命令：

```js
> vue create vuex-store
```

选择手动功能，根据'*如何做...*'部分的指示，将`Router`和`Vuex`添加为必需功能。

我们的配方将分为两部分：

+   通过`vue ui`添加 Vuex

+   创建`Vuex`状态

让我们开始吧。

### 通过 vue ui 添加 Vuex

当导入通过 Vue-CLI 创建的旧项目时，可以通过`vue ui`界面自动添加 Vuex，而无需任何努力。我们将学习如何向旧项目添加 Vuex 库，以便继续开发该项目。

在接下来的步骤中，我们将使用`vue ui`界面添加 Vuex。

1.  在项目文件夹中，通过在终端（macOS 或 Linux）或命令提示符/PowerShell（Windows）上执行以下命令来打开`vue ui`：

```js
> vue ui
```

1.  选择你正在工作的正确项目。在右侧边栏中，点击插件菜单图标：

![](img/9795f7d3-5585-42ff-b0d1-8d4ea162b369.png)

1.  在插件页面的顶部工具栏上，点击“添加 vuex”按钮。这将触发一个弹出模态窗口，然后点击“继续”按钮完成在应用程序上安装 Vuex：

![](img/1494d414-1e39-402d-b8aa-1de4b5fca347.png)

1.  将 Vuex 添加到我们的应用程序将改变应用程序的结构。首先，我们会注意到`src`文件夹中有一个名为`store`的新文件夹，在`main.js`文件中，它被添加到了导入和在 Vue 应用程序中注入`store`：

```js
import './server/server'; import Vue from 'vue'; import App from './App.vue'; import Vuesax from 'vuesax'; import './style.css'; import router from './router' import store from './store'   Vue.use(Vuesax);   Vue.config.productionTip = false;   new Vue({
  router,
  store,
  render: h => h(App) }).$mount('#app');
```

### 创建 Vuex 状态

为了将数据保存在 Vuex 中，您需要有一个初始状态，在用户进入您的应用程序时加载并定义为默认状态。在这里，我们将学习如何创建 Vuex 状态并将其用作单例，以便 Vuex 可以在 SPA 和 SSR 页面中使用：

现在我们将创建一个可以在 SSR 和 SPA 中使用的 Vuex 存储：

1.  在`src/store`文件夹中，创建一个名为`user`的新文件夹，在这个文件夹里创建一个名为`state.js`的新文件。

1.  创建一个新的`generateState`函数。这个函数将返回一个 JavaScript 对象，有三个主要属性，`data`，`loading`和`error`。`data`属性将是一个 JavaScript 对象，其中有一个名为`usersList`的属性，默认为空数组，以及一个名为`userData`的属性，其中包含用户的默认对象。`loading`属性将默认设置为布尔值`false`，`error`将有一个默认值初始化为`null`：

```js
const generateState = () => ({
  data: {
  usersList: [],
  userData: {
  name: '',
  email: '',
  birthday: '',
  country: '',
  phone: '',
  },
  },
  loading: false,
  error: null, });
```

1.  创建函数后，我们将在文件末尾创建一个`export default`对象，它将是一个 JavaScript 对象，并且我们将解构`generateState`函数的返回值：

```js
export default { ...generateState() };
```

1.  在`user`文件夹中创建一个名为`index.js`的新文件并打开它。

1.  导入新创建的`state`：

```js
import state from './state';
```

1.  在文件末尾，创建一个`export default`文件作为 JavaScript 对象。在这个对象中，我们将添加导入的`state`：

```js
export default {
  state, };  
```

1.  打开`src/store`文件夹中的`index.js`文件。

1.  从`user`文件夹中导入`index.js`文件：

```js
import Vue from 'vue'; import Vuex from 'vuex'; import UserStore from './user';
```

1.  在创建一个新的 Vuex store 的`export default`函数中，我们将删除其中的所有属性，并将导入的`UserStore`解构对象放入`Vuex.Store`参数中：

```js
export default new Vuex.Store({
  ...UserStore, })
```

## 工作原理...

当使用`vue ui`将 Vuex 作为插件添加时，`vue ui`将自动添加所需的文件，并导入所有需要的内容。这是创建 Vuex `store`的初始阶段。

首先是创建一个专门管理`state`的文件，我们可以使用它来分离`store`中状态的开始过程以及如何初始化状态。

在这种情况下，我们使用一个函数来生成每次调用时都会生成一个全新的`state`。这是一个很好的做法，因为在 SSR 环境中，服务器的`state`始终是相同的，我们需要为每个新连接创建一个新的`state`。

在创建`state`之后，我们需要创建一个默认文件来导出将在`user`文件夹中创建的 Vuex 文件。这个文件是对将在文件夹中创建的所有文件（`state`，`actions`，`mutation`和`getters`）的简单导入。导入后，我们导出一个带有所需的 Vuex 属性名称的对象，`state`，`actions`，`mutations`和`getters`。

最后，在 Vuex 的`store`中，我们导入了一个文件，将所有内容聚合并解构到我们的 store 中进行初始化。

## 还有更多...

`Vuex` state 是应用程序中的唯一数据源，它就像一个全局数据管理器，不应直接更改。这是因为我们需要防止数据的同时变异。为了避免这种情况，我们总是需要通过 mutations 来改变我们的 state，因为这些函数是同步的，并由 Vuex 控制。

## 另请参阅

在[`vuex.vuejs.org/guide/state.html`](https://vuex.vuejs.org/guide/state.html)找到有关 Vuex state 的更多信息。

# 创建和理解 Vuex mutations

当 Vuex 发生变化时，我们需要一种以异步形式执行这种变化并跟踪它的方式，以便在第一个变化完成之前不会执行另一个变化。

在这种情况下，我们需要 mutations，这些是仅负责改变应用程序状态的函数。

在这个示例中，我们将学习如何创建 Vuex mutations 以及最佳实践。

## 准备工作

此示例的先决条件如下：

+   Node.js 12+

所需的 Node.js 全局对象如下：

+   `@vue/cli`

+   `@vue/cli-service-global`

## 如何做...

要开始我们的组件，我们将使用在'*创建和理解 Vuex 状态*'食谱中使用的 Vue 项目与 Vue-CLI，或者我们可以开始一个新的。

要开始一个新的，打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows），并执行以下命令：

```js
> vue create vuex-store
```

选择手动功能，根据'*如何做...*'部分的指示，添加`Router`和`Vuex`作为必需功能。

现在我们创建一个 Vuex mutation 和基本类型的 mutation：

1.  在`src/store`文件夹内的`user`文件夹中创建一个名为`types.js`的新文件，并打开它。

1.  在这个文件中，我们将创建一个`export default`的 JavaScript 对象，其中包含一组键，这些键将是我们 mutations 的名称。这些键将是`LOADING`、`ERROR`、`SET_USER_LIST`、`SET_USER_DATA`、`UPDATE_USER`和`REMOVE_USER`：

```js
export default {
  LOADING: 'LOADING',
  ERROR: 'ERROR',
  SET_USER_LIST: 'SET_USER_LIST',
  SET_USER_DATA: 'SET_USER_DATA',
  UPDATE_USER: 'UPDATE_USER',
  REMOVE_USER: 'REMOVE_USER', }
```

1.  在`user`文件夹中创建一个名为`mutations.js`的新文件，并打开它。

1.  导入新创建的`types.js`文件：

```js
import MT from './types';
```

1.  创建一个名为`setLoading`的新函数，它将接收 Vuex `state`作为参数，并在执行时将状态的 loading 属性定义为`true`。

```js
const setLoading = state => {
  state.loading = true; };
```

1.  创建一个名为`setError`的新函数，它将接收 Vuex `state`和`payload`作为参数。这个函数将把`state`的`loading`属性设置为`false`，将`error`属性设置为接收到的`payload`参数：

```js
const setError = (state, payload) => {
  state.loading = false;
  state.error = payload; };
```

1.  创建一个名为`setUserList`的新函数，它将接收 Vuex `state`和`payload`作为参数。这个函数将把`state.data`的`usersList`属性定义为接收到的`payload`参数，将`state`的`loading`属性设置为`false`，将`error`属性设置为`null`：

```js
const setUserList = (state, payload) => {
  state.data.usersList = payload;
  state.loading = false;
  state.error = null; };
```

1.  创建一个名为`setUserData`的新函数，它将接收 Vuex `state`和`payload`作为参数。这个函数将把`state.data`的`userData`属性定义为接收到的`payload`参数，将`state`的`loading`属性设置为`false`，将`error`属性设置为`null`：

```js
const setUserData = (state, payload) => {
  state.data.userData = payload;
  state.loading = false;
  state.error = null; };
```

1.  创建一个名为`updateUser`的新函数，它将接收 Vuex `state`和`payload`作为参数。这个函数将更新`state.data`的`usersList`属性中的用户数据，将`state`的`loading`属性定义为`false`，将`error`属性定义为`null`：

```js
const updateUser = (state, payload) => {
  const userIndex = state.data.usersList.findIndex(u => u.id === 
     payload.id);
  if (userIndex > -1) {
 state.data.usersList[userIndex] = payload;
  }
 state.loading = false;
  state.error = null; };
```

1.  创建一个名为`removeUser`的新函数，它将接收 Vuex `state`和`payload`作为参数。这个函数将从`state.data`的`usersList`属性中删除用户数据，将`state`的`loading`属性定义为`false`，将`error`属性定义为`null`：

```js
const removeUser = (state, payload) => {
  const userIndex = state.data.usersList.findIndex(u => u.id === 
     payload);
  if (userIndex > -1) {
 state.data.usersList.splice(userIndex, 1);
  }
 state.loading = false;
  state.error = null; };
```

1.  最后，创建一个`export default`对象，其中键是我们在`types.js`文件中创建的类型，并将每个键定义为我们创建的函数：

```js
export default {
 [MT.LOADING]: setLoading,
 [MT.ERROR]: setError,
 [MT.SET_USER_LIST]: setUserList,
 [MT.SET_USER_DATA]: setUserData,
 [MT.UPDATE_USER]: updateUser,
 [MT.REMOVE_USER]: removeUser, }
```

1.  打开`user`文件夹中的`index.js`文件。

1.  导入新创建的`mutations.js`文件，并将其添加到`export default` JavaScript 对象中：

```js
import state from './state';
import mutations from './mutations';

export default {
  state,
  mutations,
};
```

## 它是如何工作的...

每个`mutation`都是一个将作为`commit`调用的函数，并且在 Vuex 存储中具有*标识符*。这个标识符是导出的 JavaScript 对象中的`mutation`键。在这个示例中，我们创建了一个文件，将所有标识符作为对象值保存，以便在我们的代码中作为常量使用。

这种模式有助于我们开发需要知道每个`mutation`名称的 Vuex `actions`。

在导出`mutation` JavaScript 对象时，我们使用常量作为键，相应的函数作为其值，这样 Vuex 存储在调用时可以执行正确的函数。

## 另请参阅

在[`vuex.vuejs.org/guide/mutations.html`](https://vuex.vuejs.org/guide/mutations.html)找到有关 Vuex mutations 的更多信息。

# 创建和理解 Vuex getters

从`Vuex`中访问数据可以通过状态本身完成，这可能非常危险，或者通过 getters 完成。Getters 就像是可以预处理并传递数据而不触及或干扰 Vuex 存储状态的数据。

Getter 背后的整个理念是可以编写自定义函数，可以在需要时从状态中提取数据的单一位置，以便获得所需的数据。

在这个示例中，我们将学习如何创建一个 Vuex getter 和一个可以作为高阶函数使用的动态 getter。

## 准备工作

此示例的先决条件如下：

+   Node.js 12+

所需的 Node.js 全局对象如下：

+   `@vue/cli`

+   `@vue/cli-service-global`

## 如何做...

要启动我们的组件，我们将使用在“创建和理解 Vuex mutations”示例中使用的 Vue 项目与 Vue-CLI，或者我们可以启动一个新的项目。

要启动一个新的项目，打开 Terminal（macOS 或 Linux）或 Command Prompt/PowerShell（Windows）并执行以下命令：

```js
> vue create vuex-store
```

选择手动功能，并根据“如何做...”部分中的指示添加 Router 和`Vuex`作为需要的功能。

在接下来的步骤中，我们将创建 Vuex 的 getter：

1.  在`src/store/user`文件夹中创建一个名为`getters.js`的新文件。

1.  创建一个名为`getUsersList`的新函数，并返回`state.data.usersList`属性：

```js
function getUsersList(state) {
  return state.data.usersList;
}
```

在`getter`函数中，函数将始终接收到的第一个参数是 Vuex `store`的当前`state`。

1.  创建一个名为`getUserData`的新函数，并返回`state.data.userData`属性：

```js
function getUserData(state) {
  return state.data.userData; }
```

1.  创建一个名为`getUserById`的新函数，并返回另一个函数，该函数接收`userId`作为参数。这个返回函数将返回与接收到的`userId`相匹配的`state.data.usersList`的搜索结果：

```js
function getUserById(state) {
  return (userId) => {
 return state.data.usersList.find(u => u.id === userId);
  } }
```

1.  创建一个名为`isLoading`的新函数，并返回`state.loading`属性：

```js
function isLoading(state) {
  return state.loading;
}
```

1.  创建一个名为`hasError`的新函数，并返回`state.error`属性：

```js
function hasError(state) {
  return state.error;
}
```

1.  最后，创建一个带有所有创建的函数作为属性的`export default` JavaScript 对象：

```js
export default {
  getUsersList,
  getUserData,
  getUserById,
  isLoading,
  hasError, };  
```

1.  在`src/store/user`文件夹中打开`index.js`文件。

1.  导入新创建的`getters.js`文件，并将其添加到默认导出的 JavaScript 对象中：

```js
import state from './state';
import mutations from './mutations';
import getters from './getters';

export default {
  state,
  mutations,
  getters,
};
```

## 它是如何工作的...

Getter 就像是从对象中获取的 GET 函数，是静态缓存函数-只有在`state`发生变化时才会改变返回值。但是，如果将返回值作为高阶函数添加，就可以赋予它更多的功能，使用更复杂的算法并提供特定的数据。

在这个示例中，我们创建了两种类型的 getter：最基本的，返回简单数据，以及高阶函数，需要作为函数调用以检索所需的值。

## 还有更多...

使用带有业务逻辑的 getter 是收集更多状态数据的好方法。这是一个很好的模式，因为在较大的项目中，它可以帮助其他开发人员更好地理解每个 GET 函数中发生了什么以及它在幕后是如何工作的。

您始终需要记住，getter 是同步函数，并对状态变化具有反应性，因此 getter 上的数据是被记忆和缓存的，直到单一的真相源接收到提交并更改它。

## 参见

您可以在[`vuex.vuejs.org/guide/getters.html`](https://vuex.vuejs.org/guide/getters.html)找到有关 Vuex getters 的更多信息。

# 创建和理解 Vuex actions

你已经准备好了所有的状态，你的数据集，现在你需要从外部来源获取新数据或者在你的应用程序中改变这些数据。这就是操作发挥作用的地方。

操作负责在应用程序和外部世界之间的通信中编排过程。控制数据何时需要在状态上进行变异并返回给操作的调用者。

通常，操作是通过组件或视图进行调度，但有时操作可以调度另一个操作，以在应用程序中创建一系列操作。

在这个配方中，我们将学习如何在我们的应用程序中创建所需的操作，以定义用户列表，更新用户和删除用户。

## 准备工作

这个配方的先决条件如下：

+   Node.js 12+

所需的 Node.js 全局对象如下：

+   `@vue/cli`

+   `@vue/cli-service-global`

## 如何做...

要启动我们的组件，我们将使用在“*创建和理解 Vuex getters*”配方中使用的 Vue 项目，或者我们可以启动一个新的项目。

要启动一个新的项目，打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows）并执行以下命令：

```js
> vue create vuex-store
```

选择手动功能，并根据“*如何做...*”部分和“*创建一个简单的 Vuex 存储*”配方中指示的要求，添加`Router`和`Vuex`作为必需的功能。

现在按照以下步骤创建 Vuex 操作：

1.  在`src/store/user`文件夹中创建一个名为`actions.js`的新文件。

1.  从`fetchApi`包装器中导入变异类型文件（`types.js`）和`getHttp`，`patchHttp`，`postHttp`和`deleteHttp`函数：

```js
import {
  getHttp,
  patchHttp,
  deleteHttp,
  postHttp,
} from '@/http/fetchApi';
import MT from './types';
```

1.  创建一个名为`createUser`的新的`异步`函数，它接收解构的 JavaScript 对象作为第一个参数，其中包含`commit`属性，并将`userData`作为第二个参数，用于创建用户。添加一个`try/catch`语句，在`try`上下文中。首先，我们执行`commit(MT.LOADING)`，然后我们从 API 中获取用户列表，最后，执行`commit(MT.SET_USER_DATA, data)`，将用户列表传递给被突变。如果我们收到异常并进入`Catch`语句，我们将执行`commit(MT.ERROR, error)`，将收到的错误传递给`state`：

```js
async function createUser({ commit }, userData) {
  try {
 commit(MT.LOADING);
  await postHttp(`/api/users`, {
  data: {
  ...userData,
  }
 });
  commit(MT.SET_USER_DATA, userData);
  } catch (error) {
 commit(MT.ERROR, error);
  } }
```

1.  创建一个名为`fetchUsersList`的新的`异步`函数，它接收一个解构的 JavaScript 对象作为第一个参数，其中包含`commit`属性。在`try`上下文中添加一个`try/catch`语句。我们执行`commit(MT.LOADING)`，然后从 API 中获取用户列表，最后执行`commit(MT.SET_USER_LIST, data)`，将用户列表传递给 mutation。如果我们收到异常并进入`catch`语句，我们将执行一个`commit(MT.ERROR, error)`的 mutation，将收到的错误传递给`state`。

```js
async function fetchUsersList({ commit }) {
  try {
  commit(MT.LOADING);
    const { data } = await getHttp(`api/users`);
    commit(MT.SET_USER_LIST, data);
  } catch (error) {
  commit(MT.ERROR, error);
  } }
```

1.  创建一个名为`fetchUsersData`的新的`异步`函数，它接收一个解构的 JavaScript 对象作为第一个参数，其中包含`commit`属性，以及作为第二个参数的将要获取的`userId`。在`try`上下文中添加一个`try/catch`语句。我们执行`commit(MT.LOADING)`，然后从 API 中获取用户列表，最后执行`commit(MT.SET_USER_DATA, data)`，将用户列表传递给 mutation。如果我们收到异常并进入`catch`语句，我们将执行一个`commit(MT.ERROR, error)`的 mutation，将收到的错误传递给`state`。

```js
async function fetchUserData({ commit }, userId) {
  try {
 commit(MT.LOADING);
  const { data } = await getHttp(`api/users/${userId}`);
  commit(MT.SET_USER_DATA, data);
  } catch (error) {
 commit(MT.ERROR, error);
  } }
```

1.  创建一个名为`updateUser`的新的`异步`函数，它接收一个解构的 JavaScript 对象作为第一个参数，其中包含`commit`属性，以及作为第二个参数的`payload`。在`try`上下文中添加一个`try/catch`语句。我们执行`commit(MT.LOADING)`，然后将用户数据提交到 API，最后执行`commit(MT.UPDATE_USER, payload)`，将新的用户数据传递给 mutation。如果我们收到异常并进入`catch`语句，我们将执行一个`commit(MT.ERROR, error)`的 mutation，将收到的错误传递给`state`。

```js
async function updateUser({ commit }, payload) {
  try {
  commit(MT.LOADING);
    await patchHttp(`api/users/${payload.id}`, {
  data: {
  ...payload,
      }
 });
    commit(MT.UPDATE_USER, payload);
  } catch (error) {
  commit(MT.ERROR, error);
  } }
```

1.  创建一个名为`removeUser`的新的`异步`函数，它接收一个解构的 JavaScript 对象作为第一个参数，其中包含`commit`属性，以及作为第二个参数的`userId`。在`try`上下文中添加一个`try/catch`语句。我们执行`commit(MT.LOADING)`，然后从 API 中删除用户数据，最后执行`commit(MT.REMOVE_USER, userId)`，将`userId`传递给 mutation。如果我们收到异常并进入`catch`语句，我们将执行一个`commit(MT.ERROR, error)`的 mutation，将收到的错误传递给`state`。

```js
async function removeUser({ commit }, userId) {
  try {
  commit(MT.LOADING);
    await deleteHttp(`api/users/${userId}`);
    commit(MT.REMOVE_USER, userId);
  } catch (error) {
  commit(MT.ERROR, error);
  } } 
```

1.  最后，我们将创建一个默认导出的 JavaScript 对象，其中包含所有创建的函数作为属性：

```js
export default {
  createUser,
  fetchUsersList,
  fetchUserData,
  updateUser,
  removeUser, }   
```

1.  在`src/store/user`文件夹的`index.js`中导入新创建的`actions.js`文件，并将其添加到`export default` JavaScript 对象中：

```js
import state from './state';
import mutations from './mutations';
import getters from './getters';
import actions from './actions';

export default {
  state,
  mutations,
  getters,
  actions,
};
```

## 它是如何工作的...

操作是所有 Vuex 生命周期更改的初始化程序。当分发时，操作可以执行一个 mutation commit，或者另一个操作 dispatch，甚至是对服务器的 API 调用。

在我们的情况下，我们将我们的 API 调用放在了 actions 中，因此当异步函数返回时，我们可以执行 commit 并将状态设置为函数的结果。

## 另请参阅

在[`vuex.vuejs.org/guide/actions.html`](https://vuex.vuejs.org/guide/actions.html)找到有关 Vuex 操作的更多信息。

# 使用 Vuex 创建动态组件

将 Vuex 与 Vue 组件结合使用，可以在多个组件之间实现响应性，而无需直接进行父子通信，并分担组件的责任。

使用这种方法允许开发人员增强应用程序的规模，无需将数据状态存储在组件本身，而是使用单一真相作为整个应用程序的存储。

在这个配方中，我们将使用最后的配方来改进一个应用程序，其中使用了父子通信，并将其作为整个应用程序中可用的单一真相。

## 准备就绪

这个配方的先决条件如下：

+   Node.js 12+

所需的 Node.js 全局对象如下：

+   `@vue/cli`

+   `@vue/cli-service-global`

## 如何做...

要创建我们的动态组件，我们将把组件从有状态转换为无状态，并提取一些可以制作成新组件的部分。

我们将使用在“*创建和理解 Vuex 操作*”配方中使用的 Vue 项目与 Vue-CLI，或者我们可以开始一个新的项目。

要开始一个新的，打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows），并执行以下命令：

```js
> vue create vuex-store
```

选择手动功能，并根据“*如何做...*”部分中“创建简单的 Vuex 存储”配方中的指示添加`Router`和`Vuex`作为所需功能。

我们的配方将分为五个部分：

+   创建用户列表组件

+   编辑用户列表页面

+   编辑用户视图页面

+   编辑用户视图页面

+   编辑用户创建页面

让我们开始吧。

### 创建用户列表组件

因为 Vuex 给了我们在应用程序中拥有单一数据源的能力，我们可以为我们的应用程序创建一个新的组件，该组件将处理用户列表并触发从服务器获取用户列表的 Vuex 操作。这个组件可以是无状态的，并且可以自行执行`Vuex`操作。

#### 单文件组件`<script>`部分

让我们编写单文件组件的`<script>`部分：

1.  在`src/components`文件夹中创建一个名为`userList.vue`的新文件。

1.  从`src/mixin`文件夹导入`changeRouterMixin`：

```js
import changeRouteMixin from '@/mixin/changeRoute'; 
```

1.  创建一个`export default`的 JavaScript 对象，并添加一个名为`mixin`的新 Vue 属性，其默认值为一个数组。将导入的`changeRouteMixin`添加到这个数组中：

```js
mixins: [changeRouteMixin],
```

1.  创建一个名为`computed`的新 Vue 属性。在这个属性中，创建一个名为`userList`的新值。这个属性将是一个返回 Vuex 存储器 getter`getUsersList`的函数：

```js
computed: {
  userList() {
  return this.$store.getters.getUsersList;
  }, },  
```

#### 单文件组件`<template>`部分

在这里，我们将编写单文件组件的`<template>`部分：

1.  打开`views`文件夹内`users`文件夹中的`List.vue`文件，并复制`VsTable`组件的内容和组件。

1.  打开`src/components`文件夹中的`userList.vue`文件。

1.  将你从`List.vue`文件中复制的内容粘贴到`<template>`部分中：

```js
<template>
 <vs-table
  :data="userList"
  search
 stripe pagination max-items="10"
  style="width: 100%; padding: 20px;"
  >
 <template slot="thead">
 <vs-th sort-key="name">
  #
      </vs-th>
 <vs-th sort-key="name">
  Name
      </vs-th>
 <vs-th sort-key="email">
  Email
      </vs-th>
 <vs-th sort-key="country">
  Country
      </vs-th>
 <vs-th sort-key="phone">
  Phone
      </vs-th>
 <vs-th sort-key="Birthday">
  Birthday
      </vs-th>
 <vs-th>
  Actions
      </vs-th>
 </template>
 <template slot-scope="{data}">
 <vs-tr :key="index" v-for="(tr, index) in data">
 <vs-td :data="data[index].id">
  {{data[index].id}}
        </vs-td>
 <vs-td :data="data[index].name">
  {{data[index].name}}
        </vs-td>
 <vs-td :data="data[index].email">
 <a :href="`mailto:${data[index].email}`">
  {{data[index].email}}
          </a>
 </vs-td>
 <vs-td :data="data[index].country">
  {{data[index].country}}
        </vs-td>
 <vs-td :data="data[index].phone">
  {{data[index].phone}}
        </vs-td>
 <vs-td :data="data[index].birthday">
  {{data[index].birthday}}
        </vs-td>
 <vs-td :data="data[index].id">
 <vs-button
  color="primary"
  type="filled"
  icon="remove_red_eye"
  size="small"
  @click="changeRoute('view', data[index].id)"
  />
 <vs-button
  color="success"
  type="filled"
  icon="edit"
  size="small"
  @click="changeRoute('edit', data[index].id)"
  />
 <vs-button
  color="danger"
  type="filled"
  icon="delete"
  size="small"
  @click="deleteUser(data[index].id)"
  />
 </vs-td>
 </vs-tr>
 </template>
 </vs-table> </template>  
```

### 编辑用户列表页面

现在我们已经将用户列表提取到一个新的组件中，我们需要导入这个组件并移除旧的 VsTable，它使我们的视图混乱。

#### 单文件组件`<script>`部分

在这一步中，我们将编写单文件组件的`<script>`部分：

1.  打开`views`文件夹内`users`文件夹中的`List.vue`文件。

1.  从`components`文件夹导入新创建的用户列表组件：

```js
import changeRouteMixin from '@/mixin/changeRoute'; import UserTableList from '@/components/userList';
```

1.  在`export default`的 JavaScript 对象中，添加一个名为`components`的新属性。将该属性声明为 JavaScript 对象，并将导入的`UserTableList`组件添加到对象中：

```js
components: { UserTableList },
```

1.  在`methods`属性中，在`getAllUsers`函数中，我们需要更改内容以在调用时执行一个 Vuex 分发。这个方法将执行`fetchUsersList`的 Vuex 操作：

```js
async getAllUsers() {
  this.$store.dispatch('fetchUsersList'); },
```

1.  最后，在`deleteUser`函数中，我们需要更改内容以在调用时执行一个 Vuex 分发。这个方法将执行`removeUser`的 Vuex 操作，并将`userId`作为参数传递：

```js
async deleteUser(id) {
  this.$store.dispatch('removeUser', id);
  await this.getAllUsers(); },
```

#### 单文件组件`<template>`部分

让我们编写单文件组件的`<template>`部分：

1.  在`view`文件夹内的`users`文件夹中打开`List.vue`文件。

1.  用新导入的`UserTableList`替换`VsTable`组件及其内容：

```js
<vs-col
  vs-type="flex"
  vs-justify="left"
  vs-align="left"
  vs-w="12">
 <user-table-list /> </vs-col>  
```

### 编辑用户视图页面

现在我们可以将 Vuex 添加到用户视图页面。我们将添加 Vuex 操作和获取器来操作数据，并从页面中提取管理责任。

#### 单文件组件的`<script>`部分

现在你要创建单文件组件的`<script>`部分：

1.  从`view`文件夹内的`users`文件夹中打开`View.vue`文件。

1.  删除 Vue 的`data`属性。

1.  在 Vue 的`computed`属性中，添加`userData`，返回一个 Vuex 的 getter，`getUserData`：

```js
userData() {
  return this.$store.getters.getUserData; },
```

1.  最后，在`getUserById`方法中，将内容更改为调度一个 Vuex 操作`fetchUserData`，传递计算的`userId`属性作为参数：

```js
async getUserById() {
  await this.$store.dispatch('fetchUserData', this.userId); },
```

#### 单文件组件的`<template>`部分

是时候编写单文件组件的`<template>`部分了：

1.  在`view`文件夹内的`users`文件夹中打开`View.vue`文件。

1.  在 UserForm 组件中，将`v-model`指令更改为`:value`指令：

```js
<user-form
  :value="userData"
  disabled />
```

当使用只读值，或者需要删除`v-model`指令的语法糖时，可以将输入值声明为`:value`指令，并将值更改事件声明为`@input`事件监听器。

### 编辑用户编辑页面

我们需要编辑我们的用户。在上一个示例中，我们使用了一个有状态的页面，并在页面内执行了所有操作。我们将状态转换为临时状态，并在 Vuex 操作上执行 API 调用。

#### 单文件组件的`<script>`部分

在这里，我们将创建单文件组件的`<script>`部分：

1.  在`view`文件夹内的`users`文件夹中打开`Edit.vue`文件。

1.  在 Vue 的`data`属性中，将数据的名称从`userData`更改为`tmpUserData`：

```js
data: () => ({
  tmpUserData: {
  name: '',
  email: '',
  birthday: '',
  country: '',
  phone: '',
  }, }),
```

1.  在 Vue 的`computed`属性中，添加一个名为`userData`的新属性，它将返回 Vuex 的 getter`getUserData`：

```js
userData() {
  return this.$store.getters.getUserData; }
```

1.  添加一个名为`watch`的新 Vue 属性，并添加一个名为`userData`的新属性，它将是一个 JavaScript 对象。在这个对象中，添加三个属性，`handler`，`immediate`和`deep`。`handler`属性将是一个接收名为`newData`的参数的函数，它将`tmpUserData`设置为这个参数。`immediate`和`deep`属性都是设置为`true`的布尔属性：

```js
watch: {
  userData: {
  handler(newData) {
  this.tmpUserData = newData;
  },
  immediate: true,
  deep: true,
  } },
```

1.  在 Vue 的`methods`属性中，我们需要更改`getUserById`的内容以调度名为`fetchUserData`的 Vuex 动作，并将`computed`属性`userId`作为参数传递：

```js
async getUserById() {
  await this.$store.dispatch('fetchUserData', this.userId); },
```

1.  在`updateUser`方法中，更改内容以调度名为`updateUser`的 Vuex 动作，并将`tmpUserData`作为参数传递：

```js
async updateUser() {
  await this.$store.dispatch('updateUser', this.tmpUserData);
  this.changeRoute('list'); },
```

#### 单文件组件<template>部分

在这部分，我们将编写单文件组件的`<template>`部分：

1.  在`view`文件夹内的`users`文件夹中打开`Edit.vue`。

1.  将`UserForm`组件的`v-model`指令的目标更改为`tmpUserData`：

```js
<vs-col
  vs-type="flex"
  vs-justify="left"
  vs-align="left"
  vs-w="12"
  style="margin: 20px" >
 <user-form
  v-model="tmpUserData"
  /> </vs-col>
```

### 编辑用户创建页面

对于用户创建页面，更改将是最小的，因为它只执行 API 调用。我们需要添加 Vuex 动作调度。

#### 单文件组件<script>部分

在这里，我们将创建单文件组件的`<script>`部分：

1.  在`view`文件夹内的`users`文件夹中打开`Create.vue`文件。

1.  更改`createUser`方法的内容以调度名为`createUser`的 Vuex 动作，并将`userData`作为参数传递：

```js
async createUser() {
  await this.$store.dispatch('createUser', this.userData);
  this.changeRoute('list'); },  
```

## 它是如何工作的...

在所有四个页面中，我们进行了更改，将业务逻辑或 API 调用从页面中移除到 Vuex 存储，并尝试使其对于数据的维护责任更少。

因此，我们可以将一段代码放入一个新组件中，该组件可以放置在应用程序的任何位置，并且将显示当前用户列表，而不受实例化它的容器的任何限制。

这种模式有助于我们开发更突出的应用程序，其中需要的组件不那么业务导向，而更专注于它们的任务。

## 另请参阅

您可以在[`vuex.vuejs.org/guide/structure.html`](https://vuex.vuejs.org/guide/structure.html)找到有关 Vuex 应用程序结构的更多信息。

# 为开发添加热模块重载

**热模块重载**（**HMR**）是一种用于加快应用程序开发的技术，您无需刷新整个页面即可获取您刚刚在编辑器上更改的新代码。HMR 将仅更改和刷新您在编辑器上更新的部分。

在所有 Vue-CLI 项目或基于 Vue 的框架（如 Quasar Framework）中，HMR 存在于应用程序的呈现中。因此，每当您更改任何文件，该文件是 Vue 组件并且正在呈现时，应用程序将在运行时将旧代码替换为新代码。

在这个教程中，我们将学习如何向 Vuex 存储添加 HMR，并能够在不需要刷新整个应用程序的情况下更改 Vuex 存储。

## 准备工作

此教程的先决条件如下：

+   Node.js 12+

所需的 Node.js 全局对象如下：

+   `@vue/cli`

+   `@vue/cli-service-global`

## 如何做...

要启动我们的组件，我们将使用在“*使用 Vuex 创建动态组件*”中使用的 Vue 项目和 Vue-CLI，或者我们可以启动一个新的项目。

要启动一个新的项目，请打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows）并执行以下命令：

```js
> vue create vuex-store
```

选择手动功能，将`Router`和`Vuex`添加为所需功能，如“*如何做...*”部分和“*创建简单的 Vuex 存储*”教程中所示。

在接下来的步骤中，我们将向 Vuex 添加 HMR：

1.  打开`src/store`文件夹中的`index.js`文件。

1.  将`export default`转换为一个名为`store`的常量，并使其可导出：

```js
export const store = new Vuex.Store({
  ...UserStore, });
```

1.  检查 webpack `hot-module-reload`插件是否处于活动状态：

```js
if (module.hot) {}
```

1.  创建一个名为`hmr`的新常量，其中包含`user`文件夹中`index.js`，`getters.js`，`actions.js`和`mutations.js`文件的路径：

```js
const hmr = [
  './user',
  './user/getters',
  './user/actions',
  './user/mutations', ];
```

1.  创建一个名为`reloadCallback`的新函数。在这个函数中，创建三个常量`getters`，`actions`和`mutations`。每个常量将指向`user`文件夹中的等效文件，并调用`store.hotUpdate`函数，将一个对象作为参数传递，其中包含您创建的常量的值：

```js
const reloadCallback = () => {
  const getters = require('./user/getters').default;
  const actions = require('./user/actions').default;
  const mutations =  require('./user/mutations').default;    store.hotUpdate({
  getters,
  actions,
  mutations,
  }) };
```

由于文件的 Babel 输出，您需要在使用 webpack `require`函数动态导入的文件末尾添加`.default`。

1.  执行 webpack HMR 的`accept`函数，将`hmr`常量作为第一个参数传递，将`reloadCallback`作为第二个参数传递：

```js
module.hot.accept(hmr, reloadCallback);
```

1.  最后，默认导出创建的`store`：

```js
export default store;  
```

## 它是如何工作的...

Vuex 存储支持使用 webpack HMR 插件的 API 进行 HMR。

当它可用时，我们创建一个可能需要更新的文件列表，以便 webpack 可以意识到这些文件的任何更新。当这些文件中的任何一个被更新时，将执行您创建的特殊回调。这个回调是使 Vuex 能够完全更新或更改更新文件的行为的回调。

## 另请参阅

您可以在[`vuex.vuejs.org/guide/hot-reload.html`](https://vuex.vuejs.org/guide/hot-reload.html)找到有关 Vuex 热重载的更多信息。

您可以在 [`webpack.js.org/guides/hot-module-replacement/`](https://webpack.js.org/guides/hot-module-replacement/) 找到有关 webpack HMR 的更多信息。

# 创建一个 Vuex 模块

随着我们的应用程序的增长，在单个对象中工作可能非常危险。项目的可维护性和每次更改可能产生的风险都会变得更糟。

Vuex 有一种叫做模块的方法，可以帮助我们将存储分成不同的存储分支。这些分支或模块中的每一个都有不同的状态、变化、获取器和操作。这种模式有助于开发，并减少了向应用程序添加新功能的风险。

在这个教程中，我们将学习如何创建一个模块以及如何与之一起工作，将其分成专用分支。

## 准备工作

这个教程的先决条件如下：

+   Node.js 12+

所需的 Node.js 全局对象如下：

+   `@vue/cli`

+   `@vue/cli-service-global`

## 如何做...

要开始我们的组件，我们将使用在“*使用 Vuex 创建动态组件*”中使用的 Vue 项目和 Vue-CLI，或者我们可以开始一个新的项目。

要开始一个新的项目，打开终端（macOS 或 Linux）或命令提示符/PowerShell（Windows）并执行以下命令：

```js
> vue create vuex-store
```

选择手动功能并将 `Router` 和 `Vuex` 添加为必需功能，如“*如何做...*”部分和“*创建简单的 Vuex 存储*”教程中所示。

我们的教程将分为两个部分：

+   创建新的认证模块

+   向 Vuex 添加模块

让我们开始吧。

### 创建新的认证模块

首先，我们需要创建一个新的 `Vuex` 模块。这个示例模块将被称为 `authentication`，并将存储用户的凭据数据。

在这些步骤中，我们将为 `Vuex` 创建 `authentication` 模块：

1.  在 `src/store` 文件夹中创建一个名为 `authentication` 的新文件夹。

1.  在这个新创建的文件夹中，创建一个名为 `state.js` 的新文件，并打开它。

1.  创建一个名为 `generateState` 的函数，它将返回一个具有 `data.username`、`data.token`、`data.expiresAt`、`loading` 和 `error` 属性的 JavaScript 对象：

```js
const generateState = () => ({
  data: {
  username: '',
  token: '',
  expiresAt: null,
  },
  loading: false,
  error: null, });
```

1.  在文件末尾创建一个 `export default` 对象。这个对象将是一个 JavaScript 对象。我们将解构 `generateState` 函数的返回值：

```js
export default { ...generateState() };
```

1.  在 `src/store` 文件夹中的 `authentication` 文件夹中创建一个名为 `index.js` 的新文件，并打开它。

1.  导入新创建的 `state.js` 文件：

```js
import state from './state';
```

1.  在文件末尾创建一个`export default`对象。这个对象将是一个 JavaScript 对象。添加一个名为`namespaced`的新属性，其值设置为`true`，并添加导入的`state`：

```js
export default {
  namespaced: true,
  state, }; 
```

### 将模块添加到 Vuex

现在我们已经创建了我们的模块，我们将把它们添加到 Vuex 存储中。我们可以将新模块与旧代码集成在一起。这不是问题，因为 Vuex 将把新模块处理为一个命名空间对象，具有完全独立的 Vuex 存储。

现在，在这些步骤中，我们将把创建的模块添加到 Vuex 中：

1.  打开`src/store`文件夹中的`index.js`文件。

1.  从`authentication`文件夹中导入`index.js`文件：

```js
import Vue from 'vue'; import Vuex from 'vuex'; import UserStore from './user'; import Authentication from './authentication';
```

1.  在`Vuex.Store`函数中，添加一个名为`modules`的新属性，这是一个 JavaScript 对象。然后添加导入的`User`和`Authentication`模块：

```js
export default new Vuex.Store({
  ...UserStore,
  modules: {   Authentication,
  } })  
```

## 工作原理...

模块的工作方式类似于单一的 Vuex 存储，但在同一个 Vuex 单一的数据源中。这有助于开发更大规模的应用程序，因为你可以维护和处理更复杂的结构，而无需在同一个文件中检查问题。

与此同时，可以使用模块和普通的 Vuex 存储，从传统应用程序迁移，这样你就不必从头开始重写所有内容才能使用模块结构。

在我们的情况下，我们添加了一个名为`authentication`的新模块，只有一个状态存在于存储中，并继续使用旧的用户 Vuex 存储，这样将来我们可以将用户存储重构为一个新模块，并将其分离成更具体的、面向领域的架构。

## 另请参阅

您可以在[`vuex.vuejs.org/guide/modules.html`](https://vuex.vuejs.org/guide/modules.html)找到有关 Vuex 模块的更多信息。
